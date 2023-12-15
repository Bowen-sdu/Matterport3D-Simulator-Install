# Matterport3D-Simulator-Install
该仓库为MP3D模拟器的安装过程，不使用Docker，在自己的环境下编译安装simulator。服务器版本为Ubuntu22.04

## 先决条件
* Ubuntu==22.04,其它版本也可
* Nvidia-driver with CUDA installed
* C++ compiler with C++11 support
* CMake >= 3.10
* OpenCV >= 2.4 including 3.x
* OpenGL
* GLM
* Numpy

除了OpenCV和OpenGL，其它软件，服务器上一般都有安装，这里只介绍OpenCV和OpenGL的安装方式。

## 数据集下载
首先要签署一份agreement，然后发到指定的邮箱，过一两个小时就可以收到回复。回复中会附有一个py程序，可以使用这个py程序来下载数据集。

[下载网址](https://niessner.github.io/Matterport/)

数据集下载指令：

完整的Matterport3D数据集有1.3T大小，若只是想让这个模拟器跑起来，可以只下载**matterport_skybox_images**,具体指令如下：
```
python download_mp.py -o [你的下载路径] --type matterport_skybox_images
```
这里默认把90个建筑的数据都下载下来，大小一共是20G左右。

如果要下载深度数据，则指令如下：
```
python download_mp.py -o ../VLN/data --type undistorted_depth_images undistorted_camera_parameters
```
注意：这里python要用python2.7版本来运行

数据集下载完成后，将其解压，[解压代码](https://pan.baidu.com/s/1fu7TxcTDEg6dgfNl1PZ50A)，提取码：MP3D

## OpenGL安装
参考官网安装方法即可：
```
sudo apt-get install build-essential 
sudo apt-get install build-essential libgl1-mesa-dev
sudo apt-get install libglew-dev libsdl2-dev libsdl2-image-dev libglm-dev libfreetype6-dev
sudo apt-get install libglfw3-dev libglfw3
```

## OpenCV安装
* 下载OpenCV安装包，注意安装版本>=2.4或者3.x，不要装4.x。
这里选择安装版本为3.4.16，[官网链接](https://opencv.org/releases/)，[百度网盘](https://pan.baidu.com/s/1SW-CUVLRzEEBp3h3Uwm8pA),提取码：MP3D
* 在opencv文件夹下新建build文件夹
```
cd opencv
mkdir build
cd build
```
* 在build目录下编译并安装opencv
```
sudo cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local ..
sudo make -j8
sudo make install
```

## MP3D模拟器安装
* 下载安装包
[官网链接](https://github.com/peteanderson80/Matterport3DSimulator)
[百度网盘](https://pan.baidu.com/s/1MU2G0N0qNSiEqxdQdWS3bA),提取码：MP3D
* 解压安装包后进入MatterPort3DSimulator目录，并在其中建立build目录
```
mkdir build
cd build
```
* 使用自己的python环境来编译，PYTHON_EXECUTABLE为你的python路径
```
cmake -DPYTHON_EXECUTABLE=../miniconda3/envs/python39/bin/python3 -DEGL_RENDERING=ON ..
```
* 若报错`No package 'jsoncpp' found`，安装该包
![jsoncpp错误](https://github.com/Bowen-sdu/Matterport3D-Simulator-Install/assets/57526757/3c90b274-37e2-4001-828c-ad47b5fa0794)

```
sudo apt-get install libjsoncpp-dev
```
* 若报错`No package 'epoxy' found`，安装该包
![epoxy错误](https://github.com/Bowen-sdu/Matterport3D-Simulator-Install/assets/57526757/41e92c65-11cf-4db9-a907-91ac49de943a)

```
sudo apt-get install libepoxy-dev
```
* 接下来编译得到的MakeFile文件
```
make
```
这里会报数组定义错误，这是因为include/Catch.hpp文件中定义的数组有误。
![c++数组定义错误](https://github.com/Bowen-sdu/Matterport3D-Simulator-Install/assets/57526757/00d26fb3-7571-45d6-a303-da58c0652e31)
错误定义的数组为
```
char FatalConditionHandler::altStackMem[SIGSTKSZ] = {};
```
应修改为
```
char* FatalConditionHandler::altStackMem = new char[SIGSTKSZ];
```
另外第Catch.hpp中6465行对altStackMem的声明也需要修改，原来为
```
static char altStackMem[];
```
修改为
```
static char* altStackMem;
```

之后进行make即可。

* 最后把编译生成的MatterPort3DSimulator/build/MatterSim.cpython-39-x86_64-linux-gnu.so文件复制到conda对应环境的库里./miniconda3/envs/python39/lib/python3.9/site-packages。

## 验证
* 验证测试用例
```
./build/tests ~Timing
./build/tests Timing
```
* 开启交互页面
```
python3 src/driver/driver.py
```



