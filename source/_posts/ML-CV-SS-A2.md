---
title: 机器学习与计算机视觉：作业 2
date: 2023-02-12 13:20:00
categories: 语义分割
tags:
- Python
- 图像处理
---

# Machine Learning and Computer Vision
## Assigment 2

This assignment contains 3 programming exercises. Please review the pdf file for more detail information.

## Problem 1 Image shift

Shifting an image x of size (n1, n2) in a direction (k, l) consists in creating a new image xshifted of size
(n1, n2) such that

$$x^\mathrm{shifted}(i,j)=x(i+k,j+l).$$

In practice, boundary conditions should be considered for pixels (i, j) such that (i + k, j + l) not equal to [0, n1-1] x [0, n2-1]. 

A typical example is to consider periodical boundary conditions such that

$$x^\mathrm{shifted}(i,j)=x(i+k\mod n_1,j+l\mod n_2).$$

Create in imshift function implementing the shifting of an image x in periodical boundary, such as the following image(b) Shifted in the direction (k,l) by (+100,-50):
![](fig3.png)

Hint: First write it using loops, and next try to get rid of the loops.


```python
import numpy as np
from imageio.v2 import imread
import matplotlib.pyplot as plt

# def imshift(x, k, l):
# 	h, w = x.shape
# 	k, l = k % h, l % w
# 	xshifted = np.zeros((h, w), dtype=np.uint8)
# 	for i in range(h):
# 		for j in range(w):
# 			xshifted[i, j] = x[i + k if i + k < h else i + k - h,
# 							   j + l if j + l < w else j + l - w]
# 	return xshifted

def imshift(x, k, l):
	h, w = x.shape
	k, l = k % h, l % w
	return np.vstack((np.hstack((x[k:, l:], x[k:, :l])), np.hstack((x[:k, l:], x[:k, :l]))))

#Sample call and Plotting code
#“lake.png” and "windmill.png"
k, l = 100, -50
x = imread('lake.png')
plt.subplot(1, 2, 1)
plt.imshow(imshift(x, k, l), cmap='gray')
y = imread('windmill.png')
plt.subplot(1, 2, 2)
plt.imshow(imshift(y, k, l), cmap='gray')
```

	<matplotlib.image.AxesImage at 0x11d57a6b0>
![](assignment2_2_1.png)

Check on x = windmill.png and y = lake.png, if this operation is linear, i.e., `imshift(a * x + b * y, k, l) == a * imshift(x, k, l) + b * imshift(y, k, l) ?` After shifting the image in the direction (k, l), shift it back in the direction (k, l). Interpret the results. Which shift is one-to-one?

```python
a = np.random.randint(0, 16)
b = np.random.randint(0, 16)
print((imshift(a * x + b * y, k, l) == a * imshift(x, k, l) + b * imshift(y, k, l)).all())

plt.subplot(1, 2, 1)
plt.imshow(imshift(imshift(x, k, l), -k, -l), cmap='gray')
plt.subplot(1, 2, 2)
plt.imshow(imshift(imshift(y, k, l), -k, -l), cmap='gray')
```

	True
	<matplotlib.image.AxesImage at 0x11d6c1090>
![png](assignment2_4_2.png)

The operation is linear, as the left and right side of the equation is identical.

Shifting back gives exactly the same image as the original one. The shifting is one-to-one.

## Problem 2 Convolution

In this problem, we intend to explore and implement 2D convolution.

![](conv.png)

First, Create imkernel function that produces a function handle nu implementing a convolution kernel functions on the finite support (-s1, s1)x(-s2, s2). In this case, we specifies the ‘gaussian’ kernel as following.

Create imconvolve_naive function that performs(except around boundaries) the convolution between x (image) and v (kernel) with four nested loops [ The outer 2 loops for looping over the image, inner 2 loops for looping over the kernel].

Create imconvolve_spatial function that performs the convolution between x (image) and v (kernel) including around boundaries. The spatial method intend to create a stacked-up image, which is a 3D matrix with dimension [ image_height x image_width x total_kernel_number]. The final code should read with only two loops. 

