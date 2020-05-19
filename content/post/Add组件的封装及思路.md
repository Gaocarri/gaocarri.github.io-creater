---
# 常用定义
title: "Add组件的封装及思路"          # 标题
date: 2020-03-27   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架","TypeScript"]  # 标签
categories: ["黑曜石记账项目笔记"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架","JS库"]
description: "Add组件的封装及思路"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

## Add.vue的封装

1. Add.vue的组件

```
@Component({
  components: {
    Scroll,
    TabBar,
    NumberPad,
    TagTable
  }
})
```

2. 在Add.vue中获取tagList,经过详细考虑我发现使用计算属性和watch能更好的获取tagList和当前选择标签的id

```
export default class Add extends Vue {
  selectedId: number = 0;
  type: string = "-";
  buttonSelected: boolean = false;

  get tagList() {
    if (this.type === "-") {
      return this.$store.state.tagList;
    } else if (this.type === "+") {
      return expendList;
    }
  }

  @Watch("tagList")
  onTagListChanged(newVal: Tag) {
    this.selectedId = this.tagList[0] ? this.tagList[0].id : 0;
  }

```

3. 封装一个TagTable.vue组件根据传过来的TagList

```
<template>
  <div class="tag-list">
    <header>{{tagList[0].name}}</header>
    <ul class="icons">
      <li v-for="tag in tagList" :key="tag.id">
        <Icon
          class="icon"
          :name="tag.name"
          :class="{'selected':selectedTag.id===tag.id}"
        />
        <span>{{tag.name}}</span>
      </li>
    </ul>
  </div>
</template>

<script lang='ts'>
import Vue from "vue";
import { Component, Prop } from "vue-property-decorator";

@Component
export default class TagList extends Vue {
  @Prop() readonly tagList!: Tag[];
}
```


4. 封装NumberPad来记录金额和备注

- 点加ok时进行数组操作 来进行加法运算

```
 ok() {
    const addArray = this.output.split("+");
    this.output = "0";
    for (let i = 0; i < addArray.length; i++) {
      if (addArray[i] === "") return;
      this.output = (
        parseFloat(this.output) + parseFloat(addArray[i])
      ).toString();
    }

    this.$emit("createRecord", this.output, this.notes);
    this.notes = "";
    this.output = "0";
  }
```
## 数据记录

1. 在Add.vue中使用record数组来存储每次添加的记录

```
  record: RecordItem = {
    tag: {},
    notes: "",
    type: "-",
    amount: 0
  };
  
  type RecordItem = {
  tag: Tag | {};
  notes: string;
  type: string;
  amount: number;
  createdAt?: string;
}
```


2. 在store里面封装recordList的方法

```
    // recordList方法
    fetchRecords(state) {
      state.recordList = JSON.parse(window.localStorage.getItem('recordList') || '[]') as RecordItem[];
    },
    createRecord(state, record) {
      const record2: RecordItem = clone(record);
      record2.createdAt = new Date().toISOString();
      state.recordList.push(record2);
      store.commit('saveRecords')
    },
    saveRecords(state) {
      window.localStorage.setItem('recordList', JSON.stringify(state.recordList));
    },
```

