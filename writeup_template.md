# **Finding Lane Lines on the Road. Project 1** 

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

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I .... 

1. First I create a color mask image in order to obtain the pixels that are either white or yellow (the colors we expect the lanes to be). Since blank is [255,255,255] and yellow [255,255,0] I only use the red and green channels for the filter.

2. I convert the image to gray scale and apply some smoothing using gaussian blur with a kernel size 5. I did not choose a big kernel size as I did not want to make the image too blurry, but I needed the kernel to be with enough size to eliminate possible artifacts. 

3. I apply canny edge detection algorithm. I tunned these parameters until I felt that the edges obtained were over the right and left lanes that we want to obtain. 

4. I create a mask filter for the region (quadrilateral). The values of this quadrilateral were calculated based on were I expected the lines to be.

5. From the edges under the region, I apply the hough transform to obtain the lines.

6. I process the obtained lines of the hough transform in order to obtain two coordinates (start and end positions) for the right line, and another two coordinates for the left line. The process consist in: (a) classify the lines as right line or left line (based on the sign of the slope), (2) calculate the coefficients of the polynomial of the form y = am + c for both left lines and right lines, (3) as we know the y coordinates of the lines (based on the regtion of interest), I obtain the corresponding x coordinates for the start and end positions of the right and left lines.

e.g. 
The 'y' coordinate are fix:

Ymax = 539
Ymin = 320

Calculate the 'x' coordinate as:

Xmax_right = int((Ymax - coefR[1])/coefR[0])
Xmin_right = int((Ymin - coefR[1])/coefR[0])

where coefR is the obtained coefficients of the polynomial calculated for the right line.

For single/static images this seemed to work well, but when I run the video stream, I found that, occasionally, the coordinates of the lines jumped to random positions. I tried to ammend this by averaging the coordinates of the last 10 frames, so that make the coordinates have slower transitions and thus prevent the sudden jumps.

7. Draw the lines to the images.



![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


- Color image filter: I hardcoded fixed values so for other lighting conditinos, this filter might not work. 

- Filter by region: Similarly as in the color image filter, I hardcoded the values, so if the camera moves a bit, the pipeline might stop working as we would filter in the wrong region. Also, if the vehicle is turning (i.e. changing lanes) we might probably wanted to modify the region of the camera we want to filter.

- To obtain the final right and left lines, I used a polynomial that represents a line, but this might not work when there is a curve. In this case, we might need to use a higher degree polynomial.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to use a different color space. Instead of RGB, use HSV which can be more robust for different lighting conditions.

Detect when vehicle is turning (doing a curve) in order to increase the degree of the polynomy for the right and left lanes.

Detect when vehichle is changing lanes as we might want to make the region of interest bigger to detect the old lane and the new lane.


