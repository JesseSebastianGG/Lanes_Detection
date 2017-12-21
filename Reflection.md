## Reflection

---

[//]: # (Image References)

[image1]: ./pipeline.jpg "Pipeline"

---

### 1. Pipeline

The current pipeline consists in 7 steps: grayscale, darken, apply Gaussian blur, apply Canny edge detection, mask the region of interest, apply the Hough lines method to find lines - which I have adapted, and finally superimpose the Hough lines onto the original image.

The "darken" function reduces contrast on the road so the algorithm is less likely to pick up lines that are not lane markings.

The Hough lines step was adapted mainly by modifying the drawl_lines helper function provided. These adjustments include averageing line segments from the output of the openCV Hough process using their respective gradients and vertical-intercepts and plotting averaged lines using hard-coded inputs.

I grouped the gradients and intercepts according to the sign of the former, so as to determine which was the left and which the right lane. Note that I filtered out lines that were too shallow or steep to reasonably be lane lines - at the possible expense of discontinuities when driving across lines, and a line's image becomes instantaneously vertical, for example.

Another issue was lines being off-screen; I got around this with another error-handling exception. The first try is picking y values near the corners and finding x values; where this fails, the code starts with x-values to generate y-values.

Averaging the lines involves a struggle between accurate rendering of lane lines (a high hough probabilistic threshold, say 100) and making sure the openCV Hough function returns values (the lower the threshold, the more likely). To get around this I included an error handling process to first try to execute my code at threshold=100, and if that fails, to then try at 30, and then 10. This means that the occasional frames where my principle algorithm fails, a very similar one takes over, and the discontinuities are less abrupt compared to hard-coding in the patched line.


![alt text][image1]


### 2. Shortcomings

As mentioned, there are discontinuities.

The most pressing issue is failure to pass the  Challenge test satisfactorily. The lines start and end too high up the image, and the left line occasionally is led astray by the verge of the road.

Another issue is computational budget. This code would not work in real time.

### 3. Improvements

I would focus upstream, adding HSV filters for yellow and white before turning to greyscale. With the resulting parameter-tuning I think this would help highlight the yellow line on the left of the challenge problem.

To reduce run-time I would reduce the number steps in the pipeline, replace full-pixel-searches with probabilistic equivalents, seek c++ optimization where possible, and investigate altogether different algorithms.