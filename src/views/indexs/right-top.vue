<template>
    <slider :items="items"></slider>
</template>

<script>
import Slider from './Slider'

export default {
  components: {
    Slider
  },
  data() {
    return {
      items: [
        { image: require("../../assets/img/test/1.png") },
        { image: require("../../assets/img/test/2.png") },
        { image: require("../../assets/img/test/3.png") },
        { image: require("../../assets/img/test/4.png") },
        { image: require("../../assets/img/test/5.png") },
        // { image: 'https://example.com/image3.jpg' }
      ]
    }
  }
}
</script>
