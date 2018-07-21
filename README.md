## Advanced Lane Finding
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


In this project, your goal is to write a software pipeline to identify the lane boundaries in a video, but the main output or product we want you to create is a detailed writeup of the project.  Check out the [writeup template](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) for this project and use it as a starting point for creating your own writeup.  

Creating a great writeup:
---
A great writeup should include the rubric points as well as your description of how you addressed each point.  You should include a detailed description of the code used in each step (with line-number references and code snippets where necessary), and links to other supporting documents or external references.  You should include images in your writeup to demonstrate how your code works with examples.  

All that said, please be concise!  We're not looking for you to write a book here, just a brief description of how you passed each rubric point, and references to the relevant code :). 

You're not required to use markdown for your writeup.  If you use another method please just submit a pdf of your writeup.

# Requirements

## The Project
---

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

The images for camera calibration are stored in the folder called `camera_cal`.  The images in `test_images` are for testing your pipeline on single frames.  If you want to extract more test images from the videos, you can simply use an image writing method like `cv2.imwrite()`, i.e., you can read the video in frame by frame as usual, and for frames you want to save for later you can write to an image file.  

To help the reviewer examine your work, please save examples of the output from each stage of your pipeline in the folder called `output_images`, and include a description in your writeup for the project of what each image shows.    The video called `project_video.mp4` is the video your pipeline should work well on.  

The `challenge_video.mp4` video is an extra (and optional) challenge for you if you want to test your pipeline under somewhat trickier conditions.  The `harder_challenge.mp4` video is another optional challenge and is brutal!

If you're feeling ambitious (again, totally optional though), don't stop there!  We encourage you to go out and take video of your own, calibrate your camera and show us how you would implement this project from scratch!

---

## Project specification
---
[udacity](https://review.udacity.com/#!/rubrics/1966/view)

### Camera Calibration Criteria
 - Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

### Pipeline (test images) Criteria

- Provide an example of a distortion-corrected image.
- Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image. Provide an example of a binary image result.
- Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.
- Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?
- Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.
- Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

### Pipeline (video) Criteria
- Provide a link to your final video output. Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!)

### Discussion
- Briefly discuss any problems / issues you faced in your implementation of this project. Where will your pipeline likely fail? What could you do to make it more robust?



# Project Pipeline

With requirements & specification cleared this section will focus on the project itself



