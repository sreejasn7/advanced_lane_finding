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
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"


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

The code is written by get
The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 in the file `example.py` (output_images/examples/example.py) (or, for example, in the 3rd code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

* Function used perspective_transform
* used cv2.getPerspectiveTransform and cv2.warpPerspective.
* Used Hard coded source and destination points. 

* Source Points:
```

self.src = np.float32([[710,463] , [1111 , 715] , [209 , 717],[578 , 463]])


self.dst = np.float32([[self.img_size[0]-self._offset_leftright, self._offset_topbottom],\
                               [self.img_size[0]-self._offset_leftright, self.img_size[1]-self._offset_topbottom],[self._offset_leftright, self.img_size[1]-self._offset_topbottom],\
                               [self._offset_leftright, self._offset_topbottom]])


```


I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  