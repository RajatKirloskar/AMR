**I have also uploaded a pdf which includes a more detailed explaination of the entire project.** Hope that helps!
[Design and Development of Autonomous Mobile Robot for Modular Applications (4).pdf](https://github.com/RajatKirloskar/AMR/files/10117670/Design.and.Development.of.Autonomous.Mobile.Robot.for.Modular.Applications.4.pdf)

# AMR
The Autonomous Mobile Robot (AMR) is developed mainly for warehouse point to point applications. For example, the AMR can carry material from one station to another as necessary. The AMR has the ability to carry 120 Kgs of payload and navigate autonomously in a pre-mapped environment. LiDAR sensors are used for robot localization and navigation.

### DEVELOPING THE DRIVE WHEEL SYSTEM
All the parts required to build the drive wheel system system are available in a hoverboard
-Hub Motor
-Battery
-Microcontroller
-Battery charger pin
-On/Off switch
-Hub motor holder

The first step is to set up the communication between the hoverboard motors and the contrller so that it responds to the commands sent from the Jetson Nano using the Teleop library in ROS.

Hardware- STLink v2 Programmer Module
Software- STM32 STLink Utility, STM32Cube Programmer

Hoverboard motors are BLDC motors and require a special controller to rotate the magnetic field. They do not work by simply applying power. That is why we flash the STM32 before controlling the wheels using Jetson Nano. 

To make it compatible for flashing, we need to convert the Hoverboard Firmware Hack FOC to a Hex file. The editor is set up by installing the PlatformIO IDE extension. There are a few changes that need to be made in the code in FOC such as uncommenting variannt USART which allows the programmer to communicate with the Jetson using the UART pins.
![image](https://user-images.githubusercontent.com/108690286/204653501-a58477ce-8468-4eba-aae9-28be07357519.png)

### SIMULATION
To simulate the movement of the robot in the real world, it is crucial to model the surroundings with accurate dimensions. For this project, I used Fusion 360 to model our "world". Once the world has been modelled, we need to convert the .f3d file to a URDF file. To do this, you have to first add the URDF exporter as an add-on in Fusion 360
![image](https://user-images.githubusercontent.com/108690286/204652280-f4e9a5c6-5e6f-4bb8-8b49-7e085abf46a6.png)

![image](https://user-images.githubusercontent.com/108690286/204651839-59339798-8ca5-419f-9133-c4c99113fc3a.png)

### AUTONOMOUS NAVIGATION
The ROS navigation stack is a collection of software packages that you can use to help your robot move from a starting location to the end location safely.

###### GMAPPING PACKAGE
It is a necessary package for the autonomous navigation of the mobile robot. It provides laser-based SLAM as a ros node called "slam_gmapping"

###### AMCL PACKAGE
AMCL is a two dimensional probabilistic localization system. It uses a particle filter to track a robot's pose against a known map. 
![image](https://user-images.githubusercontent.com/108690286/204654913-006ca814-cdf0-4676-b532-900f9971da20.png)

However, in this project, the AMCL package was used only for simulation. 
For the actual robot, Cartographer was implemented. Cartographer takes data from LiDAR laser scans to estimate the positions of the robot. 

### MAPPING
To create a map of the surrounding, the "cartographer_occupancy_grid_node" subscribes to the data received from the LiDAR sensors and publishes a map with the derivable area, hence creating a map with an outline.
![image](https://user-images.githubusercontent.com/108690286/204657822-2565c006-e9b3-47fe-b662-eca3351c054e.png)

### Navigation
Once the map has been generated, the map needs to be saved. The launch file should be able to locate this map. The launch file then takes the map data and opens it in RVIZ to visualize the data. Here, you can give the robot an end goal on the map. The AMR tries to reach the end goal by planning its path using "dwa_planner". The AMR finally reaches its goal by using the navigation stack algorithms to move from the starting position to the end position.
