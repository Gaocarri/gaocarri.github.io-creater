---
# 常用定义
title: "Tags组件的封装以及思路"          # 标题
date: 2020-07-28   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架","TypeScript"]  # 标签
categories: ["黑曜石记账项目笔记"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架","JS库"]
description: "Tags组件的封装以及思路"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

# Tags.vue的封装

1. Tags.vue拥有的组件

```
  components: {
  	Scroll,
    TagsNav,
    TagSelected,
    TagList
  }
```

2. TagsNav的封装
   - 无难点，注意判断完成的点击状态，以及complete方法传入新添的tag即可

```
<template>
  <header>
    <div class="back" @click="back">
      <Icon name="back" />
    </div>
    <span>添加支出类别</span>
    <span class="complete" :class="{'complete-clicked':clicked}" @click="complete">完成</span>
  </header>
</template>
```

3. TagSelected的封装
   - 无难点，只需从Tags.vue中传入选中的tag即可

```
   @Prop({ default: { name: "餐饮", id: 1 }, type: Object }) selectedTag?: Tag;
```

   

4. TagsList的封装

- 这里需要从Tags.vue中传入tagList和selectTag从而正确的显示
- 点击Icon后会得到一个selectTag从而给其添加样式
- 通过判断当前选中的tag的id是否与当前Icon的id一致来判断是否添加class

```
<ul class="icons">
      <li v-for="tag in tagList" :key="tag.id">
        <Icon
          class="icon"
          :name="tag.name"
          @click.native="selectTag(tag)"
          :class="{'selected':selectedTag.id===tag.id}"
        />
        <span>{{tag.name}}</span>
</li>

@Component
export default class TagList extends Vue {
  @Prop() readonly tagList!: Tag[];
  @Prop() selectedTag?: Tag;

  selectTag(tag: Tag) {
    this.$emit("selectTag", tag);
  }
}
```


# Tags.vue的selected方法（遇到的坑）

```
  selectTag(tag: Tag) {
    this.selectedTag = tag;
  } // 正确的写法，会同步更新
```

```
  selectTag(tag: Tag) {
    this.selectedTag.name = tag.name
    this.selectedTag.id = tag.id
  }// 错误的写法，selectedTag不会同步更新
```

原因：以这种方式修改对象属性不会自动更新
