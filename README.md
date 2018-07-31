# Advance Lane Finding

The project concentrates on detecting and tracking lanes under various road conditions. 

The final project video shows the detected laned and road area


![project_video](./output_images/project_video.gif) 





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


[5_roi_combined_mask_test1]: output_images/5_roi_combined_mask_test1.jpg

[test1]: test_images/test1.jpg

[5_rectified_test1]: output_images/5_rectified_test1.jpg

[5_roi_combined_mask_test2]: output_images/5_roi_combined_mask_test2.jpg

[test2]: test_images/test2.jpg

[5_rectified_test2]: output_images/5_rectified_test2.jpg


[5_roi_combined_mask_test3]: output_images/5_roi_combined_mask_test3.jpg

[test3]: test_images/test3.jpg

[5_rectified_test3]: output_images/5_rectified_test3.jpg


[5_roi_combined_mask_test4]: output_images/5_roi_combined_mask_test4.jpg

[test4]: test_images/test4.jpg

[5_rectified_test4]: output_images/5_rectified_test4.jpg

[5_roi_combined_mask_test5]: output_images/5_roi_combined_mask_test5.jpg

[test5]: test_images/test5.jpg

[5_rectified_test5]: output_images/5_rectified_test5.jpg

[5_roi_combined_mask_test6]: output_images/5_roi_combined_mask_test6.jpg

[test6]: test_images/test6.jpg

[5_rectified_test6]: output_images/5_rectified_test6.jpg


[5_roi_combined_mask_straight_lines1]: output_images/5_roi_combined_mask_straight_lines1.jpg

[straight_lines1]: test_images/straight_lines1.jpg

[5_rectified_lines1]: output_images/5_rectified_straight_lines1.jpg


[5_roi_combined_mask_straight_lines2]: output_images/5_roi_combined_mask_straight_lines2.jpg

[straight_lines2]: test_images/straight_lines2.jpg

[5_rectified_lines2]: output_images/5_rectified_straight_lines2.jpg

[6_hist_peak]: output_images/6_histogram_test6.jpg
[6_lane_fit]: output_images/6_lanes_fit_test6.jpg
[6_lanes_fit_straight_lines1]: output_images/6_lanes_fit_straight_lines1.jpg
[6_lanes_fit_test2]: output_images/6_lanes_fit_test2.jpg
[6_lanes_fit_test6]: output_images/6_lanes_fit_test6.jpg

[varying_windows]: output_images/varying_windows.png




# Project Pipeline

* With requirements & specification cleared this section will focus on the project itself


## Camera calibration

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.

A chessboard Camera calibration is a standard procedure in OpenCV and and thus the procedure:

### For every image:
1. Find corners in the image with `findChessboardCorners`
2. Find corners with subpixel accuracy with `cornerSubPix`
3. Show the corners (and order) with `drawChessboardCorners`

<div style="text-align:center" markdown="1">

![alt text][1_draw_corners]

</div>

4. Append corners to a detected corners list
5. Append original grid to an object corners list

### For the final list of all detected corners:
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

## Lane Masking


* Apply a distortion correction to raw images.
* Apply white balancing, by GIMP's algorithm [7]
* Use color transforms, gradients, etc., to create a thresholded binary image.
* This method is used here on an ROI and will later be used under "Detect lane pixels", on the perspective-transformed rectified images. Since it will be in the same ROI, the color and (Distorted) lane information should be highly similar.

For the purpose of masking the following class has been used, returning the relevant mask

    class MaskPipeline(object):
        def __init__(self, trapzoid_params, save=False, show=False, nimage=-1):
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

The HSL colorspace has been examined as well, as in [8], but no significant additional information has been found compared to LAB colorspace.

<div style="text-align:center" markdown="1">

![3_Lb_mask][3_Lb_mask]

</div>

The mask then goes through Morphological filtering:
* erosion 5x5 kernel - remove small noise
* opening - 1% of image width , a flat x-cross-section kernel - filtering out small x width fragments

Finally, join the masks over the trapezoid region of interest (ROI):

<div style="text-align:center" markdown="1">

![3_sobel_mask][3_combined_roi]

</div>



## Bird eye transformration