The idea is to switch the inner loops with the outer loops, and then make use of imshift to eliminate the need to use the outter loop to go over the entire image, but use shift function to shift the image along the inner loop and store the each result into your 3D matrix. With the stacked-up matrix, you can perform convolution calculation with ease. on the 3D matrix.


Write a script test_imconvolve function that compares the results and the execution times of imconvolve_naive and imconvolve_spatial, give comment on the execution times of two methods. You should have similar result like:

![](fig5.png)

```python
import numpy as np
from imageio.v2 import imread
import matplotlib.pyplot as plt
import time

def imkernel(tau, s1, s2):
	'''
	The kernel (window) function already, you can use that function to generate your kernel. 

	Note: the function is slightly difference than just a matrix. As you can see it returned a 
	lambda function object. You need to assign location of the kernel, then it will return 
	specified value in that location of the kernel.
	
	For example, we want a 3x3 window, (note: in this function, we said the center point to be 
	location (0,0), so s1 is the absolution distance to the center point, for example: s1 means 
	from -1 to 1): 

	nu = imkernel(tau,s1,s1);   #<------- generated a 3x3 window funtion
	nu(-1,-1)  #<--------- this will return the top left corner value of the kernel
	nu(0,0)  #<--------- this will return the center value of the kernel

	'''
	w = lambda i, j: np.exp(-(i**2 + j**2) / (2 * tau**2))
	# normalization
	i, j = np.mgrid[-s1:s1, -s2:s2]
	Z = np.sum(w(i, j))
	nu = lambda i, j: w(i, j) / Z * (np.absolute(i) <= s1 & np.absolute(j) <= s2)
	return nu

# Create imconvolve_naive function,
def imconvolve_naive(im, nu, s1, s2):
	(n1, n2) = im.shape
	# (s1,s2)=win_size
	xconv = np.zeros((n1, n2))
	for i in range(s1, n1 - s1):
		for j in range(s2, n2 - s2):
			sum = 0
			for k in range(-s1, s1 + 1):
				for l in range(-s2, s2 + 1):
					sum += nu(k, l) * im[i + k, j + l]
			xconv[i, j] = sum / ((2 * s1 + 1) * (2 * s2 + 1))
	return xconv

#Create imconvolve_spatial function
def imconvolve_spatial(im, nu, s1, s2):
	n1, n2 = im.shape
	level = (2 * s1 + 1) * (2 * s2 + 1)
	stack = np.zeros((n1, n2, level), dtype=np.uint8)
	kernel = np.zeros(level)
	for k in range(-s1, s1 + 1):
		for l in range(-s2, s2 + 1):
			cur = (k + s1) * (2 * s2 + 1) + l + s2
			stack[:, :, cur] = imshift(im, k, l)
			kernel[cur] = nu(k, l)
	xconv = np.zeros((n1, n2), dtype=np.uint8)
	for i in range(n1):
		for j in range(n2):
			xconv[i, j] = np.sum(np.multiply(stack[i, j, :], kernel))
	return xconv

#Sample call and Plotting code
tau = 1
s1 = 4
s2 = 4
nu = imkernel(tau, s1, s2)
im = imread('windmill.png')
plt.subplot(1, 3, 1)
plt.imshow(im, cmap='gray')
st = time.time()
res = imconvolve_naive(im, nu, s1, s2)
et = time.time()
plt.subplot(1, 3, 2)
plt.imshow(res, cmap='gray')
print('imconvolve_naive execution time: %.3fs' % (et - st))
st = time.time()
res = imconvolve_spatial(im, nu, s1, s2)
et = time.time()
plt.subplot(1, 3, 3)
plt.imshow(res, cmap='gray')
print('imconvolve_spatial execution time: %.3fs' % (et - st))
```

	imconvolve_naive execution time: 10.390s
	imconvolve_spatial execution time: 0.142s
![png](assignment2_7_1.png)

The `imconvolve_spatial` method takes only one tenth of the time of the `imconvolve_naive` method. It seems that loops greatly increse the running time of a certain algorithm.

## Problem 3: Order-statistic filtering

