---
# 常用定义
title: "react路由缓存设计" # 标题
date: 2023-11-01 # 创建时间
draft: false # 是否是草稿？
tags: ["React", "react-activation"] # 标签
categories: ["React"] # 分类
author: "Carri" # 作者
keywords: ["React"]
description: "详解React函数组件"
# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true # 关闭评论
toc: true # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false # 关闭打赏
mathjax: true # 打开 mathjax
---

## 一、需求背景

在管理后台进行列表操作时，比如点击编辑进入编辑页面，再次回退，页面对应的列表默认都会回到第一页，无法保存最初的状态，**体验很差**。

为了改善用户体验，期望经过某种优化，在我们进入编辑页面编辑后，回退路由，可以直接显示进入编辑页前列表页的状态，相当于对列表页作缓存，下图可以表示现有的路由跳转流程和期望的路由跳转流程。

![缓存1](/images/cache/缓存1.png)

## 二、方案选型

三个课堂项目技术栈为React，由**react-router-dom**提供单页面路由能力，目前三个课堂项目的react-router-dom版本为5.2.0（当前最新版本为6.17.0。

相较于React, Vue官方提供了keep-alive组件实现组件缓存，当用户在页面之间切换时，这些页面将不会被刷新，并且它们的状态将被保留。

```
<keep-alive>
  <!-- page-component matched by the route will render here -->
  <router-view></router-view>
</keep-alive>
```

但是很遗憾React和react-router-dom并没有提供相关API，react作者Dan在issue（https://github.com/facebook/react/issues/12039）中提到不引入keep-alive功能的原因，作者Dan倾向于让React使用者手动缓存数据，避免缓存组件造成的内存不可控和生命周期混乱问题。所以为了实现这一特性，我们需要依赖社区实现的第三方轮子或者自己根据需要实现一个。三个课堂作为一个已经迭代3年的项目，功能较多，不适合太大的变动，因此期望本次所选方案满足以下特性：

- 提供状态缓存机制
- 低侵入性，即最好不要替换项目核心库（如react-router-dom）的版本或使用其他替代方案
- 灵活可控，可以根据需要，决定对应路由是否需要缓存
- 生命周期，实现路由缓存后，react原有的mount和unmount若失效，需要提供激活与失活状态的生命周期
- 状态可循，需要日志机制记录缓存状态
- 性能，避免缓存状态过多造成的卡顿问题，需要控制缓存

### 三种主流实现思路的对比：

| 对比 / 实现思路 | 保存组件状态                                              | 显隐组件                                                     | 缓存组件                                                     |
| --------------- | --------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 缓存机制        | 使用redux之类的store缓存组件挂载或卸载时需要的数据        | 使组件的卸载变更为组件的隐藏(display:none)，原本的重新挂载变更为隐藏组件的重新显示(display:block) | 在组件卸载时使用context缓存组件，原本的重新挂载变更为从context的缓存中读取组件 |
| 侵入性          | 需要更改组件逻辑，侵入性 ★★★★                             | 必须更改路由库的Switch和Route机制，会影响组件挂载卸载机制，侵入性★★★ | 提供额外的组件，会影响组件挂载卸载机制，侵入性★★             |
| 可控性          | 非常可控                                                  | 可控                                                         | 可控                                                         |
| 代码量          | 大，基本需要更改每个需要缓存的组件和子组件                | 一般                                                         | 一般                                                         |
| 生命周期        | 不引入额外的生命周期                                      | 需要提供额外的生命周期，同时挂载与卸载的生命周期在第二次渲染时不会生效 | 需要提供额外的生命周期，同时挂载与卸载的生命周期在第二次渲染时不会生效 |
| 状态可循        | 支持                                                      | 支持                                                         | 支持                                                         |
| 性能            | 会根据页面复杂程度缓存相应数量的数据，可以控制缓存数量★★★ | 被缓存组件的DOM依然存在于页面上，不好控制缓存数量★★          | 缓存组件，可以控制缓存的组件数量★★★                          |
| 其他            | -                                                         | 不可使用过过渡动画（不重要）                                 | -                                                            |

