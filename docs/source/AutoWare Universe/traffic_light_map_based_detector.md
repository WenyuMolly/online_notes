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

- 1. "input/vector_map" - MapBasedDetector::mapCallback

    <p align="center">
    <img src="https://user-images.githubusercontent.com/66779478/281627297-a30a19eb-e486-499a-986e-f72e2e553424.png">
    </p> 
  a. 输入消息（input_msg）中，首先创建一个新的Lanelet地图（lanelet_map_ptr_）。 
  b. 使用fromBinMsg函数从输入消息中解析出地图数据，并将其存储在lanelet_map_ptr_中。
  c. 查询地图中的所有车道（lanelets）和交通灯（traffic lights）。
  d. 所有找到的车道和交通灯都被存储在all_lanelets和all_lanelet_traffic_lights中。
  e. 对于找到的每个交通灯，检查每个交通灯是否是线段（LineString）。如果不是，那么将跳过并继续检查下一个交通灯。

- 2. "input/camera_info" - MapBasedDetector::cameraInfoCallback

    <p align="center">
    <img src="https://user-images.githubusercontent.com/66779478/281631343-004ea80c-95ac-44cd-a7ed-1d8ad7e98f7f.png">
    </p> 
  a. 输入消息（input_msg）中，首先创建一个新的Lanelet地图（lanelet_map_ptr_）。 
  b. 使用fromBinMsg函数从输入消息中解析出地图数据，并将其存储在lanelet_map_ptr_中。
  c. 查询地图中的所有车道（lanelets）和交通灯（traffic lights）。
  d. 所有找到的车道和交通灯都被存储在all_lanelets和all_lanelet_traffic_lights中。
  e. 对于找到的每个交通灯，检查每个交通灯是否是线段（LineString）。如果不是，那么将跳过并继续检查下一个交通灯。


# 四、问题

1. vibration这个参数怎么确定