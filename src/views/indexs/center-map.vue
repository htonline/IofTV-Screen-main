<script src="../../api/modules/index.js"></script>
<template>
<!--  地图标题框架-->
  <div class="centermap">
<!--    绘制步行路线需要-->
    <div id="panel"></div>
    <div class="maptitle">
      <div class="zuo"></div>
      <span class="titletext">{{ maptitle }}</span>
      <div class="you"></div>
    </div>
<!--    地图框架-->
    <div class="mapwrap">
      <dv-border-box-13>
<!--地图本身-->
<!--        <Echart id="CenterMap" :options="options" ref="CenterMap" />-->
<!--vue项目中动态给div赋予ref属性，以达到动态显示echarts图数量的目的。-->
        <div id="container"></div>
      </dv-border-box-13>
    </div>
  </div>
</template>
<script>

//绘制路线的密钥
window._AMapSecurityConfig = {
  securityJsCode:'6e16b7dbfb31d4c7cb7005759bbd865c',
}

import xzqCode from "../../utils/map/xzqCode";
import {currentGET, getGeoInformationByIp} from "api/modules";
import * as echarts from "echarts";
import { GETNOBASE } from "api";
import AMapLoader from '@amap/amap-jsapi-loader';

import bus from '../../utils/bus'


export default {
  data() {
    return {
      map: null,
      walking: null,
      maptitle: "center-map",
      dataLngLat: {
        positionLng: 123.12,
        positionLat: 12.21
      },

      // 进行逆地理编码，所需要的参数
      geoParam: {
        radius: 1000,
        ouput: "xml",
        location: "116.31003,39.991957",
        key: "088124d7f51a0d65fd8e95c04f124492",
        extensions: "all",
      },

      // 接受逆地理编码返回的数据
      geoInformation:"",


    };
  },
  created() {
    // 接收右下角表格返回的经纬度, 在地图中定位其具体位置
    bus.$on('sendLngLat',(data) => {
      console.log("center_map",data)
      this.dataLngLat = data
      // 参数1: 坐标；参数2: 是否立即过度到目标; 参数3: 如果是的话，几秒钟
      this.map.setCenter([this.dataLngLat.positionLng, this.dataLngLat.positionLat], false)
      this.map.setZoom(17)
      //根据起终点坐标规划步行路线
      this.walking.search([Number(data.startPointLng),Number(data.startPointLat)], [Number(data.stopPointLng), Number(data.stopPointLat)], function(status, result) {
        // result即是对应的步行路线数据信息，相关数据结构文档请参考  https://lbs.amap.com/api/javascript-api/reference/route-search#m_WalkingResult
        if (status === 'complete') {
          console.log('绘制步行路线完成')
        } else {
          console.log('步行路线数据查询失败' + result)
        }
      });
    })
  },

  mounted() {
    this.initMap()
  },
  methods: {

    initMap(){
      AMapLoader.load({
        key:"8e29f96b2e2b4634d618fa50f697d037",             // 申请好的Web端开发者Key，首次调用 load 时必填
        version:"2.0",      // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
        plugins:['AMap.MarkerCluster', 'AMap.Geocoder', 'AMap.MapType', 'AMap.Walking'],       // 分别是点聚合，逆地理编码，3D建模导入, 步行规划
      }).then((AMap)=>{
         this.map = new AMap.Map("container",{  //设置地图容器id
          // 是否能左右旋转
          rotateEnable:true,
          // 是否能上下移动
          pitchEnable:true,
          // 缩放等级
          zoom: 5,
          // 地图俯仰角度，有效范围 0 度- 83 度
          pitch: 50,
          // 地图左右旋转，默认就行
          // 地图左右旋转，默认就行
          rotation: -15,
          //开启3D视图,默认为关闭
          viewMode:'3D',
          // 初始化地图中心点位置
          // center: [104.937478,35.439575],
          center: [116.39,39.9],
          //设置地图的显示样式
          mapStyle: 'amap://styles/blue',
        });

        //步行导航
        this.walking = new AMap.Walking({
          map: this.map,
          // panel: "panel",
          hideMarkers: true, //隐藏起始点图标
          outlineColor: 'red', // 线路的描边颜色
          // autoFitView: false, //路线生成后，是否将其处于视野显眼位置
        });
        //根据起终点坐标规划步行路线
        this.walking.search([this.startPointLng, this.startPointLat], [this.stopPointLng, this.stopPointLat], function(status, result) {
          // result即是对应的步行路线数据信息，相关数据结构文档请参考  https://lbs.amap.com/api/javascript-api/reference/route-search#m_WalkingResult
          if (status === 'complete') {
            console.log('绘制步行路线完成')
          } else {
            console.log('步行路线数据查询失败' + result)
          }
        });


        // 折线的节点坐标数组，每个元素为 AMap.LngLat 对象
        var path = [
          new AMap.LngLat(116.38,39.87),
          new AMap.LngLat(114.30,30.59),
          new AMap.LngLat(114.06,22.54),
          new AMap.LngLat(121.47,31.23),
          new AMap.LngLat(106.55,29.56),
        ];
        // 创建折线实例
        var polyline = new AMap.Polyline({
          path: path,
          borderWeight: 2, // 线条宽度，默认为 1
          strokeColor: 'red', // 线条颜色
          lineJoin: 'round' // 折线拐点连接处样式
        });
        // 将折线添加至地图实例
        this.map.add(polyline);

        // getGeoInformationByIP()方法与initMap()方法是平级的
        // 不能直接在function()中用this，function()中的this指向initMap()
        var that = this
        //鼠标点击地图
        this.map.on('click', function(e) {
          //获取点击位置的经纬度
          var lnglat_str = e.lnglat.getLng()+","+e.lnglat.getLat()
          alert(lnglat_str)
          // 将经纬度作为参数，进行逆地理编码
          that.geoParam.location = lnglat_str
          that.getGeoInformationByIP(that.geoParam)
        });


        // 信息窗口
        //构建信息窗体中显示的内容
        var info = [];
        info.push("<div class='input-card content-window-card'><div><img style=\"float:left;width:67px;height:16px;\" src=\" https://webapi.amap.com/images/autonavi.png \"/></div> ");
        info.push("<div style=\"padding:7px 0px 0px 0px;\"><h4>高德软件</h4>");
        info.push("<p class='input-item'>电话 : 010-84107000   邮编 : 100102</p>");
        info.push("<p class='input-item'>地址 :北京市朝阳区望京阜荣街10号首开广场4层</p></div></div>");

        var infoWindow = new AMap.InfoWindow({
          content: info.join("")  //使用默认信息窗体框样式，显示信息内容
        });


        // 点聚合
        var cluster;
        var gridSize = 30
        var points = [
          { lnglat: ["108.94", "34.34"] },//西安
          { lnglat: ["113.26", "23.13"] },//广州
          { lnglat: ["113.78", "34.76"] },//郑州
          { lnglat: ["112.45", "34.62"] },//洛阳
          { lnglat: ["112.12", "32.01"] },//襄阳
          { lnglat: ["111.29", "30.69"] },//宜昌
          { lnglat: ["114.87", "30.45"] },//黄冈
          { lnglat: ["116.38", "39.87"] },//北京
          { lnglat: ["114.30", "30.59"] },//武汉
          { lnglat: ["114.06", "22.54"] },//深圳
          { lnglat: ["121.47", "31.23"] },//上海
          { lnglat: ["106.55", "29.56"] },//重庆
        ]
        cluster = new AMap.MarkerCluster(this.map, points, {gridSize: gridSize});






      }).catch(e=>{
        console.log(e);
      })
    },



    // 发送请求给高德，返回经纬度地址对应地理信息
    getGeoInformationByIP(geoParam) {
      getGeoInformationByIp(geoParam).then((res) => {
        this.geoInformation = res;
       console.log(this.geoInformation)
      })
    }

  },
};
</script>
<style lang="scss" scoped>
#container{
  padding: 0px;
  margin: 0px;
  width: 100%;
  height: 550px;
}
#panel {
  position: fixed;
  background-color: white;
  max-height: 90%;
  overflow-y: auto;
  top: 10px;
  right: 10px;
  width: 280px;
}
#panel .amap-call {
  background-color: red;
  border-top-left-radius: 4px;
  border-top-right-radius: 4px;
}
#panel .amap-lib-walking {
  border-bottom-left-radius: 4px;
  border-bottom-right-radius: 4px;
  overflow: hidden;
}
.centermap {
  // 下外边距（与同级的下面元素的间隔距离）
  margin-bottom: 30px;

  // 标题-框架的宽高等相关设置
  .maptitle {
    height: 60px;
    display: flex;
    justify-content: center;
    padding-top: 10px;
    box-sizing: border-box;

    // 标题-字体的大小样式等设置
    .titletext {
      font-size: 28px;
      font-weight: 900;
      letter-spacing: 6px;
      background: linear-gradient(
        92deg,
        #0072ff 0%,
        #00eaff 48.8525390625%,
        #01aaff 100%
      );
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      margin: 0 10px;
    }

    // 标题-左右图片的相关设置
    .zuo,
    .you {
      background-size: 100% 100%;
      width: 29px;
      height: 20px;
      margin-top: 8px;
    }

    // 标题-图片的引入
    .zuo {
      background: url("../../assets/img/xiezuo.png") no-repeat;
    }
    .you {
      background: url("../../assets/img/xieyou.png") no-repeat;
    }
  }

  // 地图-框架的宽高等设置
  .mapwrap {
    height: 548px;
    width: 100%;
    // padding: 0 0 10px 0;
    box-sizing: border-box;
    position: relative;

    // 地图右上角-按钮的相关设置
    .quanguo {
      position: absolute;
      right: 20px;
      top: -46px;
      width: 80px;
      height: 28px;
      border: 1px solid #00eded;
      border-radius: 10px;
      color: #00f7f6;
      text-align: center;
      line-height: 26px;
      letter-spacing: 6px;
      cursor: pointer;
      box-shadow: 0 2px 4px rgba(0, 237, 237, 0.5),
        0 0 6px rgba(0, 237, 237, 0.4);
    }
  }
}
</style>
