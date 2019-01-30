## Writeup Template

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

[image1]: ./undistorted_images/calibration1.jpg "Undistorted"
[image2]: ./undistorted_images/straight_lines1.jpg "Road Transformed"
[image3]: ./thresholded_binary_images/straight_lines1.jpg "Binary Example"
[image4]: ./transformed_images/straight_lines1.jpg "Warp Example"
[image5]: ./ploy_images/straight_lines1.jpg "Fit Visual"
[image6]: ./overal/example_output.jpg "Output"
[video1]: ./project_video_lane.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

I first compute all the `objpoints` and `imgpoints` from 'camera_cal/calibration*.jpg'. Once those are obtained, I compute the `mtx, dist` using `cv2.calibrateCamera(objpoints, imgpoints, shape, None, None)`. Using `mtx, dist`,
I am able to correct a distorted image as seen below.


![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

Using `mtx, dist` computed from previous step, I am able to correct the actualy image using a helper function called `cal_undistort`.
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I first convert the distortion-corrected image to the color space of hls. Then I select the S channel to run the
sobel operator on x direction. I filter the binary image with a threshold `sx_thresh=(10, 100)` to maximize number of pixels show in the lane.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

Based on the straight_lines1.jgp, I eye balled to pick the src and the dest to be:

```python
src = np.float32([[200, 720],[590, 450],[690, 450], [1110, 720]])
dest = np.float32([[350,720],[350, 0],[950, 0],[950, 720]])
```

I also plot the src points using:

```python
    plt.plot(200, 720, 'ro')
    plt.plot(590, 450, 'ro')
    plt.plot(690, 450, 'ro')
    plt.plot(1110, 720, 'ro')
    plt.imshow(img)
    plt.show()
```

to make sure that they are on the lanes.


![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

I basically used the sliding window approach the fine all the relvant lanes pixels then used `np.polyfit` to find a second order polynomial to represents each lane. And the proceed to fill the space between the lanues.

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines 8 in a function called `measure_curvature_real` in my code in `example.ipynb`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines 9 to 10 in my code in `example.ipynb`

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I tested my pipline on the challenge video. it doesn't do well because the line finding algo didn't do a good job due to shawdows and different color of lanes. I think I need to have a better filters and threshold to find the lanes clear in different situations.
