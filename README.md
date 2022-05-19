
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
```
5. 打开场景服务下所有图层。
```
const promise = scene.open(场景地址); // 返回promise异步加载对象。
promise.then(function(layers){});
// 如果有多个则可以时用all的方法。
Cesium.when.all([promise...], function (layers) {})
```
6. 图层的显示、隐藏和删除
```
layer.visible=false // 隐藏图层
viewer.scene.layers.remove(图层名称) // 删除图层
```
7. WGS84坐标位置转换为屏幕坐标
```
const point = Cesium.SceneTransforms.wgs84ToWindowCoordinates(viewer.scene,position);
```
8. 种树
```
var s3mInstanceColc = new Cesium.S3MInstanceCollection(scene._context);
scene.primitives.add(s3mInstanceColc);
// 需要种树的坐标点
var defaultUrl = './models/springTree.s3m'; // 树的三维模型数据
const points = [
    { x: 0, y: 0, z: 6378137 }, // 北极
    { x: 0, y: 0, z: -6378137 }, // 南极
    { x: 0, y: 6378137, z: 0 }, // 亚洲下方
    { x: 0, y: -6378137, z: 0 }, // 北美下方
    { x: 6378137, y: 0, z: 0 }, // 非洲右侧 赤道和本初子午线的交点
    { x: -6378137, y: 0, z: 0 }, // 太平洋 180度经线，又称为对向子午线,东经180°/西经180°
]
points.forEach(v => {
    s3mInstanceColc.add(defaultUrl, {
        position: v, // 世界位置
        hpr: new Cesium.HeadingPitchRoll(0, 0, 0), // 旋转
        scale: new Cesium.Cartesian3(150000, 150000, 150000), // xyz缩放
        color: Cesium.Color.WHITE, //颜色
    });

})
```
9. 围绕指定地点旋转飞行
```
scene.camera.flyCircleLoop = true; // 是否循环
scene.camera.speedRatio = 5;     // 飞行速度
viewerRef.current.scene.camera.flyCircle(point); // point 围绕飞行的点
// 点击绘制一个中心点围绕飞行
var handlerPoint = new Cesium.DrawHandler(viewerRef.current, Cesium.DrawMode.Point);
handlerPoint.activate();
handlerPoint.drawEvt.addEventListener(function (result) {
var center = result.object.position;
viewerRef.current.scene.camera.flyCircle(center);
});
```
