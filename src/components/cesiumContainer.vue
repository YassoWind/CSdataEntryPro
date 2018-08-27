<template>
   <div class="main">
    <div id="cesiumContainer">
    </div>
    <div class="tool">
      <span v-on:click="pick()">点坐标</span><span>距离量测</span><span>面积量测</span>
    </div>

   </div>
</template>

<script>
import Cesium from "cesium/Cesium";
import widgets from "cesium/Widgets/widgets.css";

export default {
  name: "cesiumContainer",
  data() {
    return {
      viewer: "",
      handler: "",
      tileset:""
    };
  },
  mounted() {
    this.viewer = new Cesium.Viewer("cesiumContainer", {
      imageryProvider: new Cesium.UrlTemplateImageryProvider({
        url: "http://mt1.google.cn/vt/lyrs=s&hl=zh-CN&x={x}&y={y}&z={z}&s=Gali"
      }),
      baseLayerPicker: false,
      homeButton: false,
      navigationHelpButton: false,
      // geocoder: true,
      sceneModePicker: false,
      timeline: false,
      animation: false,
      vrButton: false
    });
      //加载桥的底图模型
      var that = this;
    this.tileset = this.viewer.scene.primitives.add(
        new Cesium.Cesium3DTileset({
          url:"./static/data/machi/Production_6.json",
          //loadSiblings: true,
          // debugShowGeometricError: true,
          maximumScreenSpaceError: 50,
          dynamicScreenSpaceError: true,
          show: true
        })
      );

      //在加载时执行跳转事件
    this.tileset.readyPromise
        .then(function() {
          console.log("jiazai");
          that.viewer.camera.flyTo({
            destination: Cesium.Cartesian3.fromDegrees(114.22627866701363, 30.651767986395594,100),
            orientation: {
                heading : Cesium.Math.toRadians(0), //默认值
                pitch : Cesium.Math.toRadians(-90), // 默认值
                roll : 0.0 //默认值
            }    
        });
        })
        .otherwise(function(error) {
          throw error;
        });
    
  },
  methods: {
    pick: function() {
      console.log(this.viewer);
      var that = this;
      this.handler = new Cesium.ScreenSpaceEventHandler(
        that.viewer.scene.canvas
      );
      this.handler.setInputAction(function(movement) {
        if (that.viewer.scene.mode !== Cesium.SceneMode.MORPHING) {
          var pickedObject = that.viewer.camera.pickEllipsoid(
            movement.endPosition
          );
          console.log(pickedObject);
        }
      }, Cesium.ScreenSpaceEventType.MOUSE_MOVE);
    }
  }
};
</script>

<style>
.main {
  width: 100%;
  height: 100%;
}
.cesium-viewer-bottom {
  display: none !important;
}
.main .tool {
  width: 300px;
  height: 50px;
  position: absolute;
  top: 10px;
  left: 10px;
  z-index: 8888;
  color: #fff;
}

.main .tool span {
  display: inline-block;
  margin-right: 20px;
}
</style>