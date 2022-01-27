<!--
 * @Description: 
 * @LastEditors: lianghui
 * @LastEditTime: 2022-01-27 11:01:59
-->
<template>
  <div id="app">
    <div class="btn-group">
      <div class="narrow-btn" @click="handleNarrow">缩小</div>
      <div class="enlarge-btn" @click="handleEnlarge">放大</div>
    </div>
    <iframe
      id="maps"
      class="mapiframe"
      frameborder="no"
      scrolling="no"
      allowtransparency="true"
    ></iframe>
  </div>
</template>

<script>
import icon1 from "./assets/images/icon1.png";
import icon2 from "./assets/images/icon2.png";

export default {
  name: "App",
  data() {
    return {
      cmap: null, // 地图实例
      picSize: 22, // 图片尺寸
      pointData: [
        {
          id: 2,
          x: -1800,
          y: -1800,
          pointType: "one",
        },
        {
          id: 3,
          x: -2000,
          y: -2500,
          pointType: "two",
        },
        {
          id: 4,
          x: -3500,
          y: -3500,
          pointType: "",
        },
      ],
    };
  },
  methods: {
    // 初始地图
    initMap() {
      this.cmap = new CityGis.Bridge({
        id: "maps",
        // city-市级 18-区 大于两位数-街道
        url: "http://10.81.71.51/citygis/areamap/WidgetPages/WidgetGIS.html?code=04",
        onReady: function () {
          this.Invoke({
            ActionName: "goToPosition",
            Parameters: {
              position: {
                x: 0,
                y: 0,
                z: 1,
              },
              marker: {
                size: 0,
                colorType: 0,
              },
              zoom: 3,
            },
          });
        },
      });
      this.drawPoints(this.pointData);
    },
    //地图信息撒点
    drawPoints(data) {
      var _this = this;
      // this.clearMap();
      // 定位闪烁
      this.cmap.Invoke({
        ActionName: "ShowData",
        Parameters: {
          name: "gar_layer",
          data: {
            content: [...data],
            parsedata: "function(d){return d}",
            parsegeometry:
              "function(item){return {x:Number(item.x),y:Number(item.y)}}",
          },
          popupEnabled: false, //弹窗
          legendVisible: false, //图例
          isLocate: true, //地图是否缩放到最大外接矩形
          renderer: {
            type: "unique-value",
            field: "pointType",
            defaultLabel: "其他",
            defaultSymbol: {
              type: "simple-marker",
              size: _this.picSize - 5,
              color: "#ff3c00",
              outline: {
                width: 1,
                color: "white",
              },
            },
            uniqueValueInfos: [
              {
                value: "one",
                label: "垃圾非离线",
                symbol: {
                  type: "picture-marker",
                  url: icon1,
                  width: _this.picSize + "px",
                  height: _this.picSize + "px",
                },
              },
              {
                value: "two",
                label: "垃圾离线",
                symbol: {
                  type: "picture-marker",
                  url: icon2,
                  width: _this.picSize + "px",
                  height: _this.picSize + "px",
                },
              },
            ],
          },
        },
      });
    },
    // 缩小地图点位
    handleNarrow() {
      if (this.picSize < 12) {
        this.picSize = 12;
      } else {
        this.picSize -= 15;
      }
      this.drawPoints(this.pointData);
      this.cmap.Invoke({ ActionName: "ZoomOut" }); // 缩小
    },
    // 放大地图点位
    handleEnlarge() {
      if (this.picSize < 80) {
        this.picSize += 15;
      }
      this.drawPoints(this.pointData);
      this.cmap.Invoke({ ActionName: "ZoomIn" }); // 放大
    },
  },
  mounted() {
    this.initMap();
  },
};
</script>

<style lang="less">
body,
html {
  height: 100%;
  padding: 0;
  margin: 0;
}
#app {
  position: relative;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 100%;
}
.mapiframe {
  width: 100%;
  height: 100%;
}
.btn-group {
  position: absolute;
  top: 20px;
  right: 100px;
  .narrow-btn {
    text-align: center;
    position: absolute;
    top: 20px;
    right: 164px;
    cursor: pointer;
    color: aliceblue;
    width: 70px;
    height: 30px;
    line-height: 30px;
    font-size: 18px;
    background: rgb(0, 104, 196);
    border-radius: 4px;
  }
  .enlarge-btn {
    text-align: center;
    position: absolute;
    top: 20px;
    right: 80px;
    cursor: pointer;
    color: aliceblue;
    width: 70px;
    height: 30px;
    line-height: 30px;
    font-size: 18px;
    background: rgb(0, 104, 196);
    border-radius: 4px;
  }
}
</style>
