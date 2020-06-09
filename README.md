# yolact_ros

A ROS wrapper for [YOLACT](https://github.com/dbolya/yolact). The package has been tested on Ubuntu-16.4 with ROS-Kinetic and Python 3.6.

## Related packages

[yolact_ros_msgs](https://github.com/Eruvae/yolact_ros_msgs): Provides messages for publishing the detection results.

## Installation

The installation is not very straight forward since YOLACT requires python 3, which is not directly supported by ROS-Kinetic. However, The following steps provide a detailed guide to install this package.
1. Install Python 3.6 if not already installed. For detailed instructions check [this](https://github.com/Sushant-Chavan/XPS-Ubuntu16-04-Setup/blob/master/README.md#python3-and-36-setup).
2. If you prefer to use a virtual for the Python3.6, follow the instructions from [here](https://github.com/Sushant-Chavan/XPS-Ubuntu16-04-Setup/blob/master/README.md#create-a-python-36-virtual-environment-requires-python-36-to-be-installed-beforehand-as-described-previously).
3. Install PyTorch using these [instructions](https://github.com/Sushant-Chavan/XPS-Ubuntu16-04-Setup/blob/master/README.md#install-pytorch-version-15).
4. If you are using a virtual env for Python3.6, deactivate it using the command `deactivate` in the terminal.
5. Install ROS-Kinetic by following steps from [here](https://github.com/Sushant-Chavan/XPS-Ubuntu16-04-Setup/blob/master/README.md#install-ros).
6. Create a new catkin workspace for YOLACT. **Since YOLOACT requires Python3, it is highly recommended to create a new workspace to avoid disturbing any other packages that have dependencies on Python2.7**. 
    ```
    sudo apt-get install python-catkin-tools python3-dev python3-catkin-pkg-modules python3-numpy python3-yaml ros-kinetic-cv-bridge
    mkdir -p ~/catkin_yoloact_ws/src
    cd ~/catkin_yoloact_ws
    catkin init
    catkin config -DPYTHON_EXECUTABLE=/usr/bin/python3 -DPYTHON_INCLUDE_DIR=/usr/include/python3.6m -DPYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.6m.so
    catkin build
    source ~/catkin_yoloact_ws/devel/setup.bash
    ```
7. Clone and build cv_bridge with Python 3. This is inspired from [here](https://stackoverflow.com/a/50291787).
   1. Install opencv for Python3
        ```
        sudo pip3 install opencv-python
        ```
    1. Clone cv_bridge repository inside the new catkin workspace
        ```
        cd ~/catkin_yoloact_ws
        git clone https://github.com/ros-perception/vision_opencv.git src/vision_opencv
        ```
    2. Find version of cv_bridge using the below command.
        ```
        apt-cache show ros-kinetic-cv-bridge | grep Version
        ```
    3. Assuming the output of the previous command is `Version: 1.12.8-0xenial-20180416-143935-0800`, i.e. `cv_bridge` version is `1.12.8`, checkout the relevant commit using the below commands
        ```
        cd src/vision_opencv/
        git checkout 1.12.8
        cd ../../
        ```
    4. Use the below command to open CMakeLists.txt and change the line `find_package(Boost REQUIRED python3)` to `find_package(Boost REQUIRED python-py35)`. Then close the editor.
        ```
        gedit src/vision_opencv/cv_bridge/CMakeLists.txt
        ```
    5. Finally build and source the workspace
        ```
        catkin build
        source ~/catkin_yoloact_ws/devel/setup.bash
        ```
8. Clone and build this ROS wrapper and related messages packages
    ```
    cd ~/catkin_yoloact_ws/src
    git clone git@github.com:Sushant-Chavan/yolact_ros.git
    git clone git@github.com:Eruvae/yolact_ros_msgs.git
    cd yolact_ros
    git submodule update --init
    cd ~/catkin_yoloact_ws
    catkin build
    source ~/catkin_yoloact_ws/devel/setup.bash
    ```
9. Install the packages required by YOLACT. If using a virtual env, first activate it and then execute the below commands.
    ```
    pip3 install cython
    pip3 install opencv-python pillow pycocotools matplotlib rospkg empy
    ```
10. Download the required model weights. The download links are available [here](https://github.com/dbolya/yolact#evaluation). For the default model, atleast download the model weight `yolact_base_54_800000.pth` to `~/catkin_yoloact_ws/src/yolact_ros/scripts/yolact/weights/yolact_base_54_800000.pth`
11. Check if the rosnode for YOLACT is setup properly and runs without any errors.
    ```
    # In a separate terminal start roscore
    roscore

    # In another terminal start the ros node. If using a virtual env, activate it before executing the below command.
    rosrun yolact_ros yolact_ros
    ```
