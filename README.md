
1. 初始化
```
   const viewer = new Cesium.Viewer('cesiumContainer', {一些配置项})
```
2. 更换世界地图
```
 viewer.imageryLayers.addImageryProvider(new Cesium.SingleTileImageryProvider({
        url: './assets/image/world-bg.jpg'
 }));
```
3. 添加天空盒子
```
scene.skyBox = new Cesium.SkyBox({
        sources: {
          positiveX:
            './assets/image/Right.jpg',
          negativeX:
            './assets/image/Left.jpg',
          positiveY:
            './assets/image/Front.jpg',
          negativeY:
            './assets/image/Back.jpg',
          positiveZ:
            './assets/image/Up.jpg',
          negativeZ:
            './assets/image/Down.jpg',
        },
})
```
4. 添加光源
```
   let point = { x: 6377163, y: -420, z: -344 }
   let position = new Cesium.Cartesian3(point.x + 300, point.y - 300, point.z - 300);
   let targetPosition = new Cesium.Cartesian3(point.x, point.y, point.z);
   let options = {
     intensity: 0.6
   };
   let position1 = new Cesium.Cartesian3(point.x + 800, point.y - 250, point.z - 250);
   let options1 = {
     targetPosition: targetPosition,
     intensity: 0.6
   };
   let directionalLight_v = new Cesium.DirectionalLight(position, options);
   let directionalLight_h1 = new Cesium.DirectionalLight(position1, options1);
   scene.addLightSource(directionalLight_v);
   scene.addLightSource(directionalLight_h1);
