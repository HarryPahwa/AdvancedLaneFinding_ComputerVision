## Advanced Lane Finding Project


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


### Camera Calibration


I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)



To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]


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


Here's a histogram of the white pixels in the image.

![alt text][image5]

Then I fit two second order polynomials to the either lane and painted the pixels in between green. 

![alt text][image6]

I tried using sliding window search but it was giving me mixed results.

![alt text][image7]

![alt text][image8]


---

My pipeline tends to fail on extremely bright concrete areas and starts to wobble. To combat this, I implemented that if there's a big change in the coefficients of the fitted polynomials between two frames, that it would ignore that and check the next frame. That improved it a little bit but it's not perfect. Another way to tackle this would be to average the last 10-15 frames and plot that instead of plotting the polynomial detected in every frame
