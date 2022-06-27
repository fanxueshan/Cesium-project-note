
1. 初始化
```
   const viewer = new Cesium.Viewer('cesiumContainer', {一些配置项})
```
   >在地图上添加一个点
```
   const entity=viewer.entities.add({
        position:Cesium.Cartesian3.fromDegrees(16.39,39.91,400),
        point:{
            pixelSize:100,
            color:new Cesium.Color(0,1,0,1)
         }
    })
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
viewer.scene.globe.show=true //打开三维地球图层
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
10. 几种坐标及其之间的转换
   >1. 世界坐标（Cartesian3：笛卡尔空间直角坐标系）椭球中心为原点的空间直角坐标系中的一个点的坐标。例如： new Cesium.Cartesian3(0, 0, 6378137) // 北极
   >2. 经纬度地理坐标系，坐标原点在椭球的质心。
      >>经度： 大地子午面与本初子午面间的两面角，东正西负。
      >>纬度： 椭球面上某点的法线与赤道平面的夹角。北正南负。
      >>Cesuim中没有具体的经纬度对象，要得到经纬度首先需要计算为弧度，再进行转换
   >3. 弧度 Cartographic 例如：new Cesium.Cartographic(longitude, latitude, height)；弧度表示的经度、纬度和高度，实际的经纬度是角度
   >4. 转换公式如下

```
//经纬度转换为世界坐标
 Cesium.Cartesian3.fromDegrees(longitude, latitude, height, ellipsoid, result) ;
```
```
//世界坐标转换为经纬度
var ellipsoid=viewer.scene.globe.ellipsoid;
var cartesian3=new Cesium.cartesian3(x,y,z);
var cartographic=ellipsoid.cartesianToCartographic(cartesian3);
var lat=Cesium.Math.toDegrees(cartographic.latitude);
var lng=Cesium.Math.toDegrees(cartographic.longitude);
var alt=cartographic.height;
```
```
//弧度和经纬度
//经纬度转弧度：
Cesium.CesiumMath.toRadians(degrees) 
//弧度转经纬度：
Cesium.CesiumMath.toDegrees(radians)
``` 
 ```
//屏幕坐标和世界坐标相互转换
//屏幕转世界坐标
var pick1= new Cesium.Cartesian2(0,0);
var cartesian = viewer.scene.globe.pick(viewer.camera.getPickRay(pick1),viewer.scene);
//世界坐标转屏幕坐标
Cesium.SceneTransforms.wgs84ToWindowCoordinates(scene, Cartesian3);
```
11. 导入矢量数据
```
viewer.dataSources.add(
Cesium.GeoJsonDataSource.load("../Cesium-1.94.3/Apps/SampleData/ne_10m_us_states.topojson")
)
```
12、相机系统的使用
>1.setView
```
const position=Cesium.Cartesian3.fromDegrees(116.39,39.91,800);//设置一个位置
   viewer.camera.setView({
      destination:position,
      orientation:{
         heading:Cesium.Math.toRadians(0),
         pitch:Cesium.Math.toRadians(-20),
         roll:0
      }
 })
```
>2.flyTO
```
const position=Cesium.Cartesian3.fromDegrees(116.39,39.91,800);//设置一个位置
viewer.camera.flyTo({
   destination:position,
   orientation:{
      heading:Cesium.Math.toRadians(0),
      pitch:Cesium.Math.toRadians(-90),
      roll:0
   },
   duration:5
})
```
>3.lookAt
```
const position=Cesium.Cartesian3.fromDegrees(116.39,39.91,800);//设置一个位置
const center=Cesium.Cartesian3.fromDegrees(116.39,39.91);
        const heading=Cesium.Math.toRadians(50);
        const pitch=Cesium.Math.toRadians(-90);
        const range=2500;
        viewer.camera.lookAt(center,new Cesium.HeadingPitchRange(heading,pitch,range));
```
