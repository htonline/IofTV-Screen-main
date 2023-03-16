<template>
  <div class="container">
    <div class="left">
      <div class="content">
        <h2>路基空洞</h2>
        <p>{{ description }}</p>
      </div>
    </div>
    <div class="right">
      <div class="content">
        <h2>修复方式</h2>
        <p>{{ repair }}</p>
      </div>
      <div class="content">
        <h2>描述性内容</h2>
        <p>实施常规化的地下空洞探测和填补</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      title: '页面标题',
      description: '这是一个页面标题的描述性内容，可能比较长，需要占用较多的空间。',
      description2: '这是右侧下方的描述性内容，也可能比较长。',
      repair: '这里是修复方式的描述性内容。',
    };
  },
};
</script>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

.left {
  flex: 1;
  margin-right: 30px;
}

.title {
  margin-bottom: 30px;
}

.title h1 {
  font-size: 36px;
  margin-bottom: 10px;
}

.title p {
  font-size: 16px;
  line-height: 1.5;
  color: #666;
}

.right {
  flex: 1;
}

.content {
  margin-bottom: 30px;
}

.content h2 {
  font-size: 24px;
  margin-bottom: 10px;
}

.content p {
  font-size: 16px;
  line-height: 1.5;
  color: #666;
}
</style>