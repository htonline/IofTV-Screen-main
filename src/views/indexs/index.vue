
<!--各边框主题的更改-->

<template>
  <div class="contents">
    <div class="contetn_left">
      <div class="pagetab">
        <!-- <div class="item">实时监测</div> -->
      </div>

<!--      左边-上部分-->
      <ItemWrap class="contetn_left-top contetn_lr-item" title="病害总览">
        <LeftTop />
      </ItemWrap>
<!--      左边-中间部分-->
      <ItemWrap class="contetn_left-center contetn_lr-item" title="病害类型总览">
        <LeftCenter />
      </ItemWrap>
<!--      左边-下部分-->
      <ItemWrap class="contetn_left-bottom contetn_lr-item" title="设备仪器" style="padding: 0 10px 16px 10px">
        <LeftBottom />
      </ItemWrap>
    </div>


<!--    中间部分-->
    <div class="contetn_center">
<!--      中间-地图部分-->
      <CenterMap class="contetn_center_top" />
<!--      中间-下部图表部分-->
      <ItemWrap class="contetn_center-bottom" title="地质图层">
        <CenterBottom />
      </ItemWrap>
    </div>

<!--    右边部分-->
    <div class="contetn_right">
<!--      右边-上部分-->
      <ItemWrap class="contetn_left-bottom contetn_lr-item" title="报警次数">
        <RightTop />
      </ItemWrap>
<!--      右边-中间部分-->
      <ItemWrap class="contetn_left-bottom contetn_lr-item" title="各省市病害数量统计(Top8)" style="padding: 0 10px 16px 10px">
        <RightCenter />
      </ItemWrap>
<!--      右边-下部分-->
      <ItemWrap class="contetn_left-bottom contetn_lr-item" title="病害信息表 ">
        <RightBottom />
      </ItemWrap>
    </div>
  </div>
</template>

<script>
import LeftTop from './left-top.vue'
import LeftCenter from "./left-center.vue";
import LeftBottom from "./left-bottom.vue";
import CenterMap from "./center-map.vue";
import CenterBottom from "./center-bottom.vue";
import RightTop from "./right-top.vue";
import RightCenter from "./right-center.vue";
import RightBottom from "./right-bottom.vue";

export default {
  components: {
    LeftTop,
    LeftCenter,
    LeftBottom,
    CenterMap,
    RightTop,
    RightCenter,
    RightBottom,
    CenterBottom,
  },
  data() {
    return {
    
    };
  },
  filters: {
    numsFilter(msg) {
      return msg || 0;
    },
  },
  created() {
  },

  mounted() {},
  methods: {
  
  },
};
</script>
<style lang="scss" scoped>
// 内容
.contents {
  .contetn_left,
  .contetn_right {
    width: 540px;
    box-sizing: border-box;
    // padding: 16px 0;
  }

  .contetn_center {
    width: 720px;
  }

  //左右两侧 三个块
  .contetn_lr-item {
    height: 310px;
  }

  .contetn_center_top {
    width: 100%;
  }

  // 中间
  .contetn_center {
    display: flex;
    flex-direction: column;
    justify-content: space-around;
  }

  .contetn_center-bottom {
    height: 315px;
  }

  //左边 右边 结构一样
  .contetn_left,
  .contetn_right {
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    position: relative;

  
  }
}


@keyframes rotating {
    0% {
        -webkit-transform: rotate(0) scale(1);
        transform: rotate(0) scale(1);
    }
    50% {
        -webkit-transform: rotate(180deg) scale(1.1);
        transform: rotate(180deg) scale(1.1);
    }
    100% {
        -webkit-transform: rotate(360deg) scale(1);
        transform: rotate(360deg) scale(1);
    }
}
</style>
