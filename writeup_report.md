## Project: Search and Sample Return
### Writeup Template: You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---


**The goals / steps of this project are the following:**  

**Training / Calibration**  

* Download the simulator and take data in "Training Mode"
* Test out the functions in the Jupyter Notebook provided
* Add functions to detect obstacles and samples of interest (golden rocks)
* Fill in the `process_image()` function with the appropriate image processing steps (perspective transform, color threshold etc.) to get from raw images to a map.  The `output_image` you create in this step should demonstrate that your mapping pipeline works.
* Use `moviepy` to process the images in your saved dataset with the `process_image()` function.  Include the video you produce as part of your submission.

**Autonomous Navigation / Mapping**

* Fill in the `perception_step()` function within the `perception.py` script with the appropriate image processing functions to create a map and update `Rover()` data (similar to what you did with `process_image()` in the notebook). 
* Fill in the `decision_step()` function within the `decision.py` script with conditional statements that take into consideration the outputs of the `perception_step()` in deciding how to issue throttle, brake and steering commands. 
* Iterate on your perception and decision function until your rover does a reasonable (need to define metric) job of navigating and mapping.

[//]: # (Image References)

[image1]: ./misc/rover_image.jpg
[image2]: ./calibration_images/example_grid1.jpg
[image3]: ./calibration_images/example_rock1.jpg 

## [Rubric](https://review.udacity.com/#!/rubrics/916/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Notebook Analysis
#### 1. Run the functions provided in the notebook on test images (first with the test data provided, next on data you have recorded). Add/modify functions to allow for color selection of obstacles and rock samples.

![alt text][image1]

#### 1. Populate the `process_image()` function with the appropriate analysis steps to map pixels identifying navigable terrain, obstacles and rock samples into a worldmap.  Run `process_image()` on your test data using the `moviepy` functions provided to create video output of your result. 

### Autonomous Navigation and Mapping

#### 1. Fill in the `perception_step()` (at the bottom of the `perception.py` script) and `decision_step()` (in `decision.py`) functions in the autonomous mapping scripts and an explanation is provided in the writeup of how and why these functions were modified as they were.


#### 2. Launching in autonomous mode your rover can navigate and map autonomously.  Explain your results and how you might improve them in your writeup.  

**Note: running the simulator with different choices of resolution and graphics quality may produce different results, particularly on different machines!  Make a note of your simulator settings (resolution and graphics quality set on launch) and frames per second (FPS output to terminal by `drive_rover.py`) in your writeup when you submit the project so your reviewer can reproduce your results.**

Screen resolution: 1440 * 900;

Graphics Quality: Fantastic.

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

For the color threshold, I modified the color_thresh function:

```python
def color_thresh(img, rgb_thresh=(160, 160, 160),rgb_thresh_max=(255, 255, 255)):   
    color_select = np.zeros_like(img[:,:,0])
    thresh = (img[:,:,0] > rgb_thresh[0]) & (img[:,:,1] > rgb_thresh[1]) & (img[:,:,2] > rgb_thresh[2]) \
                & (img[:,:,0] < rgb_thresh_max[0]) & (img[:,:,1] < rgb_thresh_max[1]) & (img[:,:,2] < rgb_thresh_max[2])    
    color_select[thresh] = 1
    return color_select
```
In this case, color threshold is threshed = color_thresh(warped) for the terrain,  threshed_obs = color_thresh(warped,rgb_thresh=(0, 0, 0),rgb_thresh_max=(160, 160, 160)) for the obstacles and threshed_rock = color_thresh(warped,rgb_thresh=(110, 110, 0),rgb_thresh_max=(255, 255, 50)) for the rocks.

For checking the rocks, 
```python
if threshed_rock.any():
        xpix_rock, ypix_rock = rover_coords(threshed_rock)
        x_world_rock, y_world_rock = pix_to_world(xpix_rock, ypix_rock, data.xpos[data.count], data.ypos[data.count],               data.yaw[data.count], data.worldmap.shape[0], 10)
        data.worldmap[y_world_rock, x_world_rock,:] = 255
```        

After implement the process_image function into the perception.py, the autonomous model works fine. It can achieve over 60% 
fidelity after mapping 40% of the map. But sometimes, it will stuck unde an obstacle. r

