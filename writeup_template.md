# **Finding Lane Lines on the Road. Project 1**

[//]: # (Image References)

[colorfilter]: ./test_images_output/colorfilter.jpg "Color filter"
[edges]: ./test_images_output/edges.jpg "Edges"
[finalimage]: ./test_images_output/finalimage.jpg "Final image"
[gaussianblurr]: ./test_images_output/gaussianblurr.jpg "Gaussian blurr"
[grayscale]: ./test_images_output/grayscale.jpg "Gray scale"
[lines]: ./test_images_output/lines.jpg "Lines"
[regionofinterest]: ./test_images_output/regtionofinterest.jpg "Region of interest"

## Pipeline

My pipeline consists of 7 steps:

1. First I create a color mask image in order to obtain the pixels that are either white or yellow (the expected colors of the lines). Since blank is [255,255,255] and yellow [255,255,0] I only use the red and green channels for the filter. After applying the color mask, we obtain the following image.

![alt text][colorfilter]

2. I convert the image to gray scale and apply some smoothing using gaussian blur with a kernel size 5. I did not choose a big kernel size as I did not want to make the image too blurry.

![alt text][grayscale]

![alt text][gaussianblurr]

3. I apply canny edge detection algorithm. I tunned the low and high threshold until I felt the edges of the images were correctly detected. For example, by setting very high "high threshold" we might eliminate good candidate lines.

![alt text][edges]

4. I create a mask filter for the region (quadrilateral). The values of this quadrilateral were calculated based on were I expected the lines to be.

![alt text][regionofinterest]

5. From the obtained edges under the region of interest, I apply the hough transform to obtain the lines. I tunned the paramters based on how I would expect line detection should be (e.g. with a reasonable line lenght, which should be not too big in order to discard potential lines, but not too small as this would include pixels that do not belong to any line). I also played with the accuracy/definition of the line by tunning the parameters theta and rho until I obtained a reasonable good results.

6. I process the obtained lines of the hough transform in order to obtain two coordinates (start and end positions) for the right line, and another two coordinates for the left line. The process consist in: (a) classify the lines as right line or left line based on the sign and the value of the slope *, (2) calculate the coefficients of the polynomial of the form y = am + c for both left line and right lines, (3) as we know the y coordinates of the lines (based on the region of interest), I obtain the corresponding x coordinates for the start and end positions of the right and left lines using the obtained polynomial coefficients, (4) for the video stream I also average the coordinates of the last positions of the lines in order to make the line changes smoother

e.g.
The 'y' coordinate are fix:

Ymax = 539
Ymin = 320

Calculate the 'x' coordinate as:

Xmax_right = int((Ymax - coefR[1])/coefR[0])
Xmin_right = int((Ymin - coefR[1])/coefR[0])

where coefR is the obtained coefficients of the polynomial calculated for the right line.

*Initially I only took into account the sign of the slope (>0 or <0), but this criteria seemed to introduce some artificats in the video stream (some jumps on the coordinates of the start and end point of the line). Therefore I decided to discard lines with slope close to zero.

![alt text][lines]

7. Draw the lines on top of the original image.

![alt text][finalimage]


### 2. Identify potential shortcomings with the current pipeline


- Color image filter: I hardcoded fixed values so for different lighting conditions, the current implementation might not work so well.

- Filter by region: Similarly as in the color image filter, I hardcoded the values. So if the camera moves a bit, the pipeline might stop working as we would filter in the wrong region. Also, if the vehicle is turning (i.e. changing lanes) we might probably want to modify the region of the camera we want to filter.

- To obtain the final right and left lines, I used a polynomial that represents a line, but this might not work when there is a curve. In this case, we might need to use a higher degree polynomial.

- The classification of the edges that belong to the right and left lines are based not only on the sign of the slope but also based on its magnitude. This works well if line is straight and with slightly curved lines. But I think the classification method and values of the parameters might need to change depending on if the line is curved and on the direction of the curve.


### 3. Improvements of the pipeline

- Use a different color space. Instead of RGB, use HSV which can be more robust for different lighting conditions.

- Detect when vehicle is turning (doing a curve) in order to increase the degree of the polynomy for the right and left lanes. Also, if we detect the vehicle is turning, we might need to modify the method and parameters of detecting which edges belong to the right line or the left line.

- Detect when vehichle is changing lanes as we might want to make the region of interest bigger to detect the old lane and the new lane.
