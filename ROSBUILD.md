======
# Windows下的ROS源码编译

## 参考链接
<p>
<a href="https://ms-iot.github.io/ROSOnWindows/Build/source.html">ROS SOURCE BUILD ON WINDOWS</a>
</p>

## 准备工作
如果之前已经在电脑上安装过ROS，需要先卸载ROS和相关功能依赖包

- 删除opt文件夹
```
rd /s c:\opt
```

- 直接卸载chocolatey
```
rd /s C:\ProgramData\chocolatey
```

安装 Visual Studio Build Tool 2019, CMake, chocolatey and python2.7.

-   <p>
    <a href="https://visualstudio.microsoft.com/zh-hans/vs">Visual Studio 2019 下载安装</a>
    </p>

- 开始菜单栏，找到"x64 Native Tools Command Prompt for VS 2019"

- 右击，以管理员身份运行

- 复制下面的命令安装chocolatey：

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```
- 关闭终端完成安装

- 安装Git
```
choco upgrade git -y

git --version
```
- 下载Python2.7安装在c:\opt\python27amd64\

### 安装辅助依赖

- 更新pip版本
```
python -m pip install -U pip setuptools
```

- 通过pip安装ROS安装工具
```
pip install -U rosdep rosinstall_generator wstool rosinstall
curl --output requirements.txt -L https://raw.githubusercontent.com/ms-iot/rosdistro-db/init_windows/rosdistro_cache/catkin-requirements.txt
pip install -U --no-deps --force-reinstall -r requirements.txt
```

- 初始化rosdep
```
rosdep init
curl --output 10-ms-iot.list -L https://raw.githubusercontent.com/ms-iot/rosdistro-db/init_windows/rosdep/sources.list.d/10-ms-iot.list
copy 10-ms-iot.list c:\opt\ros\melodic\x64\etc\ros\rosdep\sources.list.d
rosdep update
```

- 配置Chocolatey源，增加roswin源
```
choco source add -n=ros-win -s="https://roswin.azurewebsites.net/api/v2" --priority=1
choco source disable -n=chocolatey
```

## install 和 build ROS源码

- 创建catkin workspace
```
mkdir c:\ros_catkin_ws
cd c:\ros_catkin_ws
```

- 桌面可视化功能包的安装：ROS, rqt, rviz and robot-generic libraries
```
set ROSDISTRO_INDEX_URL=https://raw.githubusercontent.com/ms-iot/rosdistro-db/init_windows/index.yaml
rosinstall_generator desktop --rosdistro melodic --deps --upstream-development > melodic-desktop.rosinstall
wstool init src melodic-desktop.rosinstall
```

- ROS依赖项下载
```
rosdep install --from-paths src --ignore-src --rosdistro melodic -r -y
```

- 编译源码
```
set PATH=c:\opt\rosdeps\x64\bin;%PATH%

copy ".\src\catkin\bin\catkin_make_isolated" ".\src\catkin\bin\catkin_make_isolated.py"

python .\src\catkin\bin\catkin_make_isolated.py --use-nmake --install --install-space c:/opt/ros/melodic/x64 -DCMAKE_BUILD_TYPE=Release -DCMAKE_PREFIX_PATH=c:/opt/ros/melodic/x64;c:/opt/rosdeps/x64 --ignore turtle_actionlib turtle_tf turtle_tf2
```
编译过程中遇到缺少依赖项，查看roswin的源通过choco install或者pip install 下载。
```
https://github.com/ms-iot/rosdistro-db/blob/init_windows/rosdep/win-chocolatey.yaml
```
