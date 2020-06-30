======
# Windows下的Moveit源码编译

## 参考链接
<p>
<a href="https://moveit.ros.org/install/source-windows/">Moveit SOURCE BUILD ON WINDOWS</a>
</p>

## 准备工作
首先确保电脑已经安装了ROS
<p><a href="https://moveit.ros.org/install/source-windows/">Install ROS on Windows</a></p>

- 开始菜单栏，找到"x64 Native Tools Command Prompt for VS 2019"

- 右击，以管理员身份运行

- 配置ROS环境变量
```
cd c:\opt\ros\melodic\x64

setup.bat
```
- 创建空的工作空间存放moveit源码
```
mkdir c:\moveit_ws\src

cd c:\moveit_ws
```

- 检查上面获取到的源，下载Moveit源码
```
wstool init src

wstool merge -t src https://raw.githubusercontent.com/GuavaL/melodic-moveit-win-install/master/melodic-moveit.rosinstall

wstool update -t src
```

- 寻找额外的依赖包
```
set ChocolateyInstall=c:\opt\chocolatey

choco source add -n=ros-win -s="https://roswin.azurewebsites.net/api/v2" --priority=1

rosdep update

rosdep install --from-paths src --ignore-src -r -y

```

- Moveit源码编译
```
catkin_make_isolated --use-nmake --install --install-space c:/opt/ros/melodic/x64 -DCMAKE_BUILD_TYPE=Release -DCATKIN_ENABLE_TESTING=0
```

- 编译过程中缺少ROS包或者其他依赖包通过以下命令下载
```
git clone ~     //google github 找到ROS依赖包，下载

choco install yaml-cpp -y       //其他依赖包通过Choco下载
```

- 编译过程中的问题修改

    (1) C:\moveit_ws\src\geometric_shapes\CMakeLists.txt 添加宏add_definitions(-D_ENABLE_EXTENDED_ALIGNED_STORAGE)

    (2) C:\opt\ros\melodic\x64\include 中添加numpy头文件(C:\opt\python27amd64\Lib\site-packages\numpy\core\include\numpy)
    
    (3) 
