<template>
  <div class="container">
    <!-- 
      source:需要绑定的数据
      initial:初始展示哪一项的索引 默认0
      interval:多久切换一次 默认2000
      ...
     -->
    <banner-3-d :source="source"></banner-3-d>
  </div>
</template>

<script>
import "./assets/css/reset.min.css";
import Banner3D from "./components/Banner3D.vue";

// 需要绑定的数据
const source = [
  {
    id: 1,
    pic: require("./assets/images/01.jpg"),
    descript: {
      name: "蒙奇·D·路飞",
      identity: "草帽海贼团船长",
      dream: "找到ONE PIECE，并成为海贼王",
    },
  },
  {
    id: 2,
    pic: require("./assets/images/02.jpg"),
    descript: {
      name: "罗罗诺亚·索隆",
      identity: "草帽海贼团战斗员",
      dream: "世界第一大剑豪",
    },
  },
  {
    id: 3,
    pic: require("./assets/images/03.jpg"),
    descript: {
      name: "“小贼猫”娜美",
      identity: "草帽海贼团航海士",
      dream: "绘制出自己的世界地图、绘制全世界的航海图",
    },
  },
  {
    id: 4,
    pic: require("./assets/images/04.jpg"),
    descript: {
      name: "“GOD”▪乌索普",
      identity: "草帽海贼团狙击手",
      dream: "成为勇敢的海上战士",
    },
  },
  {
    id: 5,
    pic: require("./assets/images/05.jpg"),
    descript: {
      name: "“黑足”山治",
      identity: "草帽海贼团厨师",
      dream: "寻找传说中的奇迹之海－ALL BLUE",
    },
  },
  {
    id: 6,
    pic: require("./assets/images/06.jpg"),
    descript: {
      name: "托尼托尼·乔巴",
      identity: "草帽海贼团船医",
      dream: "成为万能药",
    },
  },
  {
    id: 7,
    pic: require("./assets/images/07.jpg"),
    descript: {
      name: "“改造人”弗兰奇",
      identity: "草帽海贼团船工",
      dream: "制造出梦想之船",
    },
  },
  {
    id: 8,
    pic: require("./assets/images/08.jpg"),
    descript: {
      name: "“鼻歌”&“灵魂之王”布鲁克",
      identity: "草帽海贼团音乐家",
      dream: "环绕世界一周到伟大航道双子岬跟伙伴鲸鱼“拉布”重逢",
    },
  },
  {
    id: 9,
    pic: require("./assets/images/09.jpg"),
    descript: {
      name: "“恶魔之子”妮可·罗宾",
      identity: "草帽海贼团考古学家",
      dream: "在历史正文碑的指引下，到达伟大航道的尽头“拉夫德鲁”",
    },
  },
];

export default {
  components: {
    Banner3D,
  },
  // 组合API：入口
  //  + 没有this
  //  + 在原有的周期函数beforeCreate之前：初始化props后，直接执行setup
  setup() {
    // 提供一些数据(响应式数据)供视图中调取使用
    return {
      source,
    };
  },
};
</script>

<style>
html,
body,
#app {
  height: 100%;
  overflow-x: hidden;
  background: #f4f4f4;
}

.container {
  position: relative;
  box-sizing: border-box;
  height: 350px;
  background: url("./assets/images/bg.jpg") no-repeat;
  background-size: cover;
}
</style>