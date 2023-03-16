<template>
    <slider :items="items"></slider>
</template>

<script>
import Slider from './Slider'
import {getPictureByTunnelID} from 'api/modules'
import bus from '../../utils/bus'

export default {
  components: {
    Slider
  },
  data() {
    return {
      tunnelID: 0,
      requiredUrls: [],
      urls: [],
      // 轮播图
      items: [
        { image: require("@/assets/img/test1/1.png") },
        { image: require("@/assets/img/test1/2.png") },
        { image: require("@/assets/img/test1/3.png") },
        { image: require("../../assets/img/test1/4.png") },
        { image: require("../../assets/img/test1/5.png") },
      ]
    }
  },
  created() {
    // 如果有值传过来了，就要调用获取图片
    bus.$on("sendTunnelId", (data) => {
      getPictureByTunnelID(data.pieceId).then(res => {
        console.log("Picture从数据中拿出来的结果", res)
        // 将数组中url那一列单独拿出来，做一个数组
        this.urls = res.object.map(({ url }) => url);
        // 将每个url的值都加上require (require只能拼接，不能只放变量)
        this.requiredUrls = this.urls.map(url => require("@/assets/"+url));
        // 轮播图清空
        this.items = []
        // 将从数据库中拿到的地址放入轮播图中
        for (let i = 0; i < this.requiredUrls.length; i++) {
          this.items[i] = { image: this.requiredUrls[i] };
        }
      })
    })
  },
  mounted() {
    this.getPicture()
  },

  methods: {
    getPicture() {
      getPictureByTunnelID(this.tunnelID).then(res => {
        console.log("picture", res)

      })
    }
  }
}
</script>
