# GDN-SLAM

GDN-SLAM is a visual SLAM system designed for complex and dynamic environments. Built upon the ORB-SLAM2 framework, it aims to improve localization robustness and mapping accuracy under challenging conditions such as dynamic interference, low-texture regions, and motion blur.

The system integrates multi-feature geometric constraints and lightweight semantic enhancement strategies to improve feature reliability and pose estimation performance in dynamic scenes.

---

## Related Manuscript

This repository contains the implementation associated with the following manuscript:

**Enhancing Dynamic SLAM Performance through Multi-Feature Geometric Consistency and NeRF-Based Optimization**

**Authors:** Huilin Liu, Junjie Huang, Lunqi Yu, Chang Su, Wanqi Ma

**Journal:** *The Visual Computer*  
**Status:** Submitted / revised for resubmission

This public repository is provided to improve the transparency and reproducibility of the submitted work.

---

## Key Features

- **Dynamic Scene Robustness**  
  GDN-SLAM is designed for dynamic environments and improves feature reliability through geometric consistency constraints and front-end optimization strategies.

- **Multi-Feature Fusion Optimization**  
  Point and line features are jointly exploited to improve feature association robustness, especially in low-texture and geometrically ambiguous regions.

- **Lightweight Semantic Enhancement**  
  A lightweight semantic optimization strategy is incorporated to assist pose estimation without requiring full fine-grained scene reconstruction.

- **Real-Time Oriented Design**  
  Sparse temporal processing and adaptive optimization strategies are adopted to balance computational efficiency and localization accuracy.

- **Compatibility with ORB-SLAM2 Ecosystem**  
  The project maintains compatibility with ORB-SLAM2-style vocabulary files, dataset interfaces, and configuration files, facilitating migration and deployment.

---

## Repository Structure

