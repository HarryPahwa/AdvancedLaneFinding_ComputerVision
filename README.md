## Advanced Lane Finding Project
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

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

[image1]: ./camera_cal/distortion.png "Undistorted"
[image2]: ./camera_cal/distortion_example.png "Road Undistorted"
[image3]: ./output_images/transformed_test3.jpg "Bird eye view example"
[image4]: ./output_images/grad_thresh_transformed_test3.jpg "Threshold Example"
[image5]: ./camera_cal/histogram.png "Histogram"
[image6]: ./camera_cal/polyfit.png "Fiting the polynomials"
[image7]: ./output_images/sliding_window_grad_thresh_transformed_test3.jpg "Fitting the polynomials"
[image8]: ./output_images/road_plot_test3.jpg "Plotting it back on the road"

[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf. 

You're reading it! and the code is [here](https://github.com/HarryPahwa/AdvancedLaneFinding_ComputerVision/blob/master/code.ipynb)

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the Jupyter notebook code.ipynb. It is identical to how it was done in the quizzes.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

I did this differently, I performed a perspective transform first and then performed the gradient and color threshhold. 

The following are the source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 50,720      | 50, 720        | 
| 1100, 720      | 1100, 720      |
| 800,450     | 1280, 0      |
| 500, 450      | 0, 0        |

![alt text][image3]

I used a combination of color and gradient thresholds to generate a binary image. I ended up using a combination of saturation, hue, and angle threshold along with an absolute sobel transform.  Here's an example of my output for this step.  

![alt text][image4]


#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Here's a histogram of the white pixels in the image.

![alt text][image5]

Then I fit two second order polynomials to the either lane and painted the pixels in between green. 

![alt text][image6]

I tried using sliding window search but it was giving me mixed results.

![alt text][image7]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this at the bottom of the function 'after_first_search' in my code.ipynb file. Most of this code was taken from the Udacity lessons.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

![alt text][image8]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

My pipeline tends to fail on extremely bright concrete areas and starts to wobble. To combat this, I implemented that if there's a big change in the coefficients of the fitted polynomials between two frames, that it would ignore that and check the next frame. That improved it a little bit but it's not perfect. Another way to tackle this would be to average the last 10-15 frames and plot that instead of plotting the polynomial detected in every frame
