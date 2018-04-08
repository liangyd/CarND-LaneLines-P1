# **Finding Lane Lines on the Road** 

## Writeup Report

---

**Finding Lane Lines on the Road**

The steps of this project are the following:

* Make a pipeline that finds lane lines on the road
* Reflect on the work in a written report
* Complete the optional challenge

The pipeline in this project can find lane lines in all the three videos in the "test_video" folder.

[//]: # (Image References)


[image0]: ./test_images/solidYellowCurve.jpg "Origin"

[image1]: ./test_images_output/1_color_selection_solidYellowCurve.jpg "Color Selection"

[image2]: ./test_images_output/2_grayscale_solidYellowCurve.jpg "Grayscale"

[image3]: ./test_images_output/3_smoothing_solidYellowCurve.jpg "Smoothing"

[image4]: ./test_images_output/4_canny_solidYellowCurve.jpg "Canny Detection"

[image5]: ./test_images_output/5_roi_solidYellowCurve.jpg "Region of Interest"

[image6]: ./test_images_output/6_hough_solidYellowCurve.jpg "Hough Transform"

[image7]: ./test_images_output/7_single_line_solidYellowCurve.jpg "Lane Lines"

---


### 1. Description

I will take the test image "solidYellowCurve.jpg" as an example to explain the pipeline functions.
![image0]

My pipeline consists of the following steps: 

1. Convert the raw RGB image to HLS image to select white and yellow pixels: I found out that HLS(Hue, Lightness, Saturation) is easier to represent a color than RGB. Thus, I used the cv2.cvtColor function to convert RGB to HLS. The white color has a high lightness value. The yellow color has a hue between 10 and 40. I used the cv2.inRange function to filter the selected colors. I converted the HLS back to RGB after color selection. ![image1]
2. Convert the image to a grayscale image: I used the cv2.cvtColor function to convert it to grayscale image. ![image2]
3. Apply Gaussian smoothing: I used the cv2.GaussianBlur function with kernel size of 5.  ![image3]
4. Run Canny edge detection: I used the cv2.Canny function with low threshold 50 and high threshold 110.  ![image4]
5. Define the region of interest and select the edges within the region. ![image5]
6. Run Hough transform to get the line segments. ![image6]
7. Draw single lane lines on the image ![image7]


In order to draw a single line on the left and right lanes, I modified the draw_lines() function by the following steps:

1. Calculate the slope of each line segment (slope=(y2-y1)/(x2-x1))
2. If a line segment has positive slope larger than 0.5 and it is on the right side of the image, it belongs to the right lane; if a line segment has negative slope less than -0.5 and it is on the left side of the image, it belongs to the left lane 
3. Define the top and bottom of the region of interest
4. Use first order polynomial curve fitting for all endpoints on right lane segments to get the slope and intercept for right lane
5. Use first order polynomial curve fitting for all endpoints on left lane segments to get the slope and intercept for left lane
6. Calculate the intersection between lane lines and the boundary of the region of interest
7. Draw the right and left lane lines by the cv2.addWeighted function




### 2. Potential Shortcomings

The potential shortcomings are:

* The region of interest is hard-coded in the program. If the road is not on the center of the image, the lanes may not be detected.

* The lane lines may not be detected if they are not yellow or white. 

* The shadows on the road might be detected as lane line segments.

* If there is a preceding vehicle in the region of interest, the lines on the preceding vehicle may be detected as lane line segments.

* The ambient light condition may influence the lane detection. For example, the image is taken at night when the street lights are on. Or, the image is taken at twilight when the vehicle headlamp is on. 

* The road surface covered with snow may be detected as white lane lines. 



### 3. Possible Improvements

The possible improvements are:

* The region of interest should be adjustable. If the detected lane line crosses the side boundary of the region, the region should be reshaped and relocated.

* Other colors should be selected if they are used for lane markers.

* The region of interest can be divided into two quadrilaterals. Each quadrilateral detects one lane. 


### 4. Optional Challenge 

The images in the "challenge.mp4" video have the following characteristics:

* The vehicle hood is shown at the bottom of each image.
* There are shadows on the road which may be detected as line segments.
* There are two different types of road surfaces. One of them is whiter than the other.

I modified my pipeline in the following steps to make it more robust:

* I increased the bottom of the ROI so that it filters out the vehicle hood. 
* I tuned the color selection function to filter out the shadows: I increased the brightness threshold which represents the white color. I narrowed the hue range which represents the yellow color.
* A line segment does not belong to any lane if it is almost horizontal: If the absolute value of a slope is less than 0.5, it is not part of the lane. 


The pipeline works for all three videos in this project.
