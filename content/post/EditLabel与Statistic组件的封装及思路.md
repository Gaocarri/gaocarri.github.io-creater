---
# 常用定义
title: "EditLabel与Statistic组件的封装及思路"          # 标题
date: 2020-04-07   # 创建时间
draft: false                       # 是否是草稿？
tags: ["Vue","框架","TypeScript"]  # 标签
categories: ["黑曜石记账项目笔记"]              # 分类
author: "Carri"                  # 作者
keywords: ["Vue","框架","JS库"]
description: "EditLabel与Statistic组件的封装及思路"  

# 用户自定义
# 你可以选择 关闭(false) 或者 打开(true) 以下选项
comment: true   # 关闭评论
toc: true       # 关闭文章目录
# 你同样可以自定义文章的版权规则
contentCopyright: '<a rel="license noopener" href="https://creativecommons.org/licenses/by-nc-nd/4.0/" target="_blank">CC BY-NC-ND 4.0</a>'
reward: false	 # 关闭打赏
mathjax: true    # 打开 mathjax
---

## EditLabel页面

1. 困扰我一个非常久的Bug,input表单输入数字结果不为数字

   正确的解决办法

   ```
   <input type="text" v-model.number.trim="recordItem.amount" />
   ```

   - 其实就是在v-model后面加上.number和.trim修饰符
   - 两者缺一不可

## Statistics界面

1. 如何切换年月日以及支出收入

- 子组件中

```typescript
  <ul>
    <li @click="selectYmw('week')" :class="{'selected':ymw=='week'}">周</li>
    <li class="middle-li" @click="selectYmw('month')" :class="{'selected':ymw=='month'}">月</li>
    <li @click="selectYmw('year')" :class="{'selected':ymw=='year'}">年</li>
  </ul>

  selectYmw(ymw: string) {
    if (this.ymw !== ymw) {
      this.ymw = ymw;
      this.$emit("selectYmw", ymw);
    }
  }
```

- 父组件中

```vue
<statistics-date @selectYmw="selectYmw" />
<Scroll class="content"></Scroll>
      
  selectYmw(ymw: string) {
    this.ymw = ymw;
  }
```

- 这样就可以在首页获取所对应的年月日和支出收入了

2. Vue+Ts使用echarts

```
<template>
  <div id="myEcharts" style="width:100vw ;height:40vh;"></div>
</template>

<script lang="ts">
import Vue from "vue";
import { Component } from "vue-property-decorator";
import "echarts/lib/chart/bar";

@Component
export default class StatisticsChart extends Vue {
  mounted() {
    const ele = document.getElementById("myEcharts");
    const chart: any = this.$echarts.init(ele);
    chart.setOption(this.options, true, false);
  }
  $echarts: any;
get options() {
    return {
      xAxis: {
        boundaryGap: false, // 两端间隔
        splitLine: {
          show: false // 每个端点的折线
        },
        type: "category", // 类目轴
        axisTick: {
          show: false
        },
        data: this.x // 横轴刻度名称
      },
      yAxis: {
        show: false,
        type: "value"
      },
      series: [
        {
          data: this.y, //这里我的数据对应月份的数据,请自行修改
          itemStyle: {
            normal: {
              label: {
                show: false,
                position: "top"
              }
            }
          },
          type: "line",
          stack: "总量",
          // 径向渐变，前三个参数分别是圆心 x, y 和半径，取值同线性渐变
          color: "#212943"
        }
      ]
    };
  }
}
</script>

<style lang="scss" scoped>
</style>
```

在动态渲染图表的时候我给组件声明了一个 Porp 从外部拿数据去实现**及时更新**，但是在外部数据改变的时候图表数据并没有更新

后来我以为是 Vue 的**钩子时机问题**，试了很久问了其他人都没有结果，后来就想另辟蹊径看看是不是 **ECharts 的问题**，没想到去网上一搜还有不少人和我遇到了同样的问题

原来不是 Vue 的错，而是 ECharts **默认渲染一次后就不会及时更新数据了，需要设置参数来唤醒它的及时更新**



```text
chart.setOption(option, notMerge, lazyUpdate);
```

问题的根源就在这句代码上

- **option**： 是我们自己配置的 options
- **notMerge（导致不能及时更新的关键参数）**：可选，这个参数意思就是当数据变化的时候，是否**不**跟之前设置的数据合并，这个参数默认为 **false**，也就是合并，把它改成 **true**，然后它就不会默认合并之前的数据了
- **lazyUpdate**：可选，在设置完`option`后是否不立即更新图表，默认为**false**，即立即更新，这个设置 **false** 就行

