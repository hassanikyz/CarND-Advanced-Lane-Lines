**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/Chess-undistort.JPG "Undistorted"
[image2]: ./examples/Testimage-undistort.JPG "Road Transformed"
[image3]: ./examples/sobel-color-lightness-thresh.JPG "Binary Example"
[image4]: ./examples/perspective_transform.JPG "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_output_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README



### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell (In [172]) of the IPython notebook located in "./AdvancedLaneFinding.ipynb". Example given below.
Camer calibartion metrics are calculated by running through all chess images provided and using OpenCV's supported functionality for calibration.


![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

Code cell Ln 173 of the Jupyter Notes book 'AdvancedLaneFinding.ipynb' performs undistorting. Test Images confirms calibration is working fine.
![Example Here][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

Used combination of threshold x gradient and color and lightness channel. Played with different values using trial and error to arrive at a set which works well for this video at least. 

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `transform_perspective()`, which appears in lines/cell In 150.  The `transform_perspective()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python

    src1 = [width // 2 - 76, height * 0.625]
    src2 = [width // 2 + 76, height * 0.625]
    src3 = [-100, height]
    src4 = [width + 100, height]
    src = np.float32([src1, src2, src3, src4])

    dst1 = [100, 0]
    dst2 = [width - 100, 0]
    dst3 = [100, height]
    dst4 = [width - 100, height]
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 564, 450      | 100, 0        | 
| 716, 450      | 1180, 0       |
| -100, 720     | 100, 720      |
| 1380, 720     | 1180, 720     |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

measure_curve_on_road() function in cell In 192 calculates the radius of curvatur of the lane and position is calculated by another function 
get_vehicle_offset()

I implemented these steps in In 197  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_output_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

While this code is working for one video it is clear there are shortcomings here as it fails to work as smoothly on different types of videos. Under different lighting conditions the identification of lanes sometimes fail. 

Challenge I faced was lane detection was failing on 23-24s mark on the project video -- it was detecting lane all the way to left of the highway divider vs till yellow line.
Experimentating with different thresholds for color and gradient resolved the issues.

Using of convolution neural network style technique vs sliding window vertically to identify 'hot' pixels for lanes detetion is expected to yield better result.
