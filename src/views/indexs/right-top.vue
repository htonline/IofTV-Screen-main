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
      require_urls: [],
      urls: [],
      // 轮播图
      items: [
        { image: require("../../assets/img/test1/1.png") },
        { image: require("../../assets/img/test1/2.png") },
        { image: require("../../assets/img/test1/3.png") },
        { image: require("../../assets/img/test1/4.png") },
        { image: require("../../assets/img/test1/5.png") },
      ]
    }
  },
  created() {
    bus.$on("sendTunnelId", (data) => {
      console.log("right-top",data)
      getPictureByTunnelID(data.pieceId).then(res => {
        console.log("picture", res)
        // 将数组中url那一列单独拿出来，做一个数组
        this.urls = res.object.map(({ url }) => url);
        console.log("urls", this.urls)

        // 将每个url的值都加上require
        // 将数组的值赋值给items
        this.items = this.urls.map(url => ({ image: { uri: url } }));
        console.log("AAAAAA", this.items)
        // this.items = this.items.map(url => require(url));
        this.items = this.items.map(item => ({ image: require(item.image) }));
        console.log("items", this.items)

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