- 设置watch监听变化

```
  @Watch("ymw")
  changeX() {
    // 获取当前月份天数
    const day = dayjs().daysInMonth();
    let dayArray = [];
    for (let i = 1; i <= day; i++) {
      dayArray.push(i);
    }
    let data: string[] = [];
    switch (this.ymw) {
      case "week":
        data = ["周一", "周二", "周三", "周四", "周五", "周六", "周日"];
        break;
      case "month":
        data = dayArray.map(i => i + "日");
        break;
      case "year":
        data = [
          "一月",
          "二月",
          "三月",
          "四月",
          "五月",
          "六月",
          "七月",
          "八月",
          "九月",
          "十月",
          "十一月",
          "十二月"
        ];
        break;
      default:
        data = ["周一", "周二", "周三", "周四", "周五", "周六", "周日"];
    }
    this.x = data;
    const ele = document.getElementById("myEcharts");
    const chart: any = this.$echarts.init(ele);
    chart.setOption(this.options, true, false);
  }
```

3. 动态获取年月日数据

```
getTotal() {
    let data: any[] = [];
    const recordList = this.$store.state.recordList;

    if (this.ymw == "week") {
      // 当前选择为周
      data = [0, 0, 0, 0, 0, 0, 0];
      const startDay = dayjs().startOf("week");
      const record = recordList.filter((item: RecordItem) => {
        return dayjs(item.createdAt) >= startDay && this.type == item.type;
      });
      for (let i = 0; i < 7; i++) {
        for (let j = 0; j < record.length; j++) {
          if (i == dayjs(record[j].createdAt).day()) {
            data[i] += record[j].amount;
          }
        }
      }
    } else if (this.ymw == "month") {
      // 获取当前月
      const day = dayjs().daysInMonth();
      for (let i = 0; i < day; i++) {
        data.push(0);
      }
      const record = recordList.filter((item: RecordItem) => {
        return (
          dayjs().month() == dayjs(item.createdAt).month() &&
          this.type == item.type
        );
      });
      for (let i = 0; i < day; i++) {
        for (let j = 0; j < record.length; j++) {
          if (i == dayjs(record[j].createdAt).date()) {
            data[i] += record[j].amount;
          }
        }
      }
    } else if (this.ymw == "year") {
      // 当前的选择是年
      const year = dayjs().year();
      for (let i = 0; i < 12; i++) {
        data.push(0);
      }
      const record = recordList.filter((item: RecordItem) => {
        return (
          dayjs().year() == dayjs(item.createdAt).year() &&
          this.type == item.type
        );
      });
      console.log(record);
      for (let i = 0; i < 12; i++) {
        for (let j = 0; j < record.length; j++) {
          if (i == dayjs(record[j].createdAt).month() + 1) {
            data[i] += record[j].amount;
          }
        }
      }
    }
    this.y = data;
  }

```

4. 使用map动态获取tag以及对应的数据
   - v-for遍历map
     - v-for="(tag,name) in tagList" 
     - tag 是键和键值对的数组,name是index
   - 

```
getTagMoney() {
    function getMap(record: any) {
      let map = new Map();
      for (let i = 0; i < record.length; i++) {
        if (!map.has(record[i].tag.name)) {
          map.set(record[i].tag.name, record[i].amount);
        } else {
          const newValue = map.get(record[i].tag.name) + record[i].amount;
          map.set(record[i].tag.name, newValue);
        }
      }
      return map;
    }
    const recordList = this.$store.state.recordList;
    if (this.ymw == "week") {
      // 本周的各标签
      const startDay = dayjs().startOf("week");
      const record = recordList.filter((item: RecordItem) => {
        return dayjs(item.createdAt) >= startDay && this.type == item.type;
      });
      this.tagList = getMap(record);
    } else if (this.ymw == "month") {
      // 本月的各标签
      const record = recordList.filter((item: RecordItem) => {
        return (
          dayjs(item.createdAt).month() == dayjs().month() &&
          this.type == item.type
        );
      });
      this.tagList = getMap(record);
    } else if (this.ymw == "year") {
      //本年的各标签
      const record = recordList.filter((item: RecordItem) => {
        return (
          dayjs(item.createdAt).year() == dayjs().year() &&
          this.type == item.type
        );
      });
      this.tagList = getMap(record);
    }
  }
```