```text
GDN-SLAM/
├── Examples/
├── Thirdparty/
├── cmake_modules/
├── include/
├── src/
├── Vocabulary/
├── .gitignore
├── CMakeLists.txt
├── Dependencies.md
├── LICENSE.txt
├── License-gpl.txt
├── README.md
├── build.sh
├── build_ros.sh
├── evaluate_ate.py
├── evaluate_rpe.py

Important:
Please ensure that the vocabulary directory name is consistent with the build scripts.
If build.sh uses Vocabulary, the folder should also be named Vocabulary.

Environment Requirements
Basic Dependencies

Operating System: Ubuntu 18.04 / 20.04

Compiler: GCC 5.4+ (with C++11/14 support)

ROS: Melodic (Ubuntu 18.04) / Noetic (Ubuntu 20.04, recommended)

Core Libraries

Pangolin ≥ 0.6

OpenCV ≥ 3.4 (4.5+ recommended)

Eigen3 ≥ 3.3.7

g2o (modified version included in Thirdparty)

DBoW2 (included in Thirdparty)

Optional Dependencies

PyTorch ≥ 1.10 (optional, if semantic-related extensions are enabled)

Installation
1. Install Basic Dependencies
sudo apt-get update && sudo apt-get install -y \
    build-essential cmake git libgtk2.0-dev \
    libboost-all-dev libssl-dev libusb-1.0-0-dev \
    libprotobuf-dev protobuf-compiler libgoogle-glog-dev \
    libgflags-dev libatlas-base-dev libeigen3-dev \
    libsuitesparse-dev ros-${ROS_DISTRO}-cv-bridge \
    ros-${ROS_DISTRO}-image-transport ros-${ROS_DISTRO}-tf
2. Install Pangolin
git clone https://github.com/stevenlovegrove/Pangolin.git
cd Pangolin
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make -j8
sudo make install
3. Install OpenCV
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git

cd opencv
mkdir build && cd build
cmake -DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib/modules \
      -DCMAKE_BUILD_TYPE=Release \
      -DCMAKE_INSTALL_PREFIX=/usr/local ..
make -j8
sudo make install
4. Clone This Repository
mkdir -p ~/gdn_slam_ws/src
cd ~/gdn_slam_ws/src
git clone https://github.com/JunjieHuang-2001/GDN-SLAM.git
cd GDN-SLAM
5. Prepare the Vocabulary File

Please ensure that the ORB vocabulary archive is available under:

Vocabulary/ORBvoc.txt.tar.gz

Then extract it before running the system. If needed, build.sh will also extract the vocabulary archive automatically.

6. Build GDN-SLAM
chmod +x build.sh
./build.sh

If ROS support is needed:

chmod +x build_ros.sh
./build_ros.sh

cd ~/gdn_slam_ws
catkin_make -DCMAKE_BUILD_TYPE=Release
source devel/setup.bash
Dataset Preparation

The project can be evaluated on public datasets such as:

TUM RGB-D Dataset

Bonn RGB-D Dynamic Dataset

TUM RGB-D Dataset

Download link:

https://vision.in.tum.de/data/datasets/rgbd-dataset

Example:

mkdir -p ./datasets
wget https://vision.in.tum.de/rgbd/dataset/freiburg3/rgbd_dataset_freiburg3_walking_xyz.tgz
tar -xvf rgbd_dataset_freiburg3_walking_xyz.tgz -C ./datasets/

If the association script is provided under Examples/RGB-D/associate.py, you can generate the association file using:

python Examples/RGB-D/associate.py \
    ./datasets/rgbd_dataset_freiburg3_walking_xyz/rgb.txt \
    ./datasets/rgbd_dataset_freiburg3_walking_xyz/depth.txt \
    ./Examples/RGB-D/associations/fr3_walking_xyz.txt

Please adjust the path according to the actual repository contents.

Running GDN-SLAM
RGB-D Example

After successful compilation, an RGB-D executable is generated in the corresponding example directory.

Example command:

./Examples/RGB-D/rgbd_tum Vocabulary/ORBvoc.txt Examples/RGB-D/tum_dynamic.yaml \
./datasets/rgbd_dataset_freiburg3_walking_xyz/ \
./Examples/RGB-D/associations/fr3_walking_xyz.txt

Please make sure that:

Vocabulary/ORBvoc.txt has been extracted from ORBvoc.txt.tar.gz

Examples/RGB-D/tum_dynamic.yaml exists in your repository

the dataset path is correct

Other Supported Modes

Depending on the provided example source files, the repository may also support:

Monocular mode

Stereo mode

RGB-D mode

Please refer to the Examples/ directory for the available executables and dataset-specific usage.

Trajectory Evaluation

This repository includes trajectory evaluation scripts:

evaluate_ate.py

evaluate_rpe.py

These scripts are adapted from widely used RGB-D trajectory evaluation tools.

Important Note

If the scripts are written in Python 2 style, please run them with Python 2.7.

Example
python2 evaluate_ate.py groundtruth.txt estimated.txt
python2 evaluate_rpe.py groundtruth.txt estimated.txt

If you have already migrated them to Python 3, replace python2 with python3.

Reproducibility Notes

To improve reproducibility, please ensure the following:

the vocabulary file is placed in the correct directory

the directory name Vocabulary matches the build script

the OpenCV, Pangolin, Eigen, and ROS environments are correctly configured

the dataset paths and YAML configuration files are consistent with the README commands

all required files under Examples/, Thirdparty/, and Vocabulary/ are present

Citation

If you find this repository useful, please cite the related manuscript:

@article{huang2026gdnslam,
  title={Enhancing Dynamic SLAM Performance through Multi-Feature Geometric Consistency and NeRF-Based Optimization},
  author={Huang, Junjie and Liu, Huilin and Yu, Lunqi and Su, Chang and Ma, Wanqi},
  journal={The Visual Computer},
  year={2026},
  note={Manuscript submitted / under review}
}
Acknowledgements

This project is built upon ORB-SLAM2 and also benefits from several open-source libraries and tools, including:

ORB-SLAM2

Pangolin

OpenCV

Eigen3

DBoW2

g2o

We sincerely thank the original authors for their open-source contributions.

License

Please refer to the following files for license information:

LICENSE.txt

License-gpl.txt

For third-party code and dependency licenses, please also refer to:

Dependencies.md

Contact

For questions regarding the code or manuscript, please contact the repository maintainer via GitHub issues.
