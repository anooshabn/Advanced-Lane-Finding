## Writeup for Project 2

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

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it :)

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell above

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image. Thus, objp is just a replicated array of coordinates, and objpoints will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  imgpoints will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.

I then used the output objpoints and imgpoints to compute the camera calibration and distortion coefficients using the cv2.calibrateCamera() in camera_calibration() function. I stored the calibration values in 'cal_undist_pickle.p' and applied this distortion correction to the test image using the cv2.undistort() in undistort() function and obtained this result: 

![alt text][https://drive.google.com/drive/folders/1jTG68BfLegtneMFTfM_OZDQ3HCFpuUAg?usp=sharing] 

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

![alt text][https://drive.google.com/drive/folders/1jTG68BfLegtneMFTfM_OZDQ3HCFpuUAg?usp=sharing]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. (these are the functions in code: abs_sobel_thresh(), mag_thresh(), dir_threshold(), adp_thresh_grayscale(), color_thr(), combined_threshold()).  Here's are example images of my output for this step.

![alt text][https://drive.google.com/drive/folders/1jTG68BfLegtneMFTfM_OZDQ3HCFpuUAg?usp=sharing]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perspective_transform()`. This function takes as inputs an image (`img`), using source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

    vertices = np.array([[(.55*imshape[1], 0.63*imshape[0]), (imshape[1],imshape[0]),
                       (0,imshape[0]),(.45*imshape[1], 0.63*imshape[0])]], dtype=np.float32)
                       
    src= np.float32(vertices)
    
    dst = np.float32([[0.75*img.shape[1],0],[0.75*img.shape[1],img.shape[0]],
                      [0.25*img.shape[1],img.shape[0]],[0.25*img.shape[1],0]])

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][https://drive.google.com/drive/folders/1jTG68BfLegtneMFTfM_OZDQ3HCFpuUAg?usp=sharing]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

This step is defined in the function 'sliding_window() by using binary image from above steps, histogram and sliding_window technique from class.

![alt text][https://drive.google.com/drive/folders/1jTG68BfLegtneMFTfM_OZDQ3HCFpuUAg?usp=sharing]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in 'sliding_window()' function as well which outputs curvature and offest from lane center which are displayed on output video using cv2.putText(). These are calculated using Rcurve and distance between image center and lane center.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in the function `draw_on_original()`.  Here is an example of my result on a test image:

![alt text][https://drive.google.com/drive/folders/1jTG68BfLegtneMFTfM_OZDQ3HCFpuUAg?usp=sharing]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

It took a lot of time to run the code (almost 3 hours on my local machine everytime i run the code to see output video).   
Also I took a lot time to tune the hyper-parameters, tired manually and also used interactive tools to tune the parameters. 
However, this pipeline fails when lane lines are not visible in low light conditions.  
I see there are a lot of papers written and research done in this area, I would to study them and work further on this issue.