* Apply a perspective transform to rectify binary image ("birds-eye view").

The transformation has been found using the straight lines test images in order to calibrate the parameters. For the purpose of bird eye transformation, the following class has been used:

    class BirdEye(object):
        def transform(self, img):
            unwarped = cv2.warpPerspective(img, self.transform_matrix, self.shape, flags=cv2.INTER_LINEAR)
            return unwarped

the perspective transform is straight forward - four object points and four corresponding to four warped points

### Straight lines

<div style="text-align:center" markdown="1">

![4_birdeye_view_straight_lines1][4_birdeye_view_straight_lines1]

![4_birdeye_view_straight_lines2][4_birdeye_view_straight_lines2]

</div>

### Curved lines

<div style="text-align:center" markdown="1">

![4_birdeye_view_test1][4_birdeye_view_test1]

![4_birdeye_view_test2][4_birdeye_view_test2]

![4_birdeye_view_test3][4_birdeye_view_test3]

![4_birdeye_view_test4][4_birdeye_view_test4]

![4_birdeye_view_test5][4_birdeye_view_test5]

![4_birdeye_view_test6][4_birdeye_view_test6]

</div>


# Detect lane pixels

## Masking

Reuse the MaskPipeline class:

    class MaskPipeline(object):

* with a rectified trapezoid - cutting off 1/3 of the top window



| solid white curve | rectified  | solid white right                    |
| :-----: | :-------------------: | :---------------------: |
| ![][test1] | ![][5_rectified_test1] |![][5_roi_combined_mask_test1] |
| ![][test2] | ![][5_rectified_test2] |![][5_roi_combined_mask_test2] |
| ![][test3] | ![][5_rectified_test3] |![][5_roi_combined_mask_test3] |
| ![][test4] | ![][5_rectified_test4] |![][5_roi_combined_mask_test4] |
| ![][test5] | ![][5_rectified_test5] |![][5_roi_combined_mask_test5] |
| ![][test6] | ![][5_rectified_test6] |![][5_roi_combined_mask_test6] |
| ![][straight_lines1] | ![][5_rectified_lines1] |![][5_roi_combined_mask_straight_lines1] |
| ![][straight_lines2] | ![][5_rectified_lines2] |![][5_roi_combined_mask_straight_lines2] |


## Localization

