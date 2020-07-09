# model_convert
不同的深度学习框架下训练好的模型保存的格式不同，对于模型的改进和部署来说都是一个问题，故而将模型互相转换就尤为重要。本项目主要记录本人在模型转换过程中的一些记录。

## ubuntu16安装caffe
如果Ubuntu版本是>= 17.04的，就可以使用以下的方式安装Caffe，注意安装的是Python 3的版本。<br>
```Bash
apt install caffe-cpu
```
### 安装依赖环境
首先我们要安装依赖环境，依赖环境有点多，需要保证都安装了，以免在编译的时候出错。如果之前安装过了，重复执行命令也没有问题的。<br>
```Bash
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev  
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
sudo apt-get install libopenblas-dev liblapack-dev libatlas-base-dev
sudo apt-get install libatlas-base-dev
sudo apt-get install python-numpy
sudo apt-get install libhdf5-serial-dev
sudo apt-get install python-dev
sudo apt install python-pip
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
sudo apt-get install git cmake build-essential
pip install scikit-image
```
### 修改编译文件
```Bash
# 切换到opt目录下
cd /opt
# 克隆caffe源码
git clone git://github.com/BVLC/caffe.git
# 切入到源码根目录
cd caffe/
# 复制官方提供的编译配置文件例子
cp Makefile.config.example Makefile.config
# 开始编写配置信息
vim Makefile.config
```
修改这个配置文件如下：<br>
* 把第8行的注释取消，编译CPU版本的Caffe，即如下：<br>
```Bash
CPU_ONLY := 1
```
* 然后版96、97、98行，改成如下：<br>
```Bash
# Whatever else you find you need goes here.
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial
```
### 开始编译
现在就可以开始编译了，前面两条是编译源码，后面两条是编译测试，-j4是表示使用4个线程并行编译，加快编译速度。
```Bash
make -j4 pycaffe
make -j4 all
make -j4 test
make -j4 runtest
```
### 添加环境变量
使用命令`vim /etc/profile`，在该文件的最后加上下面的这行代码。
```Bash
export PYTHONPATH=/opt/caffe/python:$PYTHONPATH
```
我们可以简单测试一下是否安装成功了，正常的话是可以输出caffe的版本信息的。<br>
```python
# python
Python 2.7.12 (default, Dec  4 2017, 14:50:18)
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import caffe
>>> caffe.__version__
```
