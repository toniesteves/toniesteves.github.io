---
layout: post
title: Optical Flow Tracking with OpenCV.
description: In computer vision, the Lucas Kanade method is a widely used differential method for optical flow estimation, developed by Bruce D. Lucas and Takeo Kanade. The method assumes that the flux is essentially constant in a local neighborhood of the pixel in question and solves the basic optical flux equations for all pixels in that neighborhood, by the least squares criterion.
date: 2020-02-27 15:01:35 +0300
author: toni
image: '/images/posts/20200227/cover.png'
image_caption: 'Photo by [Oliver Sjöström](https://unsplash.com/photos/m-qps7eYZl4) on [Unsplash](https://unsplash.com/)'
tags: [opencv, computer vision, python]
featured:
---


The technique known as Optical Flow allows certain points of a video frame to be located in a previous frame. This is the initial step, for example, to determine the displacement of a vehicle between two consecutive video frames.

More objectively, optical flow is the apparent movement pattern of image objects between two consecutive frames caused by object or camera movement. It is a 2D vector field, where each vector is a displacement vector, showing the movement of points from the first frame to the second.

![Optical Flow]({{site.baseurl}}/images/posts/20200227/optical-flow.jpeg){:loading="lazy"}

The image above shows a ball in motion, moving over 5 frames. The arrow shows your displacement vector along these frames. Optical stream has many applications in areas like Motion Structure, Video Compression, Video Stabilization and etc.

