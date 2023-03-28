
<template>
  <div class="right_bottom">
    <dv-capsule-chart :config="config" style="width:100%;height:260px" />
  </div>
</template>

<script>
import { currentGET, statisticsProvinceNum } from 'api/modules'
export default {
  data() {
    return {
      gatewayno: '',
      config: {
        showValue: true,
        data: []
      },

    };
  },
  created() {
    this.getData()

  },
  computed: {
  },
  mounted() { },
  methods: {
    //轮询
    switper() {
      if (this.timer) {
        return
      }
      let looper = (a) => {
        this.getData()
      };
      this.timer = setInterval(looper, this.$store.state.setting.echartsAutoTime);
    },
    getData() {
      this.pageflag = true
      // this.pageflag =false
      // 统计各省份，病害信息数量
      statisticsProvinceNum().then(res=>{
        console.log("统计各省份，病害信息数量",res)
        if (res.code == '200') {
          this.config = {
            ...this.config,
            data: res.data
          }
          // 循环访问数据库，统计数据
          // this.switper()
        } else {
          this.pageflag = false
          this.srcList = []
          this.$Message({
            text: res.message,
            type: 'warning'
          })
        }
      })
    },
  },
};
</script>
<style lang='scss' scoped>
.list_Wrap {
  height: 100%;
  overflow: hidden;
  :deep(.kong)   {
    width: auto;
  }
}

.sbtxSwiperclass {
  .img_wrap {
    overflow-x: auto;
  }

}

.right_bottom {
  box-sizing: border-box;
  padding: 0 16px;

  .searchform {
    height: 80px;
    display: flex;
    align-items: center;
    justify-content: center;

    .searchform_item {
      display: flex;
      justify-content: center;
      align-items: center;

      label {
        margin-right: 10px;
        color: rgba(255, 255, 255, 0.8);
      }

      button {
        margin-left: 30px;
      }

      input {}
    }
  }

  .img_wrap {
    display: flex;
    // justify-content: space-around;
    box-sizing: border-box;
    padding: 0 0 20px;
    // overflow-x: auto;

    li {
      width: 105px;
      height: 137px;
      border-radius: 6px;
      overflow: hidden;
      cursor: pointer;
      // background: #84ccc9;
      // border: 1px solid #ffffff;
      overflow: hidden;
      flex-shrink: 0;
      margin: 0 10px;

      img {
        flex-shrink: 0;
      }
    }




  }

  .noData {
    width: 100%;
    line-height: 100px;
    text-align: center;
    color: rgb(129, 128, 128);

  }
}
</style>