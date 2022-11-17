---
# 常用定义
title: "Money组件的封装及思路"          # 标题
date: 2020-08-01   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架","TypeScript"]  # 标签
categories: ["黑曜石记账项目笔记"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架","JS库"]
description: "Money组件的封装及思路"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

## Money.vue的封装

1. 封装MoneyHead.vue,MoneyList.vue和MoneyContent.vue并引入Money

```
   <template>
     <div>
       <Layout>
         <money-head />
         <money-list />
         <money-content />
       </Layout>
     </div>
   </template>
```



2. MoneyHead的封装

- 下拉选择日期

  - 使用year和month保存选中的年份，使用years和month保存选项们

  ```
   <select name="year" v-model="year" class="year">
          <option v-for="y in years" :value="y" :key="y">{{y}}年</option>
   </select>
  ```

  - filter函数获取当前日期的recordList的amount，这里以结余为例

  ```
    get monthBalance() {
      // 支出
      const expendRecordList = this.recordList.filter((i: RecordItem) => {
        return (
          dayjs(i.createdAt)
            .year()
            .toString() === this.year &&
          (dayjs(i.createdAt).month() + 1).toString() === this.month &&
          i.type === "-"
        );
      });
      // 收入
      const includeRecordList = this.recordList.filter((i: RecordItem) => {
        return (
          dayjs(i.createdAt)
            .year()
            .toString() === this.year &&
          (dayjs(i.createdAt).month() + 1).toString() === this.month &&
          i.type === "+"
        );
      });
      let balance = 0;
      for (let i = 0; i < expendRecordList.length; i++) {
        balance -= expendRecordList[i].amount;
      }
      for (let i = 0; i < includeRecordList.length; i++) {
        balance += includeRecordList[i].amount;
      }
      // 保留两位小数
      return balance.toFixed(2);
    }
  ```

  

## 展示每个日期支出收入

1. 这里需要的就是将同一日期的recordItem存到一起，所以同样需要引入dayjs对日期进行处理
2. 使用计算属性获得所需数据

```typescript
// 数据
  get dataList() {
    let dataList = [];
    console.log(this.$store.state.recordList);
    for (let i = 0; i < this.$store.state.recordList.length; i++) {
      const data = {
        y: 0,
        m: 0,
        d: 0,
        record: []
      } as Data;
      data.y = dayjs(this.$store.state.recordList[i].createdAt).year();
      data.m = dayjs(this.$store.state.recordList[i].createdAt).month() + 1;
      data.d = dayjs(this.$store.state.recordList[i].createdAt).date();

      // 判断日期是否为同一天
      if (i == 0) {
        // 存入recordItem
        data.record.push(this.$store.state.recordList[0]);
        dataList.push(data);
      } else if (
        // 日期不重复的情况
        data.y != dayjs(this.$store.state.recordList[i - 1].createdAt).year() ||
        data.m !=
          dayjs(this.$store.state.recordList[i - 1].createdAt).month() + 1 ||
        data.d != dayjs(this.$store.state.recordList[i - 1].createdAt).date()
      ) {
        // 存入recordItem
        data.record.push(this.$store.state.recordList[i]);
        dataList.push(data);
      } else {
        dataList[dataList.length - 1].record.push(
          this.$store.state.recordList[i]
        );
      }
    }
    return dataList;
  }
```

3. 对当日收入和支出进行一次计算

```typescript
// 获取当日支出
  getExpend(data: Data) {
    let expend = 0;
    for (let i = 0; i < data.record.length; i++) {
      if (data.record[i].type === "-") {
        expend += data.record[i].amount;
      }
    }
    return expend.toFixed(2);
  }
  // 获取当日收入
  getInclude(data: Data) {
    let include = 0;
    for (let i = 0; i < data.record.length; i++) {
      if (data.record[i].type === "+") {
        include += data.record[i].amount;
      }
    }
    return include.toFixed(2);
  }
```

4. 如何展示首页最上方年月的记录

- 首先需要在store中记录当前的年月（需要引入dayjs）
- 在MoneyHead中的select，监听年月的变化从而commit更改store的当前年月
- 在MoneyList使用filter函数获得当前年月的记录

```typescript
  // 获取首页展示的年月
  get year() {
    return this.$store.state.currentYear;
  }
  get month() {
    return this.$store.state.currentMonth;
  }
  get currentRecordList() {
    const currentRecordList = this.$store.state.recordList.filter(
      (item: RecordItem) => {
        return (
          dayjs(item.createdAt).year() == this.year &&
          dayjs(item.createdAt).month() + 1 == this.month
        );
      }
    );
    return currentRecordList;
  }
```

- 另外使用currentYear和currentMoney获取当前记录的长度，在Money.vue中利用v-if以获得要显示的内容

```
      <Scroll class="content">
        <money-list v-if="length>0" />
        <money-blank v-else />
      </Scroll>
```
