# Finding Lane Lines on the Road
This project is one of the projects os Udacity Self Drivering Car Nanodegree Program.  The goals / steps of this project are the following:
  - Make a pipeline that finds lane lines on the road
  - Reflect on your work in a written report

# Code
Project code can be found in [P1.ipynb](https://github.com/CtheSky/udacity-finding-lanelines/blob/master/P1.ipynb)

# Reflection
## Process Pipeline
It includes 5 steps:
  1. color selection
  2. canny edge detection
  3. region mask
  4. hough transform
  5. draw lines on image
  
#### Color Selection
I choose to use `Color Selection` instead of `Grayscale`. The reason is that there are some tree shadow 
which confuse the canny edge detection. Using grayscale, I will lose the information to identify that. 
While using color selection, I can select only yellow and white colors which works fine.

#### Canny Edge Detection
I don't include `Gaussian Blur` since the implementation of `Canny Edge Detection` includes a 5x5 `Gaussian Blur` internally.
And I end up with `low threshold = 50` and `high threshold = 150`.

#### Region Mask
I want to put `Region Mask` as early as possible in the pipeline because it can save computation for later steps 
by filtering out unnessary pixels. But here I put it:
 - after `Canny Edge Detection`, because `Canny Edge Detection` will connect pixels between low and high threshold to 
 pixels above high threshold and this process might be effected by `Region Mask`.
 - before `Hough Transform`, because I think it can reduce the noise. For example, `Hough Transform` doesn't know about 
 `Region Mask` and it might connect a line cross the mask border and I will have to filter out those lines in later steps.
 To make my life easier, I choose to put it before `Hough Transform`.
   
Also, I use a trapezoidal instead of triangle since it fits the lane line better and 
filter out some unnessary pixels in the top area of triangle which will confuse the `Hough Transform`.
 
 
#### Hough Transform
I set `threshold = 25` since for some short line segments there're not many points to pass the threshold. And also
I set `min_line_len = 20` and `max_line_gap = 100` for them to be picked and connected.

#### Draw Lines on Image
Here I modify `draw_lines` function to support full extent of the lane and use a global variable `enable_average_drawing`
to switch between these two mode which is convenient when debugging.

The way I modify the function is:
  1. compute the gradient of each line
  2. split lines into two parts depending on whether gradient is positive or not
  3. for each set, compute the mean and std and filter out the lines with high std (optional, not implemented)
  4. for each set, compute an average line with full extent of the lane
  5. draw the lines on the image
  
#### ShortComings
It's not robust to unknown situations. Like in the challenge, I find the problem and fix it but there're still other 
problems. My pipeline doesn't have a mechanism to prevent unknown problems to change the result too much.

#### Possible improvements
When averaging lines, I can compute the mean and std and filter out the lines with high std. Like how much percent to filter out,
it would be another paramenter to tune and pipeline will be more able to handler unknown problems or noise.

The reason that I didn't implement it is that I want to start with a dirty, quick implementation, iterate fast and
only add features when it could improve the performance. For the purpose of project, it won't improve performance but
for a production environment I will collect more data, implement it and see how it changes the performance.
