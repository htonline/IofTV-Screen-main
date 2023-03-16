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
        { image: './test_img/1.png' },
        { image: './test_img/2.png' },
        { image: './test_img/3.png' },
        { image: './test_img/4.png' },
        // { image: 'https://example.com/image3.jpg' }
      ]
    }
  }
}
</script>
