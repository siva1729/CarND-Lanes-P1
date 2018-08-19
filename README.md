# **Finding Lane Lines on the Road** 

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

#Overview:
 The imagig pipeline consists of the following steps..
 
  For performing different steps of the imaging pipeline, tuning parameters are defined as Global parameters
  that would be used during different stages of pipeline as shown below:
      - Kernel size for GaussianBlur filter
      - Edge-detection threshold values
      - Vertices of the polygon to select the mask (region of interest)
      - parameters for line-detection as part of Hough transform
      - final trace color and thickness
      
 1. Convert to Gry-scale image:
    The first step is to convert the input image(or image part of the video) to a Grayscale image
    to detect edges in the images that would help find the lane markings. This was achieved
    using the openCV cv2.cvtColor() function
    
 2. Remove high-frequency noise in the image before edge-detection:
    Then Low-Pass filtering(blurring) is applied on the Grayscale image to remove any high-frequency noise
    using the cv2.Gaussianblur() function and the kernel size is chosen to be "3" (higher value can be chosen, 
    filtering at image edges should be considered)
    
 3. Detect edges in the image:
    Using Canny edge-detection function (cv2.Canny), edge-detecting is performed and the low/high threshold values
    are chosen such that edges are appropriaely deteced for lane thickness in the suggested ratio of 1:3
 
 4. Select the Region of Interest(ROI):
    On this Gray-scale image with edges, a mask is applied to select the region that has both the right and left lanes
    and the mask is selected in the form of a Trapezoid whose size parameters can be selected as part of tuning parameters
    to selec the region of interest
 
 5. Lines-detection using Hough Transform:
    Using the ROI image (with edges), apply Hough transform to detect lines in the image. Using the tuning the parameters
    for Hough grid(rho/theta) and threshold for min # of votes required for line to be output and other parameters
    detects the lines. The min_line_length and max_line_gap parameters needs to be tuned to detect/mark lines in the
    images with min length in pixels and max gap between segments
    
 6. As part of the hough_lines() function, we have draw_lines() function that uses the lines vector output from 
    Hough transform and traces the lanes on the image. The draw_line() function consists of the following steps
    - First we try to detect the line-segements(from lines vector) that are part of the left and right lane markings
      using the slope of the line-segments. Only line-segments that are within a given slope range are considered to avoid
      the ones with extreme angles. The slope of the line-segements needs to be within min/max thresholds to be considered     
      (slope_min_threshold/slope_max_threshold). Also the x-location of these points w.r.t to center of the image is considered
      along with the slope.
    - Then store the (x,y) points in separate lists that are part of left/right lane markings. It is better to keep track 
      the list lengths to check for any case where no line-segements are detected to avoid erroroneous cases.
      This is done using (2) flags trace_left and trace_right.
    - Then using the left and right points from the list, try to fit a line using linear regression (using the function
      np.polyfit()) and get the m,b parameters for left an d right lanes
    - Considering the ROI, for marking the lanes from bottom of the image to top of the ROI, the x-cordinates of the
      lanes are computed using the y-cordinates of the ROI and slope/intercept of the left/right lanes for bottom and top 
      of the ROI.
    - Two lanes are drawn using the cv2.line() function using the bottom/top (x,y) co-ordinates for left and right lanes.
    
![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline

1. If the line-segments are not detected in the image, then we cannot draw the lines. This could be
   due to various reasons lighting conditions in the images (image is not bright), noise in the
   image, tuning parameters not good enough for lane markings for max_line_length, etc.
   
2. Chosing a ROI is also not ideal in all cases as it might be valid during turns, curves,..

3. Lanes are not always straight (turns, curves,..), so drawing a line is not ideal.



### 3. Suggest possible improvements to your pipeline

1. A lane should ideally (if exists) have markings on left and right and always with more
   or less certain width/distance between them. In the algorithm it is better to take advantage of this.
   
2. Pre-processing of the images before edge-detection to consider color/dynamic range/contrast to make
   sure edge-detection is possible for lane markings

3. Make use of the lane curve parameters (slope, etc.) from previous images to correct/detect lane markings
   in the current image(s).


   
   
