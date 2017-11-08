# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.


My pipeline (function `mark_lane_image`) consists of 5 steps.

1. Identify the white and yellow lines, which turns out to be crucial for later edge detections.
   - It is simpler to recognize white color in RGB format but yellow color in HSV/HLS space.
   - The yellow range is widely selected to be [20, 100, 100] to [30, 255, 255], as suggested in [Tracking colored objects in OpenCV](http://aishack.in/tutorials/tracking-colored-objects-opencv/).

2. Then the white-yellow-filtered image is gray-scaled and blurred by Gaussian filters.

3. The third step is to run Canny edge detection using the Gaussian smoothened gray image.

4. I mask the image to the bottom trapezoidal, which is subsequently transformed to Hough space to identify straight lines.
5. Finally, I mark the left and right lines and save the stacked image.


In order to draw a single line on the left and right lanes, I modified the `draw_lines` function, which now has three algorithms to compute lines.

1. The "raw" scheme will simply draw the line segments detected by `cv2.HoughLinesP`, identical to the lecture.
2. The other two strategies, "weighted_average" and "lin_reg (default)", both separate the raw lines to left (slope < 0) and right (slope > 0).
   The key point is to obtain the averaged slope and intercept.
   - In "weighted_average", I average the slopes and intersecpts of all valid line segments.
     Each segment is associated to a weight which is propotional to its length.
   - In "lin_reg", the end points of a raw line is equally grided according to its length.
     The grided ($x, y$) pairs are added to sets $X$ and $Y$.
     The averaged slope and intercept are extracted by performing a linear regression using $X$ and $Y$.
   - With the averaged slope and intercept, I can draw a line with its y endpoints to be 0.6 * image_height and image_height.


### 2. Identify potential shortcomings with your current pipeline

Overall, the current line detection works better for straight lines than curve lines, as revealed by the challenge output video.
The detection may also fail if the line is not clearly visible.
Example includes worn out lane lines, snow weather, or very steep road.


### 3. Suggest possible improvements to your pipeline

To identify curves, a more complicated generalization of Hough transformation is needed.
For difficult situtaions, it might be useful to track other nearby cars for guidance or detect if the tires on the rumbles (or bumps or Botts' dots) or estimate distance from road edge (fence).

