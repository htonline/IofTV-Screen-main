<template>
  <div class="center_bottom">
    <div id="main" class="echarts_bottom"></div>
  </div>
</template>

<script>
import * as echarts from 'echarts';
import 'echarts-gl';


export default {
  mounted() {
    var chartDom = document.getElementById('main');
    var myChart = echarts.init(chartDom, 'dark');
    var option;

    // 异步加载 SimplexNoise 库
    //simplex-noise 库是一个用于生成 2D 和 3D 噪声的 JavaScript 库，
    // 可以用于创建流动的云、火焰、水面、地形、动画等效果。
    this.loadScript('https://fastly.jsdelivr.net/npm/simplex-noise@2.4.0/simplex-noise.js', function () {
      // 创建 SimplexNoise 实例
      var noise = new SimplexNoise(Math.random);

      function generateData(theta, min, max) {
        var data = [];
        for (var i = 0; i <= 50; i++) {
          for (var j = 0; j <= 50; j++) {
            var value = noise.noise2D(i / 20, j / 20);
            valMax = Math.max(valMax, value);
            valMin = Math.min(valMin, value);
            data.push([i, j, value * 2 + 4]);
          }
        }
        return data;
      }

      var valMin = Infinity;
      var valMax = -Infinity;
      var data = generateData(2, -5, 5);
      console.log(valMin, valMax);
      console.log("datadata",data);
      myChart.setOption(
          (option = {
            visualMap: {
              show: false,
              min: 2,
              max: 6,
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
            xAxis3D: {
              type: 'value'
            },
            yAxis3D: {
              type: 'value'
            },
            zAxis3D: {
              type: 'value',
              max: 10,
              min: 0
            },
            grid3D: {
              axisLine: {
                lineStyle: {color: '#fff'}
              },
              axisPointer: {
                lineStyle: {color: '#fff'}
              },
              viewControl: {
                // autoRotate: true
              },
              light: {
                main: {
                  shadow: true,
                  quality: 'ultra',
                  intensity: 1.5
                }
              }
            },
            series: [
              {
                type: 'bar3D',
                data: data,
                shading: 'lambert',
                label: {
                  formatter: function (param) {
                    return param.value[2].toFixed(1);
                  }
                }
              }
            ]
          })
      );
    });

    option && myChart.setOption(option);
  },
  methods: {
    loadScript(url, callback) {
      var script = document.createElement('script');
      script.type = 'text/javascript';

      if (script.readyState) {
        // IE
        script.onreadystatechange = function () {
          if (script.readyState == 'loaded' || script.readyState == 'complete') {
            script.onreadystatechange = null;
            callback();
          }
        };
      } else {
        // Others
        script.onload = function () {
          callback();
        };
      }

      script.src = url;
      document.getElementsByTagName('head')[0].appendChild(script);
    }
  }
};
</script>
<style lang="scss" scoped>
.center_bottom {
  width: 100%;
  height: 100%;

  .echarts_bottom {
    width: 100%;
    height: 100%;
  }
}
</style>
