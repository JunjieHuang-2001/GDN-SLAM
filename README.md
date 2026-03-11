# GDN-SLAM

GDN-SLAM is a high-precision visual SLAM system designed for complex dynamic environments. It is specifically optimized for practical challenges such as dynamic object interference, low-texture scenes, and motion blur. Built upon the stable core framework of ORB-SLAM2, GDN-SLAM integrates multi-feature constraints, dynamic object detection, and semantic optimization strategies, significantly improving localization accuracy and robustness in dynamic scenarios.

## Key Features

- **Dynamic Feature Filtering**: Dynamic feature points are accurately removed using YOLOv8-based object detection and geometric consistency constraints, thereby reducing motion interference.
- **Multi-Feature Fusion Optimization**: Hierarchical geometric constraints over point and line features are incorporated to alleviate feature matching ambiguity in low-texture regions.
- **Lightweight Semantic Enhancement**: A lightweight NeRF-based semantic constraint is introduced to assist pose optimization without requiring fine-grained texture reconstruction; only key semantic cues are extracted.
- **Real-Time Optimization**: Sparse temporal sampling and dynamically adaptive weighting strategies are adopted to control computational overhead while maintaining accuracy.
- **Compatibility with the ORB-SLAM2 Ecosystem**: GDN-SLAM is fully compatible with the dataset interfaces and configuration file formats of ORB-SLAM2, enabling easy migration and deployment.

## Environment Requirements

### Basic Dependencies (Required)

- **Operating System**: Ubuntu 18.04 / 20.04
- **Compiler**: GCC 5.4+ (with C++11/14 support)
- **ROS**: Melodic (Ubuntu 18.04) / Noetic (Ubuntu 20.04)

### Core Libraries

- **Pangolin** ≥ 0.6 (for visualization)
- **OpenCV** ≥ 3.4 (4.5+ recommended)
- **Eigen3** ≥ 3.3.7
- **g2o** (source-built, modified version included)
- **DBoW2** (bag-of-words model, included)

### Extended Dependencies (Dynamic Detection / Semantic Optimization)

- **YOLOv8** (for dynamic object detection)
- **PyTorch** ≥ 1.10 (optional, for lightweight NeRF-based semantic modeling)

## Installation

### 1. Install Basic Dependencies

```bash
sudo apt-get update && sudo apt-get install -y \
    build-essential cmake git libgtk2.0-dev \
    libboost-all-dev libssl-dev libusb-1.0-0-dev \
    libprotobuf-dev protobuf-compiler libgoogle-glog-dev \
    libgflags-dev libatlas-base-dev libeigen3-dev \
    libsuitesparse-dev ros-${ROS_DISTRO}-cv-bridge \
    ros-${ROS_DISTRO}-image-transport ros-${ROS_DISTRO}-tf
```

### 2. Install Pangolin

```bash
git clone https://github.com/stevenlovegrove/Pangolin.git
cd Pangolin
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make -j8
sudo make install
```

### 3. Install OpenCV

```bash
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git

cd opencv
mkdir build && cd build
cmake -DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib/modules \
      -DCMAKE_BUILD_TYPE=Release \
      -DCMAKE_INSTALL_PREFIX=/usr/local ..
make -j8
sudo make install
```

### 4. Clone and Build GDN-SLAM

```bash
mkdir -p ~/gdn_slam_ws/src
cd ~/gdn_slam_ws/src
git clone https://github.com/huangjunjiezuishai/GDN-SLAM.git
cd GDN-SLAM

chmod +x build.sh
./build.sh

chmod +x build_ros.sh
./build_ros.sh

cd ~/gdn_slam_ws
catkin_make -DCMAKE_BUILD_TYPE=Release
source devel/setup.bash
```

## Dataset Preparation

The TUM RGB-D dataset is used here as an example.

### 1. Download the sequence

```bash
mkdir -p ./datasets
wget https://vision.in.tum.de/rgbd/dataset/freiburg3/rgbd_dataset_freiburg3_walking_xyz.tgz
tar -xvf rgbd_dataset_freiburg3_walking_xyz.tgz -C ./datasets/
```

### 2. Generate the association file

```bash
python ./Examples/RGB-D/associate.py \
    ./datasets/rgbd_dataset_freiburg3_walking_xyz/rgb.txt \
    ./datasets/rgbd_dataset_freiburg3_walking_xyz/depth.txt \
    ./Examples/RGB-D/associations/fr3_walking_xyz.txt
```

## Running GDN-SLAM

### Option 1: Run with image sequence

```bash
roslaunch GDN-SLAM yolov8.launch
rosrun GDN-SLAM rgbd_tum Vocabulary/ORBvoc.txt Examples/RGB-D/tum_dynamic.yaml \
    ./datasets/rgbd_dataset_freiburg3_walking_xyz/ \
    ./Examples/RGB-D/associations/fr3_walking_xyz.txt
```

### Option 2: Run with a ROS bag file (if available)

```bash
roslaunch GDN-SLAM yolov8.launch
rosbag play ./datasets/rgbd_dataset_freiburg3_walking_xyz.bag
```

## Project Structure

```text
GDN-SLAM/
├── Examples/
├── Vocabulary/
├── Thirdparty/
├── include/
├── src/
├── build.sh
├── build_ros.sh
└── README.md
```

## Notes

- Please make sure that the ROS environment has been properly sourced before running the system.
- If you use the ROS bag mode, ensure that the corresponding `.bag` file is available locally.
- If your YOLOv8 launch file uses a different filename, replace `yolov8.launch` with the actual launch file name in your project.

## Acknowledgements

Our code builds on [ORB-SLAM2](https://github.com/raulmur/ORB_SLAM2).
