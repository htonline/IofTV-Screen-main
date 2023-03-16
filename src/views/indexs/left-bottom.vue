<template>
  <div v-if="pageflag" class="right_center_wrap beautify-scroll-def" :class="{ 'overflow-y-auto': !sbtxSwiperFlag }">
    <component :is="components" :data="list" :class-option="defaultOption">
      <ul class="right_center ">
        <li class="right_center_item" v-for="(item, i) in list" :key="i" @click="sendlntlat(item)">
          <span class="orderNum">{{ i + 1 }}</span>
          <div class="inner_right">
            <div class="dibu"></div>
            <div class="flex">
              <div class="info" >
                <span class="labels">经度：</span>
                <span class="contents "> {{ item.detectLocationLng }}</span>
              </div>
              <div class="info">
                <span class="labels">纬度：</span>
                <span class="contents "> {{ item.detectLocationLat }}</span>
              </div>
              <div class="info">
                <span class="labels">病害类型：</span>
                <!--                如果item.alertvalue有值，就输出，否则输出montionFilter,这是个空值-->
                <!--                <span class="contents warning"> {{ item.alertvalue | montionFilter }}</span>-->
                <span class="contents warning" v-if="item.tunnelState == 1" style="color: #f5023d;"> {{ item.diseaseType }}</span>
                <span class="contents warning" v-if="item.tunnelState == 2" style="color: #e3b337;"> {{ item.diseaseType }}</span>
                <span class="contents warning" v-if="item.tunnelState == 3" style="color: #07f7a8;"> {{ item.diseaseType }}</span>
                <span class="contents warning" v-if="item.tunnelState == 4" style="color: gray;"> {{ item.diseaseType }}</span>
              </div>
            </div>


            <div class="flex">

              <div class="info">
                <span class="labels"> 地址：</span>
                <!--                <span class="contents ciyao" style="font-size:12px"> {{ item.provinceName }}/{{ item.cityName }}/{{ item.countyName }}</span>-->
                <span class="contents ciyao" style="font-size:12px"> {{ item.detectLocation }}</span>
              </div>
              <div class="info time">
                <span class="labels">时间：</span>
                <span class="contents" style="font-size:12px"> {{ item.createTime }}</span>
              </div>

            </div>
            <div class="flex">

              <div class="info">
                <span class="labels">状态：</span>
<!--                <span class="contents ciyao" :class="{ warning: item.diseaseDescription }"> {{ item.diseaseDescription || '无' }}</span>-->
                <span class="contents ciyao" v-if="item.tunnelState == 1" style="color: #f5023d;" > 紧急</span>
                <span class="contents ciyao" v-if="item.tunnelState == 2" style="color: #e3b337;" > 一般</span>
                <span class="contents ciyao" v-if="item.tunnelState == 3" style="color: #07f7a8;" > 正在修复</span>
                <span class="contents ciyao" v-if="item.tunnelState == 4" style="color: gray;"> 已修复</span>
              </div>
            </div>
          </div>
        </li>
      </ul>
    </component>
  </div>
  <Reacquire v-else @onclick="getData" style="line-height:200px" />

</template>

<script>
import {currentGET, getTunnelData} from 'api/modules'
import vueSeamlessScroll from 'vue-seamless-scroll'  // vue2引入方式(无缝滚动)
import Kong from '../../components/kong.vue'

import bus from '../../utils/bus'

export default {
  components: { vueSeamlessScroll, Kong },

  data() {
    return {
      list: [],
      pageflag: true,
      defaultOption: {
        ...this.$store.state.setting.defaultOption,
        limitMoveNum: 3,
        singleHeight: 250,
        step:0,
      },

    };
  },
  computed: {
    sbtxSwiperFlag() {
      let ssyjSwiper = this.$store.state.setting.ssyjSwiper
      if (ssyjSwiper) {
        this.components = vueSeamlessScroll
      } else {
        this.components = Kong
      }
      return ssyjSwiper
    }
  },
  created() {
    this.getData()
  },

  mounted() { },
  methods: {
    // 发送点击框的经纬度
    sendlntlat(item) {
      let data = {
        positionLng: item.detectLocationLng,
        positionLat: item.detectLocationLat,
        startPointLng: item.tunnelStartPointLng,
        startPointLat: item.tunnelStartPointLat,
        stopPointLng: item.tunnelStopPointLng,
        stopPointLat: item.tunnelStopPointLat,
        pieceId: item.tunnelId
      }
      //给地图发数据
      bus.$emit('right_bottom_sendLngLat',data)
      //给地图下面那个框，发数据
      bus.$emit('center_bottom_sendPieceId',data)
    },
    getData() {
      this.pageflag = true
      // 获取隧道信息的所有数据
      getTunnelData().then(res => {
        console.log("getTunnelData:", res);
        this.list = res.object
        let timer = setTimeout(() => {
          clearTimeout(timer)
          // this.defaultOption.step=this.$store.state.setting.defaultOption.step
          this.defaultOption.step=1
        }, this.$store.state.setting.defaultOption.waitTime);
      })
    },
  },
};
</script>
<style lang='scss' scoped>
.right_center {
  width: 100%;
  height: 100%;

  .right_center_item {
    display: flex;
    align-items: center;
    justify-content: center;
    height: auto;
    padding: 10px;
    font-size: 14px;
    color: #fff;

    .orderNum {
      margin: 0 20px 0 -20px;
    }


    .inner_right {
      position: relative;
      height: 100%;
      width: 400px;
      flex-shrink: 0;
      line-height: 1.5;

      .dibu {
        position: absolute;
        height: 2px;
        width: 104%;
        background-image: url("../../assets/img/zuo_xuxian.png");
        bottom: -12px;
        left: -2%;
        background-size: cover;
      }
    }

    .info {
      margin-right: 10px;
      display: flex;
      align-items: center;

      .labels {
        flex-shrink: 0;
        font-size: 12px;
        color: rgba(255, 255, 255, 0.6);
      }

      .zhuyao {
        color: $primary-color;
        font-size: 15px;
      }

      .ciyao {
        color: rgba(255, 255, 255, 0.8);
      }

      .warning {
        color: #E6A23C;
        font-size: 15px;
      }
    }

  }
}

.right_center_wrap {
  overflow: hidden;
  width: 100%;
  height: 250px;
}

.overflow-y-auto {
  overflow-y: auto;
}
</style>