综合来看，缓存组件的实现思路更为合适，遇事不决排除法：

- 我们无法接受更改每一个要缓存的组件，这个数量是非常大的，因此排除**保存组件状态**的思路，尽管这是React作者Dan所推崇的。
- **显隐组件**的方式，必须要基于react-router修改，对我们原本的路由代码存在破坏性，较为不能接受。
- 其中在缓存组件的实现上，社区流行的[react-router-cache-route](https://github.com/CJY0208/react-router-cache-route)，基于react-router更改了Switch和Route，和路由库**强耦合**，而我们的目的只是缓存组件，不希望出现更改react-router带来的侵入性，所以在后面的方案中也不再考虑，另外还有基于react-router的[react-router-pro](https://github.com/aiyuekuang/react-router-pro)和[react-keeper](https://github.com/lanistor/react-keeper)，需要替换整个路由库，并且都也不维护了，issue无人解答，因此也不考虑。

### 缓存组件方案对比

我们期望缓存组件的方案与react-router是解耦的，在社区中找到了两个star数1K以上或左右的库满足条件：

| 对比 / 库   | **[react-keep-alive](https://github.com/StructureBuilder/react-keep-alive)** | **[react-activation](https://github.com/CJY0208/react-activation)** |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| GitHub Star | 900+                                                         | 1.6K+                                                        |
| npm 周下载  | 1400+                                                        | 6000+                                                        |
| 使用方式    | Provider组件：缓存组件KeepAlive组件：更改组件卸载与挂载机制  | 同react-keep-alive                                           |
| 可控        | 支持                                                         | 支持                                                         |
| 原理        | 基于React.createPortal实现                                   | 一种交换DOM的机制                                            |
| 问题        | 将children提到了顶层进行渲染，破坏了dom结构和冒泡机制        | -                                                            |

react-keep-alive破坏dom结构的问题会带来潜在的风险，侵入性过高，综合时间等因素最终考虑使用react-activation，并且react-activation项目也保持着不错的活跃度。

为了保证引入依赖后项目是可控的，阅读了react-activation的源代码，理解其缓存原理，以便进行后续的使用和改造

![缓存2](/images/cache/缓存2.png)

总结：被缓存的children组件通过keep方法，实际是作为AliveScope直接的子组件渲染，只是渲染后通过dom操作转移到了原本的位置，这样children组件被卸载时，只是dom结构被去除，React组件还是保存在AliveScope中。

使用react-activation后页面会被缓存，但在实际应用中我们并不希望任何场景下页面都被缓存，因此我们还要实现可控

### 三、实现可控

以我们的项目为例

存在三个路由：直播活动管理页A、编辑页面B和录播视频管理页C，缓存直播活动后，期望在**直播活动页**进行以下操作的表现：

- 点击编辑进入**编辑页面**，**编辑页面**编辑完成后跳转回**直播活动，**此时**直播活动**页码和筛选条件不变，列表更新
- 点击编辑进入**编辑页面**，**编辑页面**不编辑直接回推到直播活动，此时**直播活动**不刷新
- 点击进入**录播视频**，再点击或回退进入**直播活动**，此时**直播活动**刷新重置

总结：只有跳转指定页面时才缓存，并在返回指定页面的时候，可以控制是否刷新列表。

### 关系

结合react-activation对每一个要缓存的页面各自处理可以实现以上特性，但是这么处理会使整个应用内聚度不够，每个页面独立处理缺乏标准，为了避免这种情况，提升软件可维护性，期望实现一个**中央状态管理器**，负责整体的缓存控制。

关系图（直播活动A、录播视频B和编辑页面C，D为 从D跳到A时，需要缓存的页面，A为D的toPath，当从A跳到非D时，D的缓存被清除）：

![缓存3](/images/cache/缓存3.png)

### 实现编辑完成后刷新列表

从A页面跳转到B页面，B页面编辑完成后跳回A页面，A页面保持筛选条件和页码并刷新列表，可以通过react-activation的生命周期[useActivation](https://github.com/CJY0208/react-activation#lifecycle)结合store实现：

![缓存4](/images/cache/缓存4.png)

使用浏览器缓存(localStorage和sessionStorage)作为store模块，考虑到多Tab可能导致缓存混乱，使用sessionStorage。

### 缓存配置

缓存配置模块独立or与路由配置一起

| 对比/方式 | 与路由配置内聚                                               | 与路由配置解耦                                               |
| --------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 实现      | 在路由配置中添加新的keepAlivePaths字段，通过keepAlivePaths字段判断是否为需缓存路由，路由组件中包裹KeepAlive，使路由可以被缓存 | 新建独立的配置项，在需要的地方添加KeepAlive包裹，使其可以被缓存 |
| 优点      | 与路由关联，方便维护                                         | 不需要修改路由代码                                           |
| 缺点      | 三个课堂项目中的路由配置不是全部写在一起的，只有同一级的路由配置由一个配置对象配置 | 与路由配置有关联，但是独立出来，内聚度不够                   |

总结：对于新项目而言，倾向于与路由配置内聚，然而三个课堂项目中的路由配置文件太过分散了，如果多个文件都添加缓存相关的配置，从整体上看不容易理清各路由的缓存关系。

选择与路由配置解耦出来配置，这样可能会导致配置了错误的path，因此需要**提供一个检测方法检测路由**是否合法。

例对管理模块进行配置：

```typescript
const MANAGE_KEEP_ALIVE_ROUTE = [
	{ path: '/manage/my-lives', keepAlivePaths: ['/manage/lubo-courses'] },
	{ path: '/manage/my-research',keepAlivePaths:'all'},
    // ...
]
```

### 代码实现

react-activation提供了KeepAlive组件用于包裹缓存组件，AliveScope提供上下文，根据我们控制缓存的需要，可以通过**依赖注入模式**将所需逻辑添加到组件中，对外仅暴露KeepAliveWrapper和AliveScopeWrapper控制缓存、useActivateWithCb和setActivateCbParams用于控制，通过**工厂函数**初始化使用

1. KeepAlive注入缓存判断逻辑

```typescript
const KeepAliveWrapper = (keepAliveManager: KeepAliveManager, props: { children: React.FC }) => {
  const keepAliveRoutes = keepAliveManager.getKeepAliveRoutes();
  const location = useLocation();
  const curPath = location.pathname;
  const routeItem = keepAliveRoutes.find((item) => item.path === curPath);

  return routeItem ? (
    <KeepAlive id={curPath} name={curPath} cacheKey={curPath}>
      {props.children}
    </KeepAlive>
  ) : (
    props.children
  );
};
```

2. AliveScope注入缓存控制hooks，KeepAliveAutoControlInject中通过hooks去进行缓存控制

```typescript
const AliveScopeWrapper = (keepAliveManager: KeepAliveManager, props: { children: React.ReactNode }) => {
  return (
    <AliveScope>
      <KeepAliveAutoControlInject keepAliveManager={keepAliveManager}/>
    </AliveScope>
  );
};
```

3. 通过工厂模式使每个模块（如manage）额外拥有一个缓存配置，这么处理的原因是考虑到三个课堂不同模块无法共享上下文的问题。不过为了避免之后仍然出现三个课堂项目中原本类似的路由配置分散问题，需要对所有模块进行统一的初始化和导出。

```typescript
const createKeepAliveFactory = (keepAliveRoutes: KeepAliveRoute[]) => {
  const keepAliveManager = new KeepAliveManager(keepAliveRoutes);

  return {
    AliveScopeWrapper: AliveScopeWrapper.bind(null, keepAliveManager) as React.FC<{ children: React.ReactNode }>,
    KeepAliveWrapper: KeepAliveWrapper.bind(null, keepAliveManager) as React.FC<{ children: React.ReactNode }>,
    useActivateWithCb: useActivateWithCb.bind(null, keepAliveManager),
    setActivateCbParams: setActivateCbParams.bind(null, keepAliveManager),
  };
};
```

优化后回退如丝般顺滑
