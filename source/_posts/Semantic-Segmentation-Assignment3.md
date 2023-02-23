---
title: 机器学习与计算机视觉：作业 3
date: 2023-02-17 15:26:23
categories: 语义分割
tags:
- Python
- 图像处理
---

# Machine Learning and Computer Vision
## Assigment 4

This assignment contains 2 programming exercises.

## Problem 1: Canny Edge Detection

In this problem, you are required to write a function that performs Canny Edge Detection. The function has the following specifications:

- It takes in two inputs: a grayscale image (geisel.jpg), and a threshold te. 
- It returns the edge image.
- You are allowed the use of loops.

A brief description of the algorithm is given below. Make sure your function reproduces each step as given.

### (i) Smoothing: 

It is inevitable that all images taken from a camera will contain some amount of noise. To prevent noise from being mistaken for edges, noise must be reduced. Therefore the image is first smoothed by applying a Gaussian filter. A Gaussian kernel with standard deviation $\sigma=1.4$ (shown below) is to be used. 

You can perform this filtering step by using the `scipy.signal.convolve2d()` function.

$$k=\frac1{159}\cdot\begin{bmatrix}
	2&4&5&4&2\\
	4&9&12&9&4\\
	5&12&15&12&5\\
	4&9&12&9&4\\
	2&4&5&4&2
\end{bmatrix}$$

### (ii) Finding Gradients: 

The next step is to find the horizontal and vertical gradients of the smoothed image using the Sobel operators. The gradient images in the x and y-direction, Gx and Gy are found by applying the kernels kx and ky given below. 
These operations can be performed using scipy.signal.convolve2d() in the same manner as before.

$$k_x=\begin{bmatrix}
	-1&0&1\\
	-2&0&2\\
	-1&0&1
\end{bmatrix},k_y=\begin{bmatrix}
	-1&-2&-1\\
	0&0&0\\
	1&2&1
\end{bmatrix}$$

The corresponding gradient magnitude image is computed using:

$$|G|=\sqrt{G_x^2+G_y^2}$$

and the edge direction image is calculated as follows:

$$G_\theta=\arctan\left(\dfrac{G_y}{G_x}\right)$$

### (iii) Non-maximum Suppression (NMS): 

The purpose of this step is to convert the thick edges in the gradient magnitude image to “sharp” edges. This is done by preserving all local maxima in the gradient image, and deleting everything else. This is carried out by recursively performing the following steps for each pixel in the gradient image:

- Round the gradient direction $\theta$ to nearest $45^\circ$, corresponding to the use of an 8-connected neighbourhood.
- Compare the edge strength of the current pixel with the edge strength of the pixel in the positive and negative gradient direction i.e. if the gradient direction is north ( $\theta=90^\circ$ ), then compare with the pixels to the north and south.
- If the edge strength of the current pixel is largest; preserve the value of the edge strength. If not, suppress (remove) the value.

### (iv) Thresholding: 

The edge-pixels remaining after the NMS step are (still) marked with their strength. Many of these will probably be true edges in the image, but some may be caused by noise or color variations. The simplest way to remove these would be to use a threshold, so that only edges stronger that a certain value would be preserved. Use the input te to perform thresholding on the non-maximum suppressed magnitude image.

Evaluate your canny edge detection function on `geisel.jpg` for a suitable value of te that retains the structural edges, and removes the noisy ones.

Things to turn in:

- Image afyer smoothing, the original gradient magnitude image, the image after NMS, and the final edge image after thresholding.
- The value for te that you used to produce the final edge image.


