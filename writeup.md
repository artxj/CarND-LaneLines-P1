

# **Finding Lane Lines on the Road**

## Writeup

---

**Finding Lane Lines on the Road**

The goals of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on the work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/solidWhiteCurve.jpg "White curve"
[image2]: ./test_images_output/solidWhiteRight.jpg "White right"
[image3]: ./test_images_output/solidYellowCurve.jpg "Yellow curve"
[image4]: ./test_images_output/solidYellowCurve2.jpg "Yellow curve 2"
[image5]: ./test_images_output/solidYellowLeft.jpg "Yellow left"
[image6]: ./test_images_output/whiteCarLaneSwitch.jpg "White lane switch"

---

### Reflection

### 1. Pipeline

The pipeline consists of the following steps.

* Applying white and yellow masks for the original image. To do so, the image is first converted to HSV color space and then white and yellow defined colors regions are applied.
* Converting the image to the grayscale.
* Applying gaussian blur.
The previous two steps may be not needed if we define yellow and white masks accurately on the step 1. But on practice, sometimes road areas become so bright that masks basically return the whole image without changes.
* Finding Canny edges.
* Defining region of interest. The octagon was used on this step, but simple triangle also fits well and octagon gives not so much improvement over it.
* Finding lines in Hough space. Minimum line length was only 10px with 3px max gap. Interestingly even that it seems not enough for few frames of 'challenge' video where a lot of shadows were on the road or the road was very bright. So for these cases there might be a need in further decreasing line parameters as well as adjusting Canny function arguments.
* After all lines were found, they are split in two categories: left and right ones. To do so, we assume that for left line (y2 - y1)/(x2 - x1) value should be less than 0, its x coordinates be less than image width divided by 2, and its angle absolute value should be between pi/8 and pi/3 radians. For the right line restrictions are: (y2 - y1)/(x2 - x1) > 0, x coords > image width / 2 and same values for the angle.
* Left and right lines sets are averaged into one left line and one right line. Every line is extrapolated until the image bottom axis and top of region of interest. Also, for every line the weight is set that is calculated as line length. All intersections with image bottom and top axis are averaged considering their weights. The resulted two points give the result line.
* The left and right lanes are drawn above the initial image.

The results over the input test images are:
![][image1]
![][image2]
![][image3]
![][image4]
![][image5]
![][image6]


### 2. Potential shortcomings

One potential shortcoming would be what would happen when there are a lot of shadows on the road or the road is very bright (especially if the lane is yellow). In this case lines become very segmented and edges are defined very approximately. It results in very approximate average lines and thus lanes might vary significantly.

Another shortcoming could be if the road is very 'curvy'. In this case simple extrapolation won't be enough - trying to average the road turn (even small enough) might produce the very inaccurate lane.


### 3. Possible improvements

A possible improvement would be to create the lane by combining several lines connecting to each other instead of one line. It should solve the case of turns on the road.

Another potential improvement could be to decrease Hough function parameters and Canny edges arguments. It results in producing more lines so we need to modify weight function in order to make average line more accurate. It might be an improvement in case of bright light and shadows over the road when it's hard to determine only few significant lines, but figuring the correct weight can be very tricky.
