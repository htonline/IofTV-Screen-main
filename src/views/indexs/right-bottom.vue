<template>
  <div class="container">
    <div class="left">
      <div class="content">
        <h2>{{ diseaseType }}</h2>
        <p>{{ diseaseDescription }}</p>
      </div>
      <div class="content" style="position: absolute; bottom: 0; transform: translateY(50%)">
        <!--        是否修复-->
        <div v-if="tunnelState != null">
          <h2><span v-if="tunnelState == 4" style="color: #07f7a8;">已修复</span></h2>
          <h2><span v-if="tunnelState != 4" style="color: #f5023d;">未修复</span></h2>
        </div>
      </div>
    </div>
    <div class="right">
      <div class="content">
        <h2>治疗方式</h2>
        <p>{{ repairMethod }}</p>
      </div>
    </div>
  </div>
</template>

<script>


import bus from '../../utils/bus'
export default {
  data() {
    return {
      diseaseType: '病害类型',
      diseaseDescription: '',
      repairMethod: '',
      tunnelState: null
    };
  },

  created() {
    bus.$on("sendDisease", (data) => {
      console.log("sendDisease", data)
      this.diseaseType = data.diseaseType
      this.diseaseDescription = data.diseaseDescription
      this.repairMethod = data.repairMethod
      this.tunnelState = data.tunnelState
    })
  }

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
