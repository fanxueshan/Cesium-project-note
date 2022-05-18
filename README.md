
1. 初始化
```
   const viewer = new Cesium.Viewer('cesiumContainer', {一些配置项})
```
2. 更换世界地图
```
 viewer.imageryLayers.addImageryProvider(new Cesium.SingleTileImageryProvider({
        url: './assets/image/world-bg.jpg'
 }));