Order-statistic filters (OSF) are local filters that are only based on the ranking of pixel values inside a sliding window.
1. Create in `imstack(img, s1, s2)` function that creates a stack `xstack` of size n1 × n2 × s, which s = (2s1 + 1)(2s2 + 1) from the n1 × n2 image x, such that `xstack(i, j, :)` contains all the values of x in the neighborhood (−s1, s1) × (−s2, s2). This function should take into account the four possible boundary conditions.

	Hint: you can use imshift, which we implemented in assignment 1, and only two loops for −s1 <= k <= s1 and −s2<= l<= s2.
	
2. Create in `imosf()` function

	function `imosf(x, type, s1, s2)` that implements order-statistic filters, returns `xosf`. imosf should first call imstack, next sort the entries of the stack with respect to the third dimension, and create the suitable output `xosf` according to the string type as follows:
	- `'median'`: select the median value,
	- `'erode'`: select the min value,
	- `'dilate'`: select the max value,
	- `'trimmed'`: take the mean after excluding at least 25% of the extreme values on each side.
	
3. Create in `imopening()` and `imclosing()` function that performs the opening and closing by the means of OSF filters.

	![](fig6.png)

4. Load `castle.png`. Write a script to test `imosf()` that loads the image `x = castle` and create a corrupted version of image x with 10% of impulse noise (salt and pepper)

	Apply your OSF filters and zoom on the results to check that your results are consistent with the following ones:

	![](fig7.png)

	Note: Typo on image, (d) -- opening , (e) -- closing

```python
#Function
def imstack(img, s1, s2):
	n1, n2 = img.shape
	xstack = np.zeros((n1, n2, (2 * s1 + 1) * (2 * s2 + 1)), dtype=np.uint8)
	for k in range(-s1, s1 + 1):
		for l in range(-s2, s2 + 1):
			xstack[:, :, (k + s1) * (2 * s2 + 1) + l + s2] = imshift(img, k, l)
	return xstack

def imosf(x, type, s1, s2):
	xstack = imstack(x, s1, s2)
	n1, n2 = x.shape
	level = (2 * s1 + 1) * (2 * s2 + 1)
	xosf = np.zeros((n1, n2), dtype=np.uint8)
	for i in range(n1):
		for j in range(n2):
			sorted = np.sort(xstack[i, j, :])
			match type:
				case 'median':
					xosf[i, j] = sorted[int((level - 1) / 2)]
				case 'erode':
					xosf[i, j] = sorted[0]
				case 'dilate':
					xosf[i, j] = sorted[-1]
				case 'trimmed':
					xosf[i, j] = np.mean(sorted[level // 10 : 9 * level // 10])
	return xosf

def imopening(img, s1, s2):
	return imosf(imosf(img, 'erode', s1, s2), 'dilate', s1, s2)

def imclosing(img, s1, s2):
	return imosf(imosf(img, 'dilate', s1, s2), 'erode', s1, s2)

#Import image here
# Sample call
# castle.png
img = imread('windmill.png')
n1, n2 = img.shape
prob = 0.1
least, most = prob / 2, 1 - prob / 2
noisy = img.copy()
for i in range(n1):
	for j in range(n2):
		ran = np.random.random()
		if ran < least: noisy[i, j] = 0
		if ran > most: noisy[i, j] = 255

s1, s2 = 1, 1
plt.subplot(1, 5, 1)
plt.imshow(noisy, cmap='gray')
plt.subplot(1, 5, 2)
plt.imshow(imosf(noisy, 'median', s1, s2), cmap='gray')
plt.subplot(1, 5, 3)
plt.imshow(imosf(noisy, 'trimmed', s1, s2), cmap='gray')
plt.subplot(1, 5, 4)
plt.imshow(imopening(noisy, s1, s2), cmap='gray')
plt.subplot(1, 5, 5)
plt.imshow(imclosing(noisy, s1, s2), cmap='gray')
```

	<matplotlib.image.AxesImage at 0x11d953400>
![png](assignment2_10_1.png)

## Conclusion

Have you accomplished all parts of your assignment? What concepts did you used or learned in this assignment? What difficulties have you encountered? Explain your result for each section. Please wirte one or two short paragraph in the below Markdown window (double click to edit).

---
**Submission Instructions**  
Remember to submit you pdf version of this notebook to Gradescope. You can find the export option at File $\rightarrow$ Download as $\rightarrow$ PDF via LaTeX