```python
import numpy as np
from imageio.v2 import imread
import matplotlib.pyplot as plt
from scipy import signal

def rgb2gray(rgb):
	h, w, _ = rgb.shape
	grayimg = np.zeros((h, w))
	for i in range(h):
		for j in range(w):
			grayimg[i, j] = np.dot(rgb[i, j, :], (306, 601, 117)) >> 10
	return grayimg

# Canny Edge Detection Function
def canny_edge(img, te):
	h, w = img.shape
	# Smoothing
	k = np.array(((2, 4, 5, 4, 2), (4, 9, 12, 9, 4), (5, 12, 15, 12, 5), (4, 9, 12, 9, 4), (2, 4, 5, 4, 2))) / 159
	smoothed = signal.convolve2d(img, k)
	# Finding Gradients
	kx = np.array(((-1, 0, 1), (-2, 0, 2), (-1, 0, 1)))
	ky = np.array(((-1, -1, -1), (0, 0, 0), (1, 2, 1)))
	Gx = signal.convolve2d(smoothed, kx)
	Gy = signal.convolve2d(smoothed, ky)
	Gr = np.sqrt(Gx ** 2 + Gy ** 2)
	Gt = np.arctan(Gy / Gx)
	# Non-maximum Suppression (NMS)
	NMSed = Gr.copy()
	direction = np.round(Gt * 4 / np.pi)
	for i in range(h):
		for j in range(w):
			match direction[i,j]:
				case -2: isLocalMax = NMSed[i, j] > NMSed[i + 1, j] and NMSed[i, j] > NMSed[i - 1, j]
				case -1: isLocalMax = NMSed[i, j] > NMSed[i + 1, j + 1] and NMSed[i, j] > NMSed[i - 1, j - 1]
				case 0: isLocalMax = NMSed[i, j] > NMSed[i, j + 1] and NMSed[i, j] > NMSed[i, j - 1]
				case 1: isLocalMax = NMSed[i, j] > NMSed[i - 1, j + 1] and NMSed[i, j] > NMSed[i + 1, j - 1]
			if not isLocalMax: NMSed[i, j] = 0
	# Thresholding
	detected_img = np.array([[NMSed[i, j] if NMSed[i, j] > te else 0 for j in range(w)] for i in range(h)])
	return smoothed, Gr, detected_img

# Import image here
# threshold
rgb = imread('geisel.jpg')

# Convert to Gray scale image, select Threshold
img = rgb2gray(rgb)
te = np.percentile(img, 88)
# Sample call
imgs, imgg, imge = canny_edge(img, te)
plt.figure(1)
plt.imshow(imgs, cmap='gray')
plt.figure(2)
plt.imshow(imgg, cmap='gray')
plt.figure(3)
plt.imshow(imge, cmap='gray')
print(te)
```

	/var/folders/qd/8r14kw9x1_50vk935vpsrv_r0000gn/T/ipykernel_1238/3512299464.py:27: RuntimeWarning: divide by zero encountered in divide
	  Gt = np.arctan(Gy / Gx)

	199.0

![](assignment3_2_2.png)
![](assignment3_2_3.png)
![](assignment3_2_4.png)

## Problem 2: Bilateral filter

Now, we will discuss a non-local filter, Bilateral filter.

The bilateral filter is a denoising algorithm that reads as:

![](fig7.png)

1. Create a `test_imbilateral(img, sigma)` function that loads the image `x = castle` and adds additive white Gaussian noise of standard deviation $\sigma=10$
2. Create in `imbilateral_naive(img, sigma, s1, s2, h)`, a function that implements the bilateral filter (except around boundaries) with four loops.
3. test your function on `y` with `s1 = s2 = 10` and `h = 1`. Zoom on the results to check that your functions are consistent with the following ones:

![](fig8.png)

4. Create function `imbilateral(y, sigma, s1, s2, h)` that implements the bilateral filter including around boundaries. The idea is again to switch the `k`, `l` loops with the `i`, `j` loops, and then make use of `imshift`. The final code should read with only two loops and deal with boundary conditions.
5. Compare the computation times.
6. Increase the noise level, and play with the search window sizes `s1` and `s2` and filtering parameter `h`.

```python
import time

#Function
def test_imbilateral(img, sigma):
	noise = sigma * np.random.randn(img.shape[0], img.shape[1])
	return img + noise

# note: noise can be generated using
# noise: sigma*np.random.randn(height,width)
def imbilateral_native(img, sigma, s1, s2, h):
	height, width = img.shape
	phi = lambda alpha: np.exp(-np.max(alpha - 2 * sigma ** 2, 0) / (16 * h * sigma ** 2))
	xbilateral = np.zeros(img.shape)
	for i in range(s1, height - s1):
		for j in range(s2, width - s2):
			Zij, Sij = 0, 0
			for k in range(-s1, s1 + 1):
				for l in range(-s2, s2 + 1):
					curphi = phi((img[i + k, j + l] - img[i, j]) ** 2)
					Zij += curphi
					Sij += curphi * img[i + k, j + l]
			xbilateral[i, j] = Sij / Zij
	return xbilateral

def imshift(x, k, l):
	h, w = x.shape
	k, l = k % h, l % w
	return np.vstack((np.hstack((x[k:, l:], x[k:, :l])), np.hstack((x[:k, l:], x[:k, :l]))))

def imbilateral(y, sigma, s1, s2, h):
	height, width = y.shape
	phi = lambda alpha: np.exp(-np.max(alpha - 2 * sigma ** 2, 0) / (16 * h * sigma ** 2))
	level = (2 * s1 + 1) * (2 * s2 + 1)
	stack = np.zeros((height, width, level), dtype=np.uint8)
	for k in range(-s1, s1 + 1):
		for l in range(-s2, s2 + 1):
			cur = (k + s1) * (2 * s2 + 1) + l + s2
			stack[:, :, cur] = imshift(y, k, l)
	xbilateral = np.zeros(img.shape)
	for i in range(height):
		for j in range(width):
			xbilateral[i, j] = np.sum(phi((stack[i, j, :] - img[i, j]) ** 2) * img[i, j]) / np.sum(phi((stack[i, j, :] - img[i, j]) ** 2))
	return xbilateral

# Import image here
# Sample call
# castle.png
img = imread('castle.png')
sigma = 10
s1, s2 = 10, 10
h = 1
noisy = test_imbilateral(img, sigma)
st = time.time()
native = imbilateral_native(noisy, sigma, s1, s2, h)
print('native method takes %lf seconds' % (time.time() - st))
plt.figure(1)
plt.imshow(native, cmap='gray')
st = time.time()
spatial = imbilateral(noisy, sigma, s1, s2, h)
print('spatial method takes %lf seconds' % (time.time() - st))
plt.figure(2)
plt.imshow(spatial, cmap='gray')
```

	native method takes 202.016382 seconds

	/var/folders/qd/8r14kw9x1_50vk935vpsrv_r0000gn/T/ipykernel_1238/3709280540.py:32: RuntimeWarning: overflow encountered in scalar negative
	  phi = lambda alpha: np.exp(-np.max(alpha - 2 * sigma ** 2, 0) / (16 * h * sigma ** 2))

	spatial method takes 2.953177 seconds

	<matplotlib.image.AxesImage at 0x11796f1f0>

