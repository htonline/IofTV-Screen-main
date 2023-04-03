<template>
  <div id="main" style="width: 100%; height: 250px;"></div>
</template>
<script>
import * as echarts from 'echarts';
import {excelData} from "api/modules"
import bus from '../../utils/bus'

export default {
  name: "Chart",
  data() {
    return {
      xData: [],
      yData: [],
      option: null,
      data: []
    }
  },
  created() {
    bus.$on("sendRadar", (object) => {
      // 传过来的值，先清空上一个的雷达数据图, 绘制一个空表
      this.data = []
      this.drawChart()
      /*
      * 用left-bottom穿过来的tunnel_id, 从file表中找到对应的雷达文件路径
      * 新值填充上之后，再绘制
      * */

      this.generateData(object.pieceId).then(() => {
        this.drawChart();
      }).catch(err => console.error(err));

    })
  },
  mounted() {
    // 使用 excelData() 函数检索数据。这个函数可能需要一些时间来检索数据，在此期间，drawChart() 函数继续执行而不等待数据被检索。
    //这意味着当在 drawChart() 函数中记录 this.data 变量时，数据可能尚未被检索，变量仍然为空。

    // 等到拿到数据后, 再绘制表格
    this.generateData().then(() => {
      this.drawChart();
    }).catch(err => console.error(err));

  },

  methods: {
    // generateData() 方法返回一个 Promise，
    // 在数据检索完成时解决，mounted() 方法调用 generateData2() 方法并等待 Promise 解决后才调用 drawChart() 函数。
    // 这确保了在尝试绘制图表之前数据可用
    generateData(tunnelId) {
      return new Promise((resolve, reject) => {
        excelData(tunnelId).then(res => {
          console.log("res", res);
          this.data = res.data.dataList
          this.xData = res.data.rowList
          this.yData = res.data.colList
          console.log("data_fun",this.data)
          console.log("xData_fun",this.xData)
          console.log("yData_fun",this.yData)
          resolve();
        }).catch(err => reject(err))
      })
    },
    drawChart() {
      const chartDom = document.getElementById('main');
      const myChart = echarts.init(chartDom);

      this.option = {
        tooltip: {},
        // 右上角的还原/下载图片功能
        toolbox: {
          feature: {
            dataZoom: {
              yAxisIndex: "none",
            },
            restore: {},
            saveAsImage: {},
          },
        },
        // x轴
        xAxis: {
          type: 'category',
          data: this.xData
        },
        // y轴
        yAxis: {
          type: 'category',
          data: this.yData
        },

        // 图片下方的伸缩移动功能
        dataZoom: [
          {
            type: "inside",
            start: 0,
            end: 10,
          },
          {
            start: 0,
            end: 10,
          },
        ],

        // 定义不同的值对应不同的颜色
        // 最小值-11000, 最大值20000
        visualMap: {
          min: -11000,
          max: 18000,
          calculable: true,
          realtime: false,
          inRange: {
            color: [
              '#313695',
              '#4575b4',
              '#74add1',
              '#abd9e9',
              '#e0f3f8',
              '#ffffbf',
              '#fee090',
              '#fdae61',
              '#f46d43',
              '#d73027',
              '#a50026'
            ]
          }
        },
        series: [
          {
            name: 'Gaussian',
            type: 'heatmap',
            data: this.data,
            emphasis: {
              itemStyle: {
                borderColor: '#333',
                borderWidth: 1
              }
            },
            progressive: 1000,
            animation: false
          }
        ]
      };
      myChart.setOption(this.option);
    },

  }
};
</script>