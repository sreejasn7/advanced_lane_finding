## Advanced Lane Finding

---

**Advanced Lane Finding Project**

The steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/originalAndUndistored_calibration.png "Undistorted"
[image2]: ./output_images/image_undistorted.png "Undistorted"
[image3]: ./output_images/image_binary.png "Binary "
[image4]: ./output_images/image_bird_eye_view.png "Warped Image"
[image5]: ./output_images/image_polyfit.png "Fit Visual"
[image6]: ./output_images/image_final_result.png "Output"
[image7]: ./output_images/pipeline_image_test4.jpg "Combined Results"
[video1]: ./output_images/final_output.mp4 "Video"


### Pipeline 

The pipeline is created using a class called LaneFinding. Includes all the necessary functions for lane finding process.

The code for camera calibration is contained in the first code cell of the IPython notebook located in "final.ipynb" under the 2nd cell.

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The Camera calibration was prepared using camera_calibrate() function inside the class. It uses 9 x 6 chessbox corner.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. `objp` is just a replicated array of coordinates, and `obj_points` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `img_points` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `obj_points` and `img_points` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline

#### 1. A distortion-corrected image.

* Function used undistort_image()
* Includes cv2.calibrateCamera() and cv2.undistort(). Used the obj_points and img_points from camera calibration.
* Used test4 from test_images

![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.


* Function used get_binary_image()
* Used HLS fto extract L and S channels. The L channel used for Sobel in x direction. S channel was robust to lane line. Used thresholds to extract needed masks of L and S channels
* Inorder to strengthen the lane lines  ,used HSV color threshold.
* Combned masks from L , S , color thresholded HSV. 

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

* Function used is perspective_transform()
* used cv2.getPerspectiveTransform and cv2.warpPerspective.
* Used Hard coded source and destination points. 

* Source Points and destination points used:
```

self.src = np.float32([[710,463] , [1111 , 715] , [209 , 717],[578 , 463]])


self.dst = np.float32([[self.img_size[0]-self._offset_leftright, self._offset_topbottom],\
                               [self.img_size[0]-self._offset_leftright, self.img_size[1]-self._offset_topbottom],[self._offset_leftright, self.img_size[1]-self._offset_topbottom],\
                               [self._offset_leftright, self._offset_topbottom]])


```




![alt text][image4]

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a  warped counterpart of a straight line image to verify that the lines appear parallel in the warped image.

Example of this can be seen below in 4rth image in the 2nd column.

![alt text][image7]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

* The entire lane pixel finding , position fitting , base position calculation , radius of curvature calculation was done under fit_polynomial() function . 
* To find the lane pixels , two methods are used find_lane_pixels and search_around_poly.Once if we get a confident detection , then search_around_poly function is used. 
* Based on the lane pixels found , a 2nd order polynomial is generated to fit along y axis(our defined full space.)
* Weighted Averaging on 20 previous polyfit outputs is done to calculate the near to accurate current fit. Average is calculated using get_average_fit() function. 
* For left lane Weightage of 50 % current lane fit and 50% to previous 10 lanes is set .
* For right lane Weightage of 60% to current lane fit and 40 % to previous 10 lane fits is set. 


![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

* Calculated inside the fit_polynomial() function . 
* Position of vehicle is calculated using the mean OF (mean(leftx pixels) and mean(rightx pixels)) 
* Radius of curvature is calculated in the function measure_curvature_pixels(). This was calculated using the current fit and weighted average previous fit . 

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

* The final result is rendered using the function fill_final_frame(). The polyfill and text rendering is coded inside this function .

![alt text][image6]

---

### Pipeline (video)


Here's a [link to my video result](./output_images/final_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Chances of Failure :
* The pipeline might fail if the image started from an unrobust environment. ie if it started with a shadow region or from a road with light shade color. 

Hurdles:
* Hurdle faced was during the 2 shadow region  , which consumed most of my time. Its from this position I decided to include HSV for more robust binary image. Gradient would not have worked in that region  , since the lanes where not quiet visible in the first shadow region. Here which one was noise or which one was lane it was difficult to find out. 
* Search using poly also intensified the search to a narrow region, still due to the noise pixels in the searching area , the polyfit would jump of lane, 
* Averageing help me to solve the fitting problem . But again , both the lanes would not respond equally to the weights. So had to set different set of weights to the left and right lane seperately. 