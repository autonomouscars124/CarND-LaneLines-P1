# **Finding Lane Lines on the Road** 

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

[//]: # (Image References)

[image1]: ./test_images_output/output_solidWhiteCurve.jpg "Solid White Curve"

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

#### Images

My pipeline consisted of several steps. 

* Iterating through each file in the repo, applying transformations and line drawing, then writing to disk

* For each file I first converted each image to gray scale using the helper function. This entails going from a 3 channel RGB to a 1 channel gray scale

* Next, using the helper function a gaussian blur was applied to the image which is useful for removing noise.

* After removing the noise and blurring the image we want to focus on the edges of the image. This is achieved through the Canny transformation and creating a new binary file which is dependent on the thresholds passed into the Canny.

* Next, using the dimensions of the image we need to crop to regions of interest. The regions_of_interest helper function takes vertices which we grab from the image. We can make the reasonable assumption that the lane lines won't be on the top half of the image so I cropped to the lower half

* The next step involved using the Hough Transform for feature extraction to find the lines in pixel-degree space

* After that we use the weighted_img helper function which will draw the lines on top of the image with the lane markers. At that point we have the result

![alt text][image1]

* One odditiy that I experienced and read about in the slack channels was the inverting of color channels when writing to disk vs calling plt.imshow(). Writing to disk showed the colors as inverted so I had to merge them back together using cv2.merge()

#### Video

The next piece of this project entailed using videos which can be thought of as a steady stream of image frames. In this section we also wanted to modify the draw_lines() function so that we could draw a single line over the left and right lanes in the video

* Using hough lines wanted to create a line from the start to the end points

* While iterating through the frames and start/end points we want to separate the left line from the right and we can do that by inspecting the slope using the ((y2-y1)/(x2-x1))

* Positive slopes correspond to the right side so add those slopes and centers. Negative slopes correspond to the left side of the image

* While inspecting the data I found there were some large slope outliers so those were not included

* Also found that we only cared about line segments about half way up the y-axis on the image. So the region was cropped to that interest area

* With these slopes and centers we wanted to find new x1, y1, x2, y2, coordinates. These values can be calculated because we know x,y from the centers of the line and we also anchor y coordinates on bottom of image up to half way for region of interest.

### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming with this project was around plugging and testing parameter values in the different helper function calls. For example I didn't fully understand why a kernal size of 3 was sufficient in the guassian_blur() function parameter when it typically called for a range from 1-10. I found this to be the case with a few other function calls which typically took a trial and error approach

Another shortcoming was dealing with the draw_lines function() and handling white objects in adjacent lanes. For example in the challenge video a white vehicle was being passed in the right lane and that was dramatically throwing off the line building of slopes and centers.

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to have a stronger set of rules for throwing out extreme slope value outliers. During testing I found a reasonable value for both left and right lines but it was purely through trial and error. 

Another improvement would be moving these functions and parameters into a more structured object oriented design. Instead of referencing the parameter values in multiple places they could be defined as single class attributes.
