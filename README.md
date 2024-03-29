# "Elderly Falling Detection" :older_man:	:older_woman:	
[![Up to Date](https://github.com/ikatyang/emoji-cheat-sheet/workflows/Up%20to%20Date/badge.svg)](https://github.com/ikatyang/emoji-cheat-sheet/actions?query=workflow%3A%22Up+to+Date%22)

A concept project for elderly safety walking.

<img src="https://user-images.githubusercontent.com/101269937/190342631-153a98b7-d131-4a3e-ad9d-dfab9ff3f831.jpg" width="170" height="170">


## "Description"

Tracking and angle detecting of a walking stick.

The project split to two main parts:

- Image Proccesing - using hough transform and other CV's technics, I created a line detecting in space. The system will track a specific line and calculates it's current angle position.

  - Components:
  
  
    1. Camera

- Robotics - using PID controll technics, I programmed a robot to track a specific line in space.
 
  - Components:


    1. Raspberrypi
    2. Pi Camera 
    3. 2 Servo Motors (vertical and horizontal)
    4. 4 DC Motors
    5. 4 Wheels
    6. Portable Power Supply (Preferably 12V)

## " Before we start... "
 
I used a "Yahboom Raspbot AI Vision Robot Car".

Raspbot is specially designed for AI beginners to learn AI at the lowest cost and include all you need for this project.

 - It has a Customized package (YB_Pcb_Car) that does the integration between the overall components and the raspberry pi.

 - The easy access to the components programming saves a lot of technical job and makes it easier to focus on the image processing.

>Link to purchase page:

https://category.yahboom.net/products/raspbot?_pos=1&_sid=b0c180d95&_ss=r

<p align="center">
<img src="https://user-images.githubusercontent.com/101269937/190184712-ad14d2e9-e70a-43a0-9437-20c94b0c1d50.jpg" width="250" height="200">
</p>


### "Note"

**for those who pass the purchase, it's available to use only the image processing part where only required a camera component**

## " Installation Packages "

```python

import cv2

import YB_Pcb_Car

import numpy as np

import matplotlib.pylab as plt

import math

import time

import RPi.GPIO as GPIO
```

## "Image processing - Line Detection"

For this part i had to filtering a lot of noise.
The main mission was to detect the walking stick line as "clean" as possible.

Algorithm summary:

- Before i worked on a video, I wanted to have a success on image dimension.
I took a random photos of the line (uploaded on "Test images") 

- I converted the image to binary (easy to work on) with using "in range" function (first move the color scale from RGB to HSV).

<p align="center">
<img src="https://user-images.githubusercontent.com/101269937/190342972-c689ca68-168c-45ff-8766-71e7ab8c0be9.jpg" width="250" height="200">
</p>


- I used successive filters "Erode" and "Dilate" that are very useful on small noises cleaning and emphasize the wanted object.

  <p align="center">
  <img src="https://user-images.githubusercontent.com/101269937/190343106-25df99bd-3ded-4734-81fd-521581d938ed.png" width="250" height="200">
  </p>


- Because the line detecting rely on color, I had to build more defences in case of line detecting. 
Using simple optics theory and camera's data sheet, I succesd to guess , with minimal error, the object distance (at this point the object dosent detected as a line for the algrithm yet).

  <p align="center">
  <img src="https://user-images.githubusercontent.com/101269937/190344411-85511f2c-467d-4238-8261-231ae14b42af.jpg" width="400" height="300">
  </p>


  As a result The noise which goes behind and before the measured object's distance, will be filterd.
  
 - Until this point, the line was not even detected. 
 **reminder:The main goal of the previous actions was to maximize the chances to detect the correct line in the cleanest way**
  
  <p align="center">
  <img src="https://user-images.githubusercontent.com/101269937/190346269-bb029a17-0616-412c-913f-e6a32200372b.jpg" width="250" height="200">
  </p>


 - To detect the line i used the "Hough transform" function (**it's impossible to detect a single line with this funcion only**) and with the help of the other filters i Finally succeessed to detect a single line!
 
 - After i detected the desirable line, It's time to detect the line's angle that will be the falling decision line.
 
  <p align="center">
  <img src="https://user-images.githubusercontent.com/101269937/190344729-08dba308-5548-44ae-97b6-00a6e3b181cb.jpg" width="400" height="200">
  </p>


- Only after I ensured the success of detection in the image dimension I moved to work on video's dimension which maintained the promises results.

- On video's dimension, every time the system will detect fall (line's angle below 45 degrees) the buzzer component will alert.

### Final Result:

  <p align="center">
  <img src="https://user-images.githubusercontent.com/101269937/190343890-69875fd5-7c41-41ef-ab80-a37fce02ff99.jpg" width="250" height="200">
  </p>

  <p align="center">
  <img src="https://user-images.githubusercontent.com/101269937/190344988-56b1cded-7397-4012-ba3d-a23b626d79bd.jpg" width="250" height="200">
  </p>



## "Robotics - PID controll"

In order to detect the line in every single time i had to stay tracked. 

- For this mission i decided to use a basic PID control, that will allow the camera move flexibly vertically and horizontally. the most diffcuilt part on implmenting a PID contrioll is finding the k's parameters. '

### PID formula:

 <img src="https://user-images.githubusercontent.com/101269937/190350613-011d3925-88b3-4667-b200-9855acd01595.jpg" width="200" height="50">


**In order to synchronize the servo movement parameter (presented by the angle changes) with the PID output, i had to normalize the results (i did it for both servoes). Works great linearly.**

- After successing controll the two servoes Smoothly and synchronizly as possible, i moved to the robot movement. For that i used raspboot's libary (YB_Pcb_Car) which offers a simple direction built-in functions that controll the 4 DC motors that conects to the raspberrypi with easy implementaion. 

- the last mission was to have conditions to the robot's direction decision action. I used a center point and two imaginary lines which bounderd the area. The goal was to maintian the point (represents the robot's center) between the lines and with the horizontally servo angle position ratio detemine the direction (forward, backward, right, left, static).

 <p align="center">
 <img src="https://user-images.githubusercontent.com/101269937/190356983-2af3efe5-981f-465e-961e-bb38dba28ca2.jpg" width="250" height="200">
 </p>

