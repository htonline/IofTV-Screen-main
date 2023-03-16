<template>
  <div class="slider">
    <div class="slider-container" :style="{ transform: 'translateX(' + (-currentIndex * width) + 'px)' }">
      <div class="slider-item" v-for="(item, index) in items" :key="index" :style="{ width: width + 'px' }">
        <img :src="item.image" alt="">
      </div>
    </div>
    <div class="slider-dots">
      <span
          class="slider-dot"
          v-for="(item, index) in items"
          :key="index"
          :class="{ 'active': index === currentIndex }"
          @click="handleDotClick(index)"
      ></span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Slider',
  props: {
    items: {
      type: Array,
      required: true
    },
    interval: {
      type: Number,
      default: 3000
    }
  },
  data() {
    return {
      currentIndex: 0,
      timer: null,
      width: 0
    }
  },
  mounted() {
    this.width = this.$el.offsetWidth
    this.start()
  },
  methods: {
    start() {
      this.timer = setInterval(this.next, this.interval)
    },
    pause() {
      clearInterval(this.timer)
      this.timer = null
    },
    next() {
      this.currentIndex = (this.currentIndex + 1) % this.items.length
    },
    handleDotClick(index) {
      this.currentIndex = index
      this.pause()
      this.start()
    }
  }
}
</script>

<style>
.slider {
  position: relative;
  overflow: hidden;
  width: 100%;
  height: 100%;
}

.slider-container {
  display: flex;
  transition: transform 0.3s;
}

.slider-item {
  flex-shrink: 0;
}

.slider-item img {
  display: block;
  width: 100%;
  height: 100%;
}

.slider-dots {
  position: absolute;
  bottom: 20px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1;
}

.slider-dot {
  display: inline-block;
  width: 10px;
  height: 10px;
  margin-right: 10px;
  border-radius: 50%;
  background-color: #ddd;
  cursor: pointer;
}

.slider-dot.active {
  background-color: #333;
}
</style>