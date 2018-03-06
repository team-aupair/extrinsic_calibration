# Extrinsic calibration

This a short tutorial for extrinsic calibration of Pepper's Depth and RGB cameras.

## Setup

First of all, print a calibration pattern (a chessboard) if you do not have any. We would recommend A3 size. Once you have it, count the number of rows and columns and measure a size of a square. Adjust these parameters in launch/calibration_cameras_pepper.launch:
* define the numbers of columns -1
* define the numbers of rows -1 
* define the size of one square in m

## Data recording

* turn on your robot, wake up, and move the head to the "zero" pose, for example
```
ssh nao@<PEPPER_IP>
qicli call ALMotion.wakeUp
qicli call ALMotion.setAngles "HeadPitch" 0.0 0.3
```

* take two post-it (one on the top of enougher) and fix it on the right eye of Pepper (to cover this eye with the camera inside)

* start Naoqi Driver, ensuring that color and IR images are enabled in [share/boot_config.json](http://protolab.aldebaran.com:9000/mummer/naoqi_driver/blob/master/share/boot_config.json)
```
roslaunch naoqi_driver naoqi_driver.launch nao_ip:=<PEPPER_IP>
```

* create an empty folder for data recording and provide its name in launch/extrinsic_calibration_pepper.launch
* update ROS topics for color and IR images in launch/extrinsic_calibration_pepper.launch
* launch the code:
```
roslaunch extrinsic_calibration extrinsic_calibration_pepper.launch
```

* if the software can subscribe to ROS topics, then you should see an image from a color camera
* place the calibration pattern in front of the robot
* once the pattern is detected (you can see all detected crossings), press "s" on your keyboard to save images from both cameras
* repeat 2 previous steps each time changing a distance and/or an angle to cover different views (at 60cm-80m distance) until you reach a sufficient number of image pairs (for example, 30 times or more)

## Calibration based on recorded data
* launch the code to compute extrinsic parameters based on recorded images:
```
roslaunch extrinsic_calibration extrinsic_calibration_pepper.launch
```

* press "c" on your keyboard to compute calibration values
* check if the resulted XYZ and RPY for last processed image pair correspond to the reality

### How to apply the extrinsic parameters

Once the calibration parameters are found, you can apply them using ROS static_transform_publisher. Check an example in register_depth.launch, update the values (x y z yaw pitch roll) and launch it after naoqi_driver:
```
roslaunch naoqi_driver naoqi_driver.launch nao_ip:=<PEPPER_IP>
roslaunch extrinsic_calibration register_depth.launch
```

-------------------

### Calibration Value for Pepper(ip 192.168.1.212)
* for static_transform_publisher
```
x: 0.032
y: -0.144
z: 0.048
Yaw: -0.0834208
Pitch: -0.062641
Roll: -0.0225045
frame_id: CemeraDepth_optical_frame
child_frame_id: CameraTop_optical_frame
milliseconds: 1
```