The Gunnar-Farneback algorithm was developed to produce results from the dense optical flow technique (ie, in a dense grid of points). The first step is to approximate each neighborhood of both frames by quadratic polynomials. Subsequently, considering these quadratic polynomials, a new signal is constructed by a global displacement. Finally, this global shift is calculated by equationing the coefficients in the yields of the quadratic polynomials. You can check out a video lecture [here](https://www.youtube.com/watch?v=a-v5_8VGV0A&t=61m30s), which perfectly explains how the Farneback algorithm works.

### Let's Code!

First let's import OpenCV and numpy


{% highlight python %}
import cv2
import numpy as np
{% endhighlight %}

We then need to define the source of the video. So let's define a variable that receives a [`cv2.VideoCapture()`](https://docs.opencv.org/2.4/modules/highgui/doc/reading_and_writing_images_and_video.html#videocapture-videocapture), or simply pass the value 0 as a parameter to get information from your webcam. Next, we will read the first frame using the [`read()`](https://docs.opencv.org/2.4/modules/highgui/doc/reading_and_writing_images_and_video.html#videocapture-read) function, in addition to setting the color scheme to grayscale using the [`cv2.cvtColor()`](https://docs.opencv.org/2.4/modules/imgproc/doc/miscellaneous_transformations.html#cvtcolor) method.

{% highlight python %}
cap = cv2.VideoCapture(0)
_, frame = cap.read()
old_gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
{% endhighlight %}

Well, initially we need to define a callback function that returns the coordinates of a specific point, mapping the vector space around it and thus starting the optical flow. In this way, we can link this function to the mouse click, so our function would look like this.

{% highlight python %}
def select_point(event, x, y, flags, params):
  global point, point_selected, old_points
  if event == cv2.EVENT_LBUTTONDOWN:
    point = (x, y)
    point_selected = True
    old_points = np.array([[x, y]], dtype=np.float32)

{% endhighlight %}


Observe que precisamos tornar esse ponto uma variável global, dada a necessidade de manipular esse ponto mais adiante no código

Vamos iniciar nossa janela principal e informar a ela que existe um callback associado a um evento do mouse.

{% highlight python %}
cv2.namedWindow("Frame")
cv2.setMouseCallback("Frame", select_point)
{% endhighlight %}

*Note that it is through the name specified in the [`namedWindow()`](https://docs.opencv.org/2.4/modules/highgui/doc/user_interface.html?highlight=namedwindow) function that we will refer to the main window everywhere in the code.*

In addition, we are going to add a control flag that will serve to start the optical flow from the click and point detection.

{% highlight python %}
point_selected = False
point = ()
old_points = np.array([[]])
{% endhighlight %}

Let's configure the video stream. For this we will read the video input frame by frame using loop. Since we want to process the entire video, we will read the frames until [`isOpened()`](https://docs.opencv.org/2.4/modules/highgui/doc/reading_and_writing_images_and_video.html#videocapture-isopened) returns true. Similar to the first frame, we will convert each frame to grayscale.

{% highlight python %}
while cap.isOpened():
  _, frame = cap.read()
  curr_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
{% endhighlight %}

In addition, we are going to update our control flag so that the location of the initial click is marked on the screen.

{% highlight python %}
if point_selected is True:
  cv2.circle(frame, point, 5, (0, 0, 255), 2)
{% endhighlight %}

Next, the optical flow will be calculated using [`calcOpticalFlowPyrLK()`](https://docs.opencv.org/2.4/modules/video/doc/motion_analysis_and_object_tracking.html), which references Pyramid Lucas Kanade. This function receives as parameters two consecutive frames, which we will define through prev_gray and gray_frame. After that, it will be necessary to define other optimization parameters required by the algorithm, which we will pass through a dictionary. Soon after detecting the new points, we will update their values inside the loop in order to update the optical flow

{% highlight python %}
if point_selected is True:
           ...
  new_points, status, error = cv2.calcOpticalFlowPyrLK(prev_gray, gray_frame, old_points, None, **lk_params)
  prev_gray = gray_frame.copy()
  old_points = new_points
{% endhighlight %}

Our parameter dictionary looks like this:

{% highlight python %}
lk_params = dict(winSize = (15, 15),
 maxLevel = 5,
 criteria = (cv2.TERM_CRITERIA_EPS | cv2.TERM_CRITERIA_COUNT, 10, 0.03))
{% endhighlight %}


*In the dictionary of mandatory parameters of the algorithm, it is possible to randomly manipulate and choose different parameters and see how that will change the results.*

*winsize: Maps an area of 15x15 pixels around the defined point.*

*maxlevel: We define the algorithm level, because the smaller the window, the fewer pixels and the better the optical flow detection (Pyramid Analysis).*

*criteria: Parameter that specifies the termination criteria and iterations of the iterative search algorithm (the more iterations, the more exhaustive the search by the points).*

Finally, we perform some cleaning.

{% highlight python %}
cap.release()
cv2.destroyAllWindows()
{% endhighlight %}

### Result

![Optical Flow Gif](https://miro.medium.com/max/640/1*nD1OGLH9ZwA_pfz4nVEV_A.gif){:loading="lazy"}

### Simple Math Intuition

Before detecting the object, the vector field around the defined point is mapped, and this happens with each new frame in which the object moves in the video. The Lucas Kanade method assumes that the displacement of the image content between two close instants (frames or frames) is small and approximately constant taking into account the pixels neighboring a point p.

Thus, optical flow, in a higher-level definition, is the movement of objects between consecutive frames of sequence, caused by the relative movement between the object and the camera. We can express the optical flow as follows:

![Optical Flow]({{site.baseurl}}/images/posts/20200227/optical_flow_math.png){:loading="lazy"}

Between consecutive frames, we can express the image intensity $$(I)$$ as a function of point $$(x,y)$$ and time $$(t)$$. So if we extract the first image $$I(x,y,t)$$ and move its pixels represented by $$(dx, dy)$$ over time $$(t)$$, we will get a new image $$I(x+dx, y+dy)$$.

Let's initially assume that the pixel intensities of an object are constant between consecutive frames.


\begin{align}
  I(x, y, t) = I(x + \delta x, y + \delta y, t + \delta t)
\end{align}

### Conclusion
I hope this material has been useful and makes sense to you, especially for beginners. In an elementary way, we present concepts related to optical flows. In addition, in the article references you can find a very useful material used to prepare this article that can help you expand your knowledge on the subject.

Remembering that any feedback, whether positive or negative, just get in touch through my twitter or linkedin or the comments below :)

### References

* [1] [A Review On Particle Image Velocimetry And Optical Flow Methods In Riverine Environment.](https://www.researchgate.net/publication/320908264_A_Review_On_Particle_Image_Velocimetry_And_Optical_Flow_Methods_In_Riverine_Environment)

* [2] [Optical Flow](https://docs.opencv.org/3.4/d4/dee/tutorial_optical_flow.html)

* [3] [Wikipedia Optical Flow](https://en.wikipedia.org/wiki/Optical_flow)

* [4] [Implementing Lucas-Kanade Optical Flow algorithm in Python](https://sandipanweb.wordpress.com/2018/02/25/implementing-lucas-kanade-optical-flow-algorithm-in-python/)