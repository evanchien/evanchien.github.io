---
layout: post
title: 'Gesture calligraphy'
---
### Category: Computer Vision, Python
### Overview
The target of this computer vision project is to detect human finger gestures for calligraphy. The target is not only to be able to draw with fingers but to make the drawing unique by showing the drawing speed with line width. Plus, a gesture is designed for screen cleaning.


{% include image.html url="http://www.youtube.com/watch?feature=player_embedded&v=T3r4xMxH39M" image="projects/proj-1/CV_demo.png" %}

### ROI processing
A key to ROI (human palm/finger) finding is how we seperate it from the environment. Here, we take the skin color of Asian as our target.

First, we process every frame in HSV color space and truncate part of the frame so we can focus on the human palm and not be affected by other areas with human skin color, for example, human faces.

Applying an `inrange` mask and `bitwise and` the original frame to itself, we get a frame with isolated human palm. The ROI isn't always perfect. Lighting conditions and camera noise can bring some HOLES to the ROI. Thus, we use `dilate/erode` to kill the holes and to stablize the contour.

### Contour
The most important part of this project is to get a clear coutour of the ROI and to filtrate out the noise.

To get the contour, we apply `Canny edge detector` to the binary image converted from the HSV image.

There can be plenty of objects with colors similar to human sikn in a frame. `cv2.contourArea` is what I use to isolate the palm from the background as the fact that the palm of a person sitting in front of the webcam shall be larger than the objects in the background. So, properly select the area size let our program focuses on the palm.

### Noise canceling in motion
After we get the biggest contour, we use `numpy.argmin` to find the topmost point of the contour. If it is stable and we are almost done. But, it's not that perfect. The noise cancelation and the edge detector we applied to the image can cause small jitters to the point and that's not what we want. So, it is necessary to add a low pass filter before we actually draw the points/lines in the frame.

### Gesture recognition
How can the program know when to draw and when not to draw? We use `cv2.matchShapes` to compare the contour with a certain pattern, the stop sign.The matchShapes function is comparing the image moments (Hu moments) of the two contours. The benefit of this is that the moments aren't affected by rotation, mirror and scaling. If the score is lower than the threshold, we know it doesn't match the stop sign and thus we record the point and the distance of it with the point in the previous frame.

The last thing in the program is just to output the points and connecting them with lines in different widths.

### Future works
In this program, a fixed color range is selected. To adapt to different skin colors, machine learning and training should help. This will be my next goal.

Besides skin color, gesture selection is now limited. It is not hard for user to choose their own gestures as their stop signs. However, the threshold setting can be difficult. This is another topic I would like to work on.
