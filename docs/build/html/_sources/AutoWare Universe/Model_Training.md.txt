### YoloV5 模型训练

# 一、环境准备

1. **检查显卡驱动**：
    ```bash
    nvidia smi
    ```
    - 如果显示“Command 'nvidia-smi' not found, ...”，说明没有安装显卡驱动
    - 点击左下角的“9个小方块”图标，然后搜索“软件和更新”（Software & Update），查看下面的附加驱动（Additional Drivers），选择一个进行安装（这步选择哪个可能需要进一步结合具体情况选择，我的是RTX3090+ubuntu20.04，选择了最前面的470）
    - 重启，然后再运行“nvidia-smi”指令来看驱动是否安装成功 (有如下图返回一个表格就代表驱动成功了)，且右上角后面跟着推荐的CUDA版本号。
    <p align="center">
    <img src="https://user-images.githubusercontent.com/66779478/280258406-c8908bcd-766a-44ee-92e0-57e59797bcfe.png">
    </p>   
    

    - 可能需要的其他知识：
    - 如何看显卡型号

    ```bash
    lspci | grep VGA
    ```
    - NVIDIA 后面会跟着ID，可以去nvidia官网查ID对应的显卡型号 

2. **安装CUDA**


# 二、训练流程

1. **数据准备**：
    - 搜集需要的图片，例如红绿灯、行人、车辆的图片。
    - 使用标注工具（例如[labelImg](https://github.com/tzutalin/labelImg)）为图片中的对象进行标注。YOLOv5需要的是YOLO格式的标注文件，即每张图片都会有一个对应的`.txt`文件，其中包含对象的类别和坐标。

2. **数据组织**：
    - 将所有图片放在一个目录中，例如`images/`，并将所有的`.txt`标注文件放在另一个目录中，例如`labels/`。
    - 分割数据集为训练集和验证集。

3. **配置文件**：
    - 创建一个`.yaml`文件，指定类别和数据的路径。例如`data.yaml`:
      ```yaml
      train: path/to/train/images/
      val: path/to/val/images/
      nc: 3  # 类别数量，例如：红绿灯、行人、车辆
      names: ['traffic_light', 'pedestrian', 'vehicle']
      ```

4. **安装依赖并克隆YOLOv5仓库**：

    ```bash
    git clone https://github.com/ultralytics/yolov5.git
    cd yolov5
    pip install -U -r requirements.txt
    ```

5. **训练模型**：

    ```bash
    python train.py --img 640 --batch 16 --epochs 100 --data data.yaml --cfg models/yolov5s.yaml --weights yolov5s.pt
    ```
    这里用的是YOLOv5的小模型配置`yolov5s.yaml`和预训练权重`yolov5s.pt`。你可以根据需要选择其他模型配置。

6. **评估模型**：
    YOLOv5的训练脚本在训练结束后会自动评估模型。可以查看`runs/train/exp`目录下的结果，其中包括了损失、准确率、混淆矩阵等信息。

7. **使用训练好的模型进行推断**：
    ```bash
    python detect.py --source path/to/test/images/ --weights runs/train/exp/weights/best.pt --conf 0.4
    ```

8. **优化**：
    - 如果模型的性能不理想，可以尝试更多的数据增强、更长的训练周期、使用更大的模型配置等方法进行优化。



# 三、开源数据集

以下是一些包括红绿灯、车道线、行人和斑马线的标注的公开数据集（for research purpose only）：

1. **Cityscapes Dataset**: 这是一个大型的城市场景数据集，其中包括车道线、行人、车辆等多个类别的标注。它包括精细的语义分割标注，但需要注意的是，它不提供红绿灯的实例级别标注。

2. **Berkeley DeepDrive (BDD100K) Dataset**: 这是一个非常大的驾驶相关数据集，其中包括多个类别，如车道线、行人、车辆等。它为图像提供了边界框和语义分割标注。

3. **LISA Traffic Light Dataset**: 这是一个专门用于红绿灯检测的数据集。

4. **Tusimple Lane Detection Challenge**: 这是一个专门用于车道线检测的数据集。

5. **Apolloscape**: 该数据集包括多种驾驶相关的任务，其中包括车道线和语义分割。

6. **Mapillary Vistas Dataset**: 这是一个街景数据集，其中包括多个类别的标注，包括行人、车辆、红绿灯和斑马线。

以下是这些数据集的官方链接：

1. **Cityscapes Dataset**: 
    - [Cityscapes Dataset Official Link](https://www.cityscapes-dataset.com/)

2. **Berkeley DeepDrive (BDD100K) Dataset**: 
    - [BDD100K Official Link](https://bdd-data.berkeley.edu/)

3. **LISA Traffic Light Dataset**: 
    - [LISA Dataset Official Link](http://cvrr.ucsd.edu/LISA/lisa-traffic-sign-dataset.html)

4. **Tusimple Lane Detection Challenge**: 
    - [TuSimple Dataset Official Link](https://benchmark.tusimple.ai/#/)

5. **Apolloscape**: 
    - [Apolloscape Official Link](http://apolloscape.auto/)

6. **Mapillary Vistas Dataset**: 
    - [Mapillary Vistas Dataset Official Link](https://www.mapillary.com/dataset/vistas)

