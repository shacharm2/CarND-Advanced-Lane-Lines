## Advanced Lane Finding
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)


In this project, your goal is to write a software pipeline to identify the lane boundaries in a video, but the main output or product we want you to create is a detailed writeup of the project.  Check out the [writeup template](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) for this project and use it as a starting point for creating your own writeup.  

Creating a great writeup:
---
A great writeup should include the rubric points as well as your description of how you addressed each point.  You should include a detailed description of the code used in each step (with line-number references and code snippets where necessary), and links to other supporting documents or external references.  You should include images in your writeup to demonstrate how your code works with examples.  

All that said, please be concise!  We're not looking for you to write a book here, just a brief description of how you passed each rubric point, and references to the relevant code :). 

You're not required to use markdown for your writeup.  If you use another method please just submit a pdf of your writeup.

The Project
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

## How to write a README
A well written README file can enhance your project and portfolio.  Develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777).


[//]: # (Image References)

[1_draw_corners]: output_images/1_chessboard_calibration3.jpg

[2_undistorted]: output_images/2_undistorted_chessboard_calibration3.jpg




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

![alt text][2_undistorted]

</div>

- The undistort rectifies the chessboard while distorting the boundaries of the original image, as expected

- distortion parameters were saved to cache for later use




# references

[1] [OpenCV3 Computer Vision with Python Cookbook](https://books.google.co.il/books?id=TrZTDwAAQBAJ&pg=PA122&lpg=PA122&dq=cv2+find+chessboard&source=bl&ots=3sRelPxcB2&sig=HIscYLpFDzP842niSEc3EtlYpUE&hl=en&sa=X&ved=0ahUKEwijuIWK663cAhVBUbwKHSnJDx4Q6AEIaDAF#v=onepage&q=cv2%20find%20chessboard&f=false)

[2] [OpenCV official Camera Calibration](https://docs.opencv.org/3.4/dc/dbb/tutorial_py_calibration.html)