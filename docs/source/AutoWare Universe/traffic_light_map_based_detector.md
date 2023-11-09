### traffic_light_map_based_detector

# 一、收发topic

1. **该节点订阅topic**
    - "input/vector_map" - HD map
    - "input/camera_info" -相机内参等，用于初始化针孔摄像机模型以及后面生成camera pose
    - "input/route" -规划路线，如果有就只判断这个路线上的红绿灯，若无就判断所有的红绿灯
    <p align="center">
    <img src="https://user-images.githubusercontent.com/66779478/281624546-52fd35aa-ad72-4339-8b68-123ff4feeeb7.png">
    </p>   
    
2. **该节点发出去的topic**
    - "output/rois" -红绿灯的roi
    - "debug/markers" -用于debug的图片
    <p align="center">
    <img src="https://user-images.githubusercontent.com/66779478/281624853-ff69a8f4-43c2-430f-8a68-98759c6fe203.png">
    </p> 

# 二、参数

- 只有vibration参数，用于计算从map到camera的tf，以及roi
    <p align="center">
    <img src="https://user-images.githubusercontent.com/66779478/281625231-2102fae0-5cbb-40a5-89c7-6ff2a8407b67.png">
    </p> 


# 三、回调函数

- **1. "input/vector_map" - MapBasedDetector::mapCallback**

    <p align="center">
    <img src="https://user-images.githubusercontent.com/66779478/281627297-a30a19eb-e486-499a-986e-f72e2e553424.png">
    </p> 

  a. 输入消息（input_msg）中，首先创建一个新的Lanelet地图（lanelet_map_ptr_）。 
  b. 使用fromBinMsg函数从输入消息中解析出地图数据，并将其存储在lanelet_map_ptr_中。
  c. 查询地图中的所有车道（lanelets）和交通灯（traffic lights）。
  d. 所有找到的车道和交通灯都被存储在all_lanelets和all_lanelet_traffic_lights中。
  e. 对于找到的每个交通灯，检查每个交通灯是否是线段（LineString）。如果不是，那么将跳过并继续检查下一个交通灯。

- **2. "input/camera_info" - MapBasedDetector::cameraInfoCallback**

    <p align="center">
    <img src="https://user-images.githubusercontent.com/66779478/281631343-004ea80c-95ac-44cd-a7ed-1d8ad7e98f7f.png">
    </p> 

    <p align="center">
    <img src="https://user-images.githubusercontent.com/66779478/281723068-304ac7a3-728d-4936-8981-5d929ce80d2d.png">
    </p> 

  a. get transform from map frame to camera frame。 
  b. visible_traffic_lights : for each traffic light in map check if in range and in view angle of camera
  c. Get the ROI from the lanelet and the intrinsic matrix of camera to determine where it appears in image.

- **3. "input/route" - MapBasedDetector::routeCallback**

    <p align="center">
    <img src="https://user-images.githubusercontent.com/66779478/281726687-b148fbef-fdd2-46a1-bdc0-a9c4a0fb3eb2.png">
    </p> 

  a. 检查是否接收到地图（lanelet_map_ptr_）。如果没有接收到地图，将发出一个警告并返回。
  b. 从输入消息的各个段和原语中提取出路线车道（route_lanelets）。
  c. 对于每个段中的每个原语，它尝试从地图中获取对应的车道。如果找不到对应的原语，将发出一个错误并返回。
  d. 查询出路线车道中的所有交通灯（route_lanelet_traffic_lights）。
  e. 对于每个交通灯，检查其是否为LineString类型（isLineString()）。如果不是，将跳过并继续下一个交通灯。
  f. 对于每个是LineString类型的交通灯，将这个交通灯加入到路线交通灯集合（route_traffic_lights_ptr_）中。

# 四、问题

1. vibration这个参数怎么确定