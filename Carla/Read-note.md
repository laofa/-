# Carla Read-note  
### 1.end-to-end model  
end-to-end在不同应用场景下有不同的具体诠释，对于视觉领域而言，end-end一词多用于基于视觉的机器控制方面，具体表现是，
神经网络的输入为原始图片，神经网络的输出为（可以直接控制机器的）控制指令。简单地来说就是输入的是原始数据，输出的是最后的结果，
我们减少了对raw data(原始数据)的raw feature的处理，也就是我们开始偷懒，把这部分的工作也交给了处理层的结构去完成。这样做的好处
是可以减少我们的工作量，采用现有的比较好的模型比如CNN去完成工作，这个的结果在输入数据比较理想的情况下是可以接受的，但是当我们
应用于无人驾驶的时候我们不可避免地会遇到天状况比较差的情况，收集的图像也就不尽人意，这个时候继续采用这种端到端的模式就会失真。
它的缺点也就体现出来了，而且越是这样依赖端到端的模式会使得我们的中间处理层越是接近一个黑箱。  

### 2. imitation learning and reinforcement learning  
模仿学习和增强学习的区别：  
我们先来了解一下机器学习的算法大致的分类：


### 3.Carla论文：英特尔&丰田联合开源城市驾驶模拟器CARLA
#### 1.模拟器引擎
引擎：Unreal Engine 4(UE4)  
用途：城市自动驾驶系统的开发、训练、验证的开源模拟器  
现状：模拟器开源而且已经安装，但是配置要求很高  
实现：Carla用Python实现并提供API，可以采用服务器-客户端的模式，服务器上运行并提供画面  
控制：转向、加速、制动  
容量：40个建筑、16辆车、50个行人  
地图：Town 1有2.9km的路线长度，Town 2有1.4km的路线长度  
天气：晴天、雨天、雨后、黄昏等均可设置，模拟视觉条件的不同  
传感：正常的摄像头视觉、真实深度、真实语义分割，后两个是由为实验提供的伪传感器  
分割：road,lanemarking,traffic sign,sidewalk,fence,pole,wall,building,vegetation,vehicle,pedestrian,and other.  
#### 2.三种自动驾驶方法
a.传统的模块化流水线  
子系统：传感、计划、持续控制。不需要地图，使用语义分割传感器，PID控制  
本地计划：沿线行驶、左转、右转、交叉路口行驶、急停  
b.模仿学习的端到端模型  
模型：深度神经网络  
数据集：{observation、command、action}  
command：直行、左转、右转  
using：Adam optimizer[14]
c.强化学习的端到端模型  
比较好的是前两种，强化学习和模仿学习类似，不赘述  
#### 3.结论
原论文的结论是：在新环境下第一种方法好，原本环境下模仿学习好，强化学习最差。我们将进一步验证。  

### Carla使用文档
#### 1.启动Carla
```
./CarlaUE4.sh /Game/Maps/Town01  
./CarlaUE4.sh /Game/Maps/Town02  
```
Town01和Town02是指两个不同的地图  

#### 2.客户端运行
文件夹PythonClient提供了与CARLA服务器交互的Python3的API  
运行之前需要安装依赖：  
```
pip install -r PythonClient/requirements.txt
```
PythonClient/client_example.py会提供基本功能来控制车子并将图像保存在本地  
```
./client_example.py --help
```
PythonClient/manual_control.py提供服务器视角和用WASD控制车子  
```
./manual_control.py --help
```

#### 3.服务器运行
用CARLA客户端控制的服务器模式  
```
./CarlaUE4.sh /Game/Maps/Town01 -carla-server -benchmark -fps=15 // 地图1  
./CarlaUE4.sh /Game/Maps/Town02 -carla-server -benchmark -fps=15 // 地图2  
./CarlaUE4.sh /Game/Maps/Town01 -carla-server -benchmark -fps=15 -windowed -ResX=800 -ResY=600 // 窗口设置  
```

##### 4.特殊命令
-carla-server 服务器模式  
-carla-settings="Path/To/CarlaSettings.ini" INI例子 Example.CarlaSettings.ini.  
-carla-world-port=N 未知，看不懂  
-carla-no-hud 平视显示器  
-carla-no-networking 关闭网络. Overrides -carla-server if present.  

#### 5.控制
服务器窗口下可以控制车子，但是必须要关闭网络-carla-no-networking  
```
W            : throttle	油门  
S            : brake	制动  
AD           : steer	方向  
Q            : toggle reverse	切换反向？  
Space        : hand-brake	手刹  

P            : toggle autopilot	切换自动驾驶模式  

Arrow keys   : move camera	方向键控制相机  
PgUp PgDn    : zoom in and out	视觉放大缩小  
mouse wheel  : zoom in and out	视觉放大缩小  
Tab          : toggle on-board camera	切换车载相机  

R            : restart level	启动水平  
G            : toggle HUD	切换平视显示器  
C            : change weather/lighting	改变天气和亮度  

Enter        : jump		跳？  
F            : use the force	推？  

F11          : toggle fullscreen	切换全局视角  
Alt+F4       : quit			退出  
```
#### 6.设置
CarlaSettings.ini文件是用来对模拟器的初始设置：
```
{ProjectFolder}/Config/CarlaSettings.ini.  
File provided by command-line argument -carla-settings="Path/To/CarlaSettings.ini".  
Other command-line arguments as -carla-server or -world-port.  
Settings file sent by the client on every new episode.  
```
天气设置  
```
WeatherId=6  
0 - Default  
1 - ClearNoon  
2 - CloudyNoon  
3 - WetNoon  
4 - WetCloudyNoon  
5 - MidRainyNoon  
6 - HardRainNoon  
7 - SoftRainNoon  
8 - ClearSunset  
9 - CloudySunset  
10 - WetSunset  
11 - WetCloudySunset  
12 - MidRainSunset  
13 - HardRainSunset  
14 - SoftRainSunset  
```


