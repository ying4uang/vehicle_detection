

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/carvsnotcarshogycrcb.png
[image2]: ./examples/HOG_example.jpg
[image3]: ./examples/sliding_windows.jpg
[image4]: ./output_images/pipelinebox.png
[image5]: ./output_images/boxandheat.png
[image6]: ./examples/labels_map.png
[image7]: ./output_images/pipelinebox.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

The main entrance is the process_image function in cell 16, which calls find cars to obtain the windows and heat map and then draw out the boxes.


### Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the single_img_features() function.  I played around with the function paramters in cell 6(where you can see the output the image)

I started by reading in all the `vehicle` and `non-vehicle` images. 

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=6`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image1]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters such as colorspace 'RGB' and 'YCrCb' and 'HSV' and orient=6,9 with different pixels per cell and cels per block and it seems YCrCb and orient=6 pix_per_cell = 8 cell_per_block = 2 achieved better results

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

In cell 8, I trained a linear SVM using LinearSVC class under skilearn.svm.  Features(colorspace, spatial, histogram, hog) were extraced and concatenated using the extract features method first on cars images and non-car images. I then used the SVM libraries to train data and test on test set, the accuracy was about 0.9921.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

Inside slide_window() function I scanned the images with parameter variables such as window size and overlap to get the list of windows to be searched upon. It was then passed as a parameter of search_windows() function to search through each single image and predict wheter the window contains a car. Those windows will be appended to a list and passed to the draw_boxes() function in cell 3 for drawing out rectangles for car objects on the image.


#### 2. Show some examples of test images tobo demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image4]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](https://youtu.be/zT4eOspxuqU)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions. The apply_threshold() function in Cell 15 is to reject areas affected by false positives. However, I did not feel that different with or without the filter applied. I then used `labels = label(heat_map)` to identify individual blobs in the heatmap. What I used is the apply_threshold() function in Cell 15 to reject areas affected by false positives.

I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  




---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I have spent a lot of time tuning the parameters such as window size as I constantly have tree shadows detected as cars. To further imporve the result, I could average the results over the frames so the box sizes does not jump around.