![](assignment3_4_4.png)
![](assignment3_4_5.png)

## Problem 2 Adaptive Histogram Equalization

It is often found in image processing and related fields that real world data is unsuitable for direct use. This warrants the inclusion of pre-processing steps before any other operations are performed. An example of this is histogram equalization (HE) and its extension adaptive histogram equalization (AHE).

The goal of this problem is to implement a function for AHE as described in Chapter 1 of Adaptive Histogram Equalization - A Parallel Implementation2. The function has the following specifications:

<ol type="i">
	<li>The desired function AHE() takes two inputs: the image ("beach.png") im and the contextual region size win_size.</li>
	<li>Using the pseudocode in Algorithm as a reference, compute the enhanced image after AHE.</li>
	<li>You may use loops if necessary. You should not make use of any inbuilt functions for AHE or HE.</li>
	<li>The function returns one output: the enhanced image after AHE.</li>
</ol>

![](fig6.png)

Evaluate your function on the image beach.png for win size = 33, 65 and 129. In your report, include the original image, the 3 images after AHE. Make sure to resize all images to ensure they do not take up too much space. Additionally, include your answers (no more than three sentences each) to the following questions:

How does the original image qualitatively compare to the images after AHE?

```python
def AHE(im, win_size):
	h, w = im.shape
	p = int((win_size - 1) / 2)
	mirrored = np.zeros((h + win_size - 1, w + win_size - 1))
	mirrored[:p, :p] = np.rot90(im[:p, :p], 2)
	mirrored[:p, p:w + p] = np.flipud(im[:p, :])
	mirrored[:p, w + p:] = np.rot90(im[:p, w - p:], 2)
	mirrored[p:h + p, :p] = np.fliplr(im[:, :p])
	mirrored[p:h + p, p:w + p] = im.copy()
	mirrored[p:h + p, w + p:] = np.fliplr(im[:, w - p:])
	mirrored[h + p:, :p] = np.rot90(im[h - p:, :p], 2)
	mirrored[h + p:, p:w + p] = np.flipud(im[h - p:, :])
	mirrored[h + p:, w + p:] = np.rot90(im[h - p:, w - p:], 2)
	output = np.zeros((h, w))
	for x in range(p, h + p):
		for y in range(p, w + p):
			rank = 0
			for i in range(x - p, x + p + 1):
				for j in range(y - p, y + p + 1):
					if mirrored[x, y] > mirrored[i, j]: rank += 1
			output[x - p, y - p] = rank * 255 / (win_size**2)
	return output

# Import image here
# Sample call
# Plotting code below
im = imread('beach.png')
win_sizes = [33, 65, 129]
for i in range(len(win_sizes)):
	enhanced = AHE(im, win_sizes[i])
	plt.figure(i)
	plt.imshow(enhanced, cmap='gray')
```

![](assignment3_6_0.png)
![](assignment3_6_1.png)
![](assignment3_6_2.png)

## Conclusion

Have you accomplished all parts of your assignment? What concepts did you used or learned in this assignment? What difficulties have you encountered? Explain your result for each section. Please wirte one or two short paragraph in the below Markdown window (double click to edit).

**Submission Instructions**  
Remember to submit you pdf version of this notebook to Gradescope. You can find the export option at File $\rightarrow$ Download as $\rightarrow$ PDF via LaTeX