[//]: # (Image References)

[1_draw_corners]: output_images/1_chessboard_calibration2.jpg

[2_undistorted]: output_images/2_undistorted_chessboard_calibration2.jpg

[2_lane]: output_images/2_test1.jpg

[3_sobel_mask]: output_images/3_roi_sobel_mask_straight_lines1.jpg

[3_Lb_mask]: output_images/3_Lb_mask_straight_lines1.jpg

[3_combined_roi]: output_images/3_roi_combined_mask_straight_lines1.jpg

[4_birdeye_view_straight_lines1]: output_images/4_birdeye_view_straight_lines1.jpg

[4_birdeye_view_straight_lines2]: output_images/4_birdeye_view_straight_lines2.jpg

[4_birdeye_view_test1]: output_images/4_birdeye_view_test1.jpg

[4_birdeye_view_test2]: output_images/4_birdeye_view_test2.jpg

[4_birdeye_view_test3]: output_images/4_birdeye_view_test3.jpg

[4_birdeye_view_test4]: output_images/4_birdeye_view_test4.jpg

[4_birdeye_view_test5]: output_images/4_birdeye_view_test5.jpg

[4_birdeye_view_test6]: output_images/4_birdeye_view_test6.jpg

# Camera calibration

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.

A chessboard Camera calibration is a standard procedure in OpenCV and and thus the procedure:

## For every image:
1. Find corners in the image with `findChessboardCorners`
2. Find corners with subpixel accuracy with `cornerSubPix`
3. Show the corners (and order) with `drawChessboardCorners`

<div style="text-align:center" markdown="1">

![alt text][1_draw_corners]

</div>

4. Append corners to a detected corners list
5. Append original grid to an object corners list

## For the final list of all detected corners:
1. Calibrate camera with `calibrateCamera`

    - this has been run with try/except claws due to OpenCV versions' `calibrateCamera` functionality mismatches
2. Rectification - Undistort original images using `undistort` and the distortion coefficients
<div style="text-align:center" markdown="1">

![undistorted chessboard][2_undistorted]

</div>

For the purpose of rectifying the following class has been used:
    
    class Rectification(object):
        def rectify(self, img, axes=None, filename=''):
            ...
            ...
            return undistorted_img

- The undistort rectifies the chessboard while distorting the boundaries of the original image, as expected

- distortion parameters were saved to cache for later use

Finally, show the undistorted lane image

<div style="text-align:center" markdown="1">

![undistorted lane][2_lane]

</div>

# Lane Masking


* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.

For the purpose of masking the following class has been used, returning the relevant mask

    class MaskPipeline(object):
        def process(self, img, axes=None, filename=''):
            ...
            ...
            return roi_mask

The class used two methods for lane mask extraction. The main idea is that lanes are abnormalities on the road. While many abnormalities may exist, they have a certain structure that can be used to filter out. Thus, for all masking methods, an image adaptive threshold has been used to mask the lanes.

1. Sobel operator masking 
    * Mask the ROI of relevant lanes, threshold by the 99.5 percentile

<div style="text-align:center" markdown="1">

![3_sobel_mask][3_sobel_mask]

</div>


2. CIELab Colorspace
   * CIELab is a perceptually uniform colorspace. 
   * L is lightness axis - similar to gray scale without any color data encoded into it
   * a is the green-red axis - expect no relevant information
   * b is the blue-yellow axis
   * Build a joint Lb mask 


<div style="text-align:center" markdown="1">

![3_Lb_mask][3_Lb_mask]

</div>

Finally, join the masks over the trapezoid region of interest (ROI):

<div style="text-align:center" markdown="1">

![3_sobel_mask][3_combined_roi]

</div>



# Bird eye transformation

* Apply a perspective transform to rectify binary image ("birds-eye view").

The transformation has been found using the straight lines test images in order to calibrate the parameters. For the purpose of bird eye transformation, the following class has been used:

    class BirdEye(object):
        def transform(self, img):
            unwarped = cv2.warpPerspective(img, self.transform_matrix, self.shape, flags=cv2.INTER_LINEAR)
            return unwarped

the perspective transform is straight forward - four object points and four corresponding to four warped points

### Test results - Straight lines

<div style="text-align:center" markdown="1">

![4_birdeye_view_straight_lines1][4_birdeye_view_straight_lines1]

![4_birdeye_view_straight_lines2][4_birdeye_view_straight_lines2]

</div>

### Test results - Curved lines

<div style="text-align:center" markdown="1">

![4_birdeye_view_test1][4_birdeye_view_test1]

![4_birdeye_view_test2][4_birdeye_view_test2]

![4_birdeye_view_test3][4_birdeye_view_test3]

![4_birdeye_view_test4][4_birdeye_view_test4]

![4_birdeye_view_test5][4_birdeye_view_test5]

![4_birdeye_view_test6][4_birdeye_view_test6]

</div>







# references

[1] [OpenCV3 Computer Vision with Python Cookbook](https://books.google.co.il/books?id=TrZTDwAAQBAJ&pg=PA122&lpg=PA122&dq=cv2+find+chessboard&source=bl&ots=3sRelPxcB2&sig=HIscYLpFDzP842niSEc3EtlYpUE&hl=en&sa=X&ved=0ahUKEwijuIWK663cAhVBUbwKHSnJDx4Q6AEIaDAF#v=onepage&q=cv2%20find%20chessboard&f=false)

[2] [OpenCV official Camera Calibration](https://docs.opencv.org/3.4/dc/dbb/tutorial_py_calibration.html)

