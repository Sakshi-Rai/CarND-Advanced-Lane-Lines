
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

[image1]: ./output_images/calibration.png "Calibration"
[image2]: ./output_images/undistort.png "Undistorted"
[image3]: ./output_images/combined_binary.png "Thresholding"
[image4]: ./output_images/polyline.png "SourceImage"
[image5]: ./output_images/polyline2.png "DistortedImage"
[image6]: ./output_images/pixel.png "Pixels"
[image7]: ./output_images/test6.png "Final Output"
[video1]: ./final_video.mp4 "Video"

### Writeup / README

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

![alt text][image1]

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I applied the distortion correction to one of the test images and this is the output:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used r channel and g channel color threshold of 130 and for s channel,i used (90-255) and gradient thresholds of (20,100) and for direction threshold,I used pi/6 to pi/2 to generate a binary image. Here's an example of my output for this step.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `pers_transform` in the file `P4.ipynb`.  The `pers_transform()` function takes as inputs an image (`gray`).the values for the source (`src`) and destination (`dst`) points are hardcoded in this function.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([[250,720],[620,450],[720,450],[1150,720]])
dst = np.float32([[250,720],[250,0],[800,0],[800,720]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 250, 720      | 250, 720        | 
| 620, 4500      | 250, 0      |
| 720, 450     | 800, 720      |
| 1150, 720      | 800, 720        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.
Source Points
![alt text][image4]
Destination Points
![alt text][image5]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The code has been implemented in function 'sliding_window()' in the file 'P4.ipynb'.Then I used sliding window approach to find the lane lines.Firstly I calculated the base position by finding the maximas in the histogram.I used 9 windows to detect the lane lines with margin of 75.I used the points that I got to fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image6]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in my code in `P4.ipynb` in function "sliding_window".I used the mathematetical formula to find the left and right curvature and then averaged the curvatures for both the lane lines.
I have added a offset of -0.4 due to my hardcoded values of destination in perspective transform in my camera position.


#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in my code in `P4.ipynb` in the function `sliding_window`.  Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./final_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I used various techniques to process image in order to get a clear position of lane lines.Firstly I calibrated the camera to avoid any distortion in the image taken by camera.Then I used perspective transform to find the bird eye view of the road.So that I can process the lane lines more efficiently.I identified the lane lines using sliding window approach.
My code is not working properly in challenge video where the lane lines are not clearly visible due to shadows.Also I need to use averaging methods of previous frames lane lines to detect the lane lines in frames where it is distorted or where the program is unable to detect them.I can also use lane width to get an estimate of lane lines the frames.
