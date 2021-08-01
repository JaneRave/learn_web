<template>
  <div class="banner-box" ref="root">
    <!-- 轮播图区域 -->
    <div class="wrapper">
      <div
        v-for="item in state.source"
        :key="item.id"
        :class="item.className"
        :style="item.sty"
      >
        <img :src="item.pic" alt="" />
        <div class="mark"></div>
        <p class="detail">
          <span>{{ item.descript.name }}</span>
          <span>身份：{{ item.descript.identity }}</span>
          <span>梦想：{{ item.descript.dream }}</span>
        </p>
      </div>
    </div>
    <!-- 左右切换按钮 -->
    <a
      href="javascript:;"
      class="arrow arrow-left"
      v-throttle="[change.bind(null, 'left'), 500]"
    ></a>
    <a
      href="javascript:;"
      class="arrow arrow-right"
      v-throttle="[change.bind(null, 'right'), 500]"
    ></a>
  </div>
</template>

<script>
import { onMounted, reactive, ref, watch } from "vue";
export default {
  // 注册传递的属性，设置规则
  props: {
    source: {
      type: Array,
      required: true,
    },
    initial: {
      type: Number,
      default: 0,
    },
    interval: {
      type: Number,
      default: 2000,
    },
  },
  // 入口
  setup(props) {
    // props是基于Proxy处理的响应式数据：属性值是不可以更改的
    /* 1.处理source */
    let source = props.source,
      diff = source.length - 5;
    if (diff < 0) {
      diff = Math.abs(diff);
      source.slice(0, diff).forEach((item) => {
        source.push({
          ...item,
          id: parseInt(source[source.length - 1].id) + 1,
        });
      });
    }
    
    /* 2.处理每一项的样式 */
    const computed = (initial, source) => {
      // 确保五项索引是合法的
      let len = source.length;
      initial = initial < 0 ? 0 : initial >= len ? len - 1 : initial;
      let temp1 = initial - 2,
        temp2 = initial - 1,
        temp3 = initial,
        temp4 = initial + 1,
        temp5 = initial + 2;
      temp1 < 0 ? (temp1 = len + temp1) : null;
      temp2 < 0 ? (temp2 = len + temp2) : null;
      temp4 >= len ? (temp4 = temp4 - len) : null;
      temp5 >= len ? (temp5 = temp5 - len) : null;

      // 计算每一项的样式
      return source.map((item, index) => {
        let transform = `translate(-50%,-50%) scale(0.55)`,
          zIndex = 0,
          className = "slide";
        switch (index) {
          case temp3:
            zIndex = 3;
            className = "slide active";
            transform = `translate(-50%,-50%) scale(1)`;
            break;
          case temp1:
            zIndex = 1;
            transform = `translate(-195%,-50%) scale(0.7)`;
            break;
          case temp2:
            zIndex = 2;
            transform = `translate(-130%,-50%) scale(0.85)`;
            break;
          case temp4:
            zIndex = 2;
            transform = `translate(30%,-50%) scale(0.85)`;
            break;
          case temp5:
            zIndex = 1;
            transform = `translate(95%,-50%) scale(0.7)`;
            break;
        }
        item.sty = {
          transform,
          zIndex,
        };
        item.className = className;
        return item;
      });
    };
    source = computed(props.initial, source);

    /* 3.构建响应式数据 */
    let state = reactive({
      initial: props.initial,
      source,
    });
    // 监听initial，重新计算每一项的样式
    watch(
      () => state.initial,
      (initial, prevInitial) => {
        state.source = computed(initial, state.source);
      }
    );

    /* 4.自动轮播 */
    let autoTimer = null;
    const autoPlay = () => {
      autoTimer = setInterval(() => {
        state.initial++;
        if (state.initial >= state.source.length) {
          state.initial = 0;
        }
      }, props.interval);
    };

    /* 5.页面第一次渲染完：开启自动轮播 & 鼠标进入离开控制自动轮播 */
    let root = ref(null);
    onMounted(() => {
      autoPlay();
      let box = root.value;
      box.onmouseenter = () => clearInterval(autoTimer);
      box.onmouseleave = () => autoPlay();
    });

    /* 6.点击左右按钮切换 */
    const change = (dir) => {
      if (dir === "right") {
        state.initial++;
        state.initial >= state.source.length ? (state.initial = 0) : null;
        return;
      }
      state.initial--;
      state.initial < 0 ? (state.initial = state.source.length - 1) : null;
    };

    return {
      state,
      root,
      change,
    };
  },
};
</script>

<style lang="less" scoped>
.banner-box {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 80%;
  height: 80%;

  .wrapper {
    position: relative;
    box-sizing: border-box;
    height: 100%;

    .slide {
      position: absolute;
      top: 50%;
      left: 50%;
      z-index: 0;
      transform: translate(-50%, -50%);
      box-sizing: border-box;
      width: 25%;
      height: 100%;
      border: 3px solid #000;
      overflow: hidden;
      transition: 0.5s;

      img {
        display: block;
        width: 100%;
        height: 100%;
      }

      .mark {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: rgba(0, 0, 0, 0.75);
        transition: 0.3s;
      }

      .detail {
        position: absolute;
        left: 0;
        bottom: 0;
        box-sizing: border-box;
        padding: 10px;
        width: 100%;
        height: 40%;
        background: rgba(0, 0, 0, 0.75);
        background: -webkit-linear-gradient(
          top,
          rgba(0, 0, 0, 0.35),
          rgba(0, 0, 0, 0.75)
        );
        transform: translateY(100%);
        transition: 0.3s;

        span {
          display: block;
          line-height: 2;
          font-size: 12px;
          color: #fff;
        }
      }

      &.active .mark,
      &:hover .mark {
        background: rgba(0, 0, 0, 0);
      }

      &.active:hover .detail {
        transform: translateY(0);
      }
    }
  }

  .arrow {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    width: 39px;
    height: 80px;
    background: url("../assets/images/btn.png") no-repeat center center;

    &.arrow-left {
      left: 0;
      background-position: 0 0;
    }

    &.arrow-right {
      right: 0;
      background-position: -39px 0;
    }
  }
}
</style>