# **Finding Lane Lines on the Road** 

---

Brian Chesney
December 6, 2018

**Finding Lane Lines on the Road**

The goal of thie project was to make a pipeline that finds lane lines on the road


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I used a Gaussian function with a kernel size of 5 to slightly blur the image.  Then I used a Canny edge detect on the blurred, grayscale image.  Next, I created a polygon mask to indicate the region of the image that will contain the lanes.  Finally, I applied a Hough transform to identify the lines in the image.

![A test image converted to gray scale][image1]

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by binning the pairs of points represnting each line into either left or right based on the the slope.  The left line should have a positive slope, somewhere between 0.5 and 1.0 and the right line should have a negative slope somewhere between -0.5 and -1.0.  Lines that did not fit this criteria were discarded.

While I was binning on slope, I also kept track of which point would most likely represent convergence for the left line I wanted to draw as well as the right line I would draw.  I chose the point farthest up the image (smallest y value in image indices) for left and for right, each.

Next, I avergaed the slopes of all the left points.  Then I found the y-intercept of the line represented by the averaged slope of all y-points and the convergent point chosen for the left line, mentioned in the previous paragraph.  I had to taken into account a difference between the way the images are indexed in terms of x, y and the traditional line representation in Cartesian co-ordinates.  Then I drew the left line on top of the image from the y-intercept I had found for the left line to the convergence point for the left line.

Then I averaged all of the slopes for the right line.  I then found the y-intercept for the right line in the same manner as for the left line, using the avergaed slope and the convergence point chosen for the right line.  Next, I found the x-intercept of the right line from the y-intercept, averaged slope and the boundary of the image.  Actually it was the intercept of on the horizontal boundary of the image.  Finally, I drew a line from the intercept of the horizontal boundary of the image to the convergence point for the right line.


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the aspect ratio of the image changes.  I tried to keep the polygon parameterized in terms of the dimension of the image, but I did not test it on different size images. 

Another shortcoming could be when the lanes are in a different part of the field of view.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to improve the filtering on the slope of the left and right lines, currently it is a simple average for each line.

Another potential improvement could be to look across frames and potentially do some averaging on the left and right convergence points as well as the slopes for the left and right lines.

### 4. Challenge video

I tried the lane finding algorithm on the challenge video.  It found the lanes on some of the frames, but on other frames, it was way off to the left, spotting convergence in the gradient between the trees and the sky.  This leads to very jumpy behvior which could be filtered a bit more, but is not the root of the problem.  To make this work correctly I would have to do a better job of deriving the mask polygon to apply in the pipeline.  The perspective in the challenge video is a little different than the other videos and so requires a different mask.

