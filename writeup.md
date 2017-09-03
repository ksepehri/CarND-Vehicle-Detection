**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)

[image1]: ./writeup_media/car_extract_features_hog2.png
[image2]: ./writeup_media/car_heat_map_one_window.png
[image3]: ./writeup_media/car_hog.png
[image4]: ./writeup_media/car_noncar.png
[image5]: ./writeup_media/find_cars.png
[image6]: ./writeup_media/noncar_hog.png
[image7]: ./writeup_media/rgb_histogram.png
[image8]: ./writeup_media/sliding_windows2.png
[image9]: ./writeup_media/test_image_with_windows.png
[image10]: ./writeup_media/test_image_with_windows2.png
[image11]: ./writeup_media/test_images_heat_maps_threshold.png
[image12]: ./writeup_media/test_images_heat_maps.png
[image13]: ./writeup_media/time_images_heat_threshold_combo2.png
[image14]: ./writeup_media/spatial_bin.png
[image15]: ./writeup_media/labels_result.png
[image16]: ./writeup_media/last_frame.png

[imageX]: ./writeup_media/.png


## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in Step 3 of the IPython notebook (`vehicle_detection.ipynb`).  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image4]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `RGB` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image3]
![alt text][image6]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and toyed with orientation, pixels per cell, and cells per block until i found what worked best.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using spatial binning, color histograms, and HOG. In Step 5 I extracted features from the data (split into train and test sets) and I scaled and trained them.   

![alt text][image14]

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

In Step 7 I implemented sliding window search. I played around with different scales and overlapping. I found 64, 96, 128 to be the best window sizes and set up the areas of the image to search accordingly (lower half of the image). 

![alt text][image8]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image5]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video 
Here's a [link to my video result](./project_output.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

I then applied thresholding to the heatmap to remove false positives.

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the result of thresholding:

### Here are 6 frames and their corresponding heatmaps:

![alt text][image13]

### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all 6 frames:
![alt text][image15]

### Here the resulting bounding boxes are drawn onto the last frame in the series:

![alt text][image16]



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

My process was incremental. Each step built upon the last one and it helped troubleshooting and debugging.

One of the issues is some false positives still occur after filtering. This could be improved with further experimentation with color spaces and tuning parameters. It can also be improved with using more labeled data. Another issue is that the pipeline is pretty slow, there are other pipelines to explore like YOLO.

