# V-Rep-Autonomous-Vehicle-Simulator with ROS


<img src="pictures/1.png"  >
<img src="pictures/gif1.gif"  >  
https://youtu.be/k36PovHUOAg  
  
  
<br /> <br />
## Instrunction
- It's built for Autonomous Vehicle Simulator using V-Rep & ROS simultaneously  
- It publishes data `Velodyne PointCloud`, `IMU Sensor`, `GPS`, `Localization (X,Y,Z,Heading)`, `Transform (tf)`  
to ROS at the same time as soon as you run the simulation.   
- Map File was built by using `Google Sketchup`.

1. move `libv_repExtImu.so`, `libv_repExtRosVelodyne.so` to your V-Rep home folder.  
2. build `libv_repExtRosInterface.so` and move to your V-Rep home folder to communicate with ROS. (googling please)

./NVIDIA-DRIVER.run -a -N --ui=none --no-kernel-module

export VREP_ROOT=~/v-rep/
apt-get install sudo
sudo apt-get install -y ros-kinetic-desktop-full git cmake python-tempita python-catkin-tools python-lxml
cd 99_Repo/
git clone https://github.com/CoppeliaRobotics/v_repStubsGen
cd v_repStubsGen/
export PYTHONPATH=$PYTHONPATH:$PWD

mkdir -p /tmp/quickstart_ws/src
cd /tmp/quickstart_ws 
catkin init
cd src/
git clone https://github.com/CoppeliaRobotics/v_repExtRosInterface.git

cp -r /mnt/Downloads/v_repStubsGen-9125e020d5b754bdabb2f575a12d52fc55a782d8/* /tmp/quickstart_ws/src/v_repExtRosInterface/external/v_repStubsGen/
cp -r /mnt/Downloads/v_repPlusPlus-cb31df76dac19580cbf2b071e431b3c4e8f2d8f2/* /tmp/quickstart_ws/src/v_repExtRosInterface/external/v_repPlusPlus/
cp -r /mnt/Downloads/PyCPP-c5ec1cf71c303d1115342cc18f74b8902b0f0c8e/* /tmp/quickstart_ws/src/v_repExtRosInterface/external/v_repStubsGen/external/pycpp/

add below code to cmakelist.txt
set(roslib_LIBRARIES "/opt/ros/kinetic/lib/roslib")
sudo apt-get install xsltproc
	
3. open V-Rep and Run. if there is no error, It's OK  



<br /> <br />
## Topic 
<img src="pictures/2.png" width="400" >
<br /><br />

## Transform Tree(tf tree)
<img src="pictures/3.png"  >


<br /> <br />
## Reference
I've used IMU Plugin & Velodyne Plugin from   
https://github.com/bartville/vrep_plugin_imu  
https://github.com/omcandido/vrep_plugin_velodyne
<br /><br />

<br /> <br />
## TODO
1. Modify Vehicle Wheel 
2. Other Map (Highway, Urban etc..)
3. Use urdf file of Vehicle
4. Install other sensors on it


<br /> <br />
feel free to use and if you find any bug while using, post an issue. :-)  