For the purpose of lane (line) localization, the Localization class has been written, which handles both left and right lanes (utilizing the Line class).


    class Localization(object):
        ...
        def localize(self, img, img_file:
        ...

This class takes care of 
- lane initialization
- lane tracking
- lane overlay over image




### Histogram peak based initialization

Initialize with histogram peaks

![][6_hist_peak]


### Sliding window for lande detection

Once the initialization has started, the algorithm applies a sliding window method in order find lanes and estimate the quadratic lines, curvature and offset. As expected, straight lines should have very large curvature, while turns should have considerable lower curvature.

Once two windows have been estimated, a short polynmial is estimated to evaulate the next window X location.


| Straight lanes | Curved #1 | Curved #2 |
| :-----: | :-------------------: | :---------------------: |
| ![][6_lanes_fit_straight_lines1] | ![][6_lanes_fit_test2] |![][6_lanes_fit_test6] |


### Next frame processing - post initialization

* initialization - The following frame initialization occurs around the 
previous base of the two lines
* Window width is set by the previous fit - 2 standard deviation of the detected lane pixels with respect to the line width. This is done in order to avoid additional image artifcats that would insert outliers to the line fit (that would have been dealt with RANSAC line estimation, if required)

![][varying_windows]


For the purpose of lane handling, the Line class has been written, as suggested, taking care of the following functions, such as line averaging, abnormalities filtering, etc


    class Line(object):
        def __init__(self, basex):
        ...

### Line averaging


A buffer of the 10 recent line fits is saved. Given a new fit, outliers are removed (mean, std) and then a weighted sum over the parameters is calculated. 

For all the line fit parameters, post filtering: 


$\text{p} _m  = \sum_{i=1}^N \text{w}_i * \text{p}_i/\sum_{i=1}^N \text{w}_i$ 


* The advantage - is increased stability and robustness to both soft noise (averaging) and hard noise (salt/pepper) & outliers 

* The disadvantage - slower to respond to changes such as can be seen in the project video, for transitioning between two road areas, where one is slightly elevated, causing rapid Y-axis changes.

## Sanity 


### Bad lines

* bad fitted lines were not dealt with using previous lines as 'placeholders':

1. polyfit residual anomaly - large residuals compared with last 10 fits residuals
2. line parameters anomaly - large residuals compared with last 10 fits parameters, individually

### Curvature

Visual inspection of the lines curvature reveals 
1. Straight lines should have large curvatures > 5km
2. Curved have 1 km or less

### Stability

Although the lines were stable, it can be seen at the end of lines, the extrapolation jitters. Shortenning the line estimation would increase visual stability over the video.

# Final results

As shown at the begining, the final results of the project & challenge video are presented.

The output *videos* are under the base folder, named
1. project_video_out.mp4
2. challenge_video_out.mp4


<div style="text-align:center" markdown="1">


## Project video

![project_video](output_images/project_video.gif)

## Challenge video

![challenge_video_out](output_images/challenge_video_out.gif)

</div>


# Discussion

The project focused on advanced lane finding, with various artifacts such as small shadows to large shadows, varying gray level road colors and middle of road artifacts. The pipeline was designed to have as little as hyperparameters as possible and thus, an anomaly detection method has been widely used, as artifacts on the road can be considered abnormal to the human eye - white or yellow vs gray, rapid changes in lane direction, etc.

Another aspect the algorithm focuses on is adaptivity - measured parameters can be used to estimate next frames, including lane parameters such as direction, curvature and width used to search for upcoming estimated location of the lane.

The pitfalls of the algorithm can be identified as less robust to quick changes, due to averaging of line parameters, global thresholding (as in, non local) and thus, less robust to shadows, as been seen under the bridge, where for a few frames, line estimation freezes and continues after passing of the bridge. Another pitfal may be occlusions, where a car passing on the lane would would cause the algorithm to fail on finding the lane.

To overcome these kind of pitfalls, local identification of tunable parameters can be used,shadow removal algorithms, adaptive color value identification, adaptive tracking, as kalman filters, or pattern matching (previous lane image to estimate the folowing lane), etc. Other modification can be done in order to help the algorithm be more adaptive to quick changes, such as change-of-rate of the line parameters. Finally, integrating this algorithm with object identification and anomaly detection algorithm may help the lane identification to be more robust. 





# references

[1] [OpenCV3 Computer Vision with Python Cookbook](https://books.google.co.il/books?id=TrZTDwAAQBAJ&pg=PA122&lpg=PA122&dq=cv2+find+chessboard&source=bl&ots=3sRelPxcB2&sig=HIscYLpFDzP842niSEc3EtlYpUE&hl=en&sa=X&ved=0ahUKEwijuIWK663cAhVBUbwKHSnJDx4Q6AEIaDAF#v=onepage&q=cv2%20find%20chessboard&f=false)

[2] [OpenCV official Camera Calibration](https://docs.opencv.org/3.4/dc/dbb/tutorial_py_calibration.html)

[3] [Skeletize](http://opencvpython.blogspot.com/2012/05/skeletonization-using-opencv-python.html)

[4] [OpenCV morphological operations](https://docs.opencv.org/3.0-beta/doc/py_tutorials/py_imgproc/py_morphological_ops/py_morphological_ops.html)

[5] [sklearn RANSAC documentation](http://scikit-learn.org/stable/auto_examples/linear_model/plot_robust_fit.html) / Robust line estimation

[6] [https://github.com/JustinHeaton/Advanced-Lane-Finding](https://github.com/JustinHeaton/Advanced-Lane-Finding) / For ideas of how to present GIFs 

[7] Gimp white balance algorithm -  [GIMP's White balance](https://pippin.gimp.org/image-processing/chapter-automaticadjustments.html)

[8] [HSV filtering in lane finding example](https://github.com/Kidra521/carnd/blob/master/p1_lane_lines_detection/P1.ipynb) / A different colorspace, in which HSV colorpsace has been used. HSV (or HSL) are more device specific.

