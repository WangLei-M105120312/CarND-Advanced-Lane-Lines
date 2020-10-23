# **Advanced Lane Finding Project**

---
The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* For test highway images:
* 1. Apply a distortion correction to raw images.
* 2. Use color transforms, gradients, etc., to create a thresholded binary image.
* 3. Apply a perspective transform to rectify binary image ("birds-eye view").
* 4. Detect lane pixels and fit to find the lane boundary.
* 5. Determine the curvature of the lane and vehicle position with respect to center.
* 6. Warp the detected lane boundaries back onto the original image.
* 7. Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.
* 8. Video pipeline

[//]: # (Image References)

[image1]: ./output_images/undistort_output.png "Undistorted"
[image2]: ./output_images/undistort_Image.png "Road Transformed"
[image3]: ./output_images/combined_s_gradientthreshold.png "Binary"
[image4]: ./output_images/pipelineall.png "Binary Example"
[image5]: ./output_images/warped_image.png "Warp Example"
[image6]: ./output_images/orig_warped_all.png "Orig and Warp Example"
[image7]: ./output_images/color_fit_lines.png "Fit Visual"
[image8]: ./output_images/laneline_all.png "Output"
[image9]: ./output_images/laneline_curvature_all.png "Output"
[video1]: ./video_output/project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first section of the Jupyter notebook located in "Advanced Lane Lines Finding Project.ipynb". Use cv2.findChessboardCorners to find the chessboard corners, and store the corners points in an array "imgpoints" for each calibration image. The object points will always be the same as the known coordinates of the chessboard with zero as 'z' coordinate because the chessboard is flat. The object points are stored in an array called "objpoints". Then use the output "objpoints" and "imgpoints" to compute the camera calibration and distortion coefficients using the "cv2.calibrateCamera" function. Apply this distortion correction to the test image using the "cv2.undistort" function and obtained the result as below: 

![alt text][image1]

### Pipeline (single images)
The code used to implement these functionality could be found at [Advanced Lane Lines Finding Project](Advanced%Lane%Lines%Finding%Project.ipynb).

#### 1. Provide an example of a distortion-corrected image.

The camera calibration calculation implemented for chessboard image as the above example is used for eight highway test images. The following image shows the result of applying the camera calibration to the first picture of the test images and the distortion-corrected image is shown as below:

![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

The code for this step is in the second section "Use color transforms, gradients, etc., to create a thresholded binary image" of the Jupyter notebook located in "Advanced Lane Lines Finding Project.ipynb". A color transformation applying HLS combined S channnel and gradient thresholds is selected because it shows more contrasts on the lane lines as shown below:

![alt text][image3]

All binary image results for eight test images are shown as below:

![alt text][image4]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for this step is in the third section "Apply a perspective transform to rectify binary image ("birds-eye view")" of the Jupyter notebook located in "Advanced Lane Lines Finding Project.ipynb".

The code for my perspective transform includes a function called `warper()`.  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([(575,464),
                  (707,464), 
                  (258,682), 
                  (1049,682)])
dst = np.float32([(450,0),
                  (w-450,0),
                  (450,h),
                  (w-450,h)])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 575, 464      | 450, 0        | 
| 707, 464      | w-450, 0      |
| 258, 682      | 450, h        |
| 1049, 682     | w-450, h      |

The perspective transform was verified and working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image5]

The transformation matrix and the inverse transformation matrix was stored using pickle to be used on the main notebook "Advanced Lane Lines Finding Project". The following picture shows the binary images results after the perspective transformation:

![alt text][image6]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The line detection code can be found in the fourth section "Detect lane pixels and fit to find the lane boundary" of the Jupyter notebook. The algorithm calculates the histogram on the X axis. Finds the picks on the right and left side of the image, and collect the non-zero points contained on those windows. When all the points are collected, a polynomial fit is used (using np.polyfit) to find the line model. Using the same code, another polynomial fit is done on the same points transforming pixels to meters to be used later on the curvature calculation later. The following pictures show the points found on each window, the windows and the polynomials for eight test highwya images:

![alt text][image7]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The code for this step is in the fifth section "Determine the curvature of the lane and vehicle position with respect to center" of the Jupyter notebook. The left and right curvatures for eight test highway images are calculated and shown in the Jupyter notebook.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The code for this step is in the sixth section "Warp the detected lane boundaries back onto the original image" of Jupyter Notebook. To display the lane lines on the image, the polynomials where evaluated on a lineal space of the Y coordinates. The generated points where mapped back to the image space using the inverse transformation matrix generated by the perspective transformation. The following images show the lane line on the eight test highway images:

![alt text][image8]

### 7. Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

The code for this step is in the seventh section "Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position" of Jupyter Notebook. Combining the images with lane lines from Section Sixth, the images also shows the right and left lane curvatures and vehicle position.

![alt text][image9]
---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

After some refactoring of the code in the eighth section of Jupyter Notebook, the project video was processed and the results at video_output folder. Here's a [link to my video result](./video_output/project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I have ever had the results the lane lines crossing two lanes, which results from the incorrect perspective transform image due to the lane line without clear contrasts and even with more noises when other channel and colors applied. After comparing different colors/channels, magnitude and gradient, HLS combined S channnel and gradient thresholds is selected because it shows more contrasts on the lane lines. Further tests in HLS/color transforms/gradients are desired to make the algorithms more robust. 