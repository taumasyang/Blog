---
title: 机器学习与计算机视觉：作业 1
date: 2023-02-01 22:23:22
categories: 语义分割
tags:
- 机器学习
- 计算机视觉
---

# Machine Learning and Computer Vision
## Assigment 1

Welcome to Oversea Research Program - Machine Learning and Computer Vision. This program will give you a comprehensive introduction to computer vison providing board coverage including low level vision, inferring 3D properties from image, and object recognition. We will be using a varity of tools in this class that will require some initial configuration. To ensure everything smoothly moving forward, we will setup the majority of the tools to be used in this course in this assignment. You will also practice some basic image manipulation techniques. At the end, you will need to export this Ipython notebook as pdf.

### Python

**Python**

We will use the Python programming language for all assignments in this course, with a few popular libraries (numpy, matplotlib). And assignment starters will be given in format of the browser-based Jupyter/Ipython notebook that you are currently viewing. If you have previous knowledge in Matlab, check out the [numpy for Matlab users](https://docs.scipy.org/doc/numpy-dev/user/numpy-for-matlab-users.html) page. The section below will serve as a quick introduction on Numpy and some other libraries.

**Setup Python environment**

We can install Anaconda from the links given below. You can setup your environment using Anaconda for Python 2.7 or 3.6. 

The Anaconda versions for Python can be downloaded from the following:

https://www.anaconda.com/download/#linux

https://www.anaconda.com/download/#macos

https://www.anaconda.com/download/#windows

After downloading and installing one of these, one needs to set the /path/to/anaconda2 in $PATH variable.

Then we can run >> jupyter notebook from terminal or use the Anaconda UI.
Otherwise a more "geeky" procedure for Linux users is given here: 

https://www.digitalocean.com/community/tutorials/how-to-set-up-a-jupyter-notebook-to-run-ipython-on-ubuntu-16-04.

For submitting your assignments, you can submit your python notebook file with result shown or PDF file. PDF file is needed to setup using LaTex. 

Please use nbconvert tool for this. This can be installed from instructions given on:
nbconvert: "conda install nbconvert" (or http://nbconvert.readthedocs.io/en/latest/install.html) 
The above link also gives instructions for installing Pandoc and Latex for different OS. Please follow those instructions as installing these might be required for nbconvert.

## Get started with Numpy

Numpy is the fundamental package for scientific computing with Python. It provides a powerful N-dimensional array object and functions for working with these arrays.

### Arrays

```python
import numpy as np

v = np.array([1, 0, 0])        # a 1d array
print("1d array")
print(v)
print(v.shape)                 # print the size of v
v = np.array([[1], [2], [3]])  # a 2d array
print("\n2d array")
print(v)
print(v.shape)                 # print the size of v, notice the difference
v = v.T                        # transpose of a 2d array

m = np.zeros([2, 3])           # a 2x3 array of zeros
v = np.ones([1, 3])            # a 1x3 array of ones
m = np.eye(3)                  # identity matrix
v = np.random.rand(3, 1)       # random matrix with values in [0, 1]
m = np.ones(v.shape) * 3       # create a matrix from shape
```

    1d array
    [1 0 0]
    (3,)
    
    2d array
    [[1]
     [2]
     [3]]
    (3, 1)

### Array indexing

```python
import numpy as np

m = np.array([[1, 2, 3], [4, 5, 6]])  # create a 2d array with shape (2, 3)
print("Access a single element")
print(m)
print(m[:2,1:3])

print(m[0, 2])                        # access an element
m[0, 2] = 252                         # a slice of an array is a view into the same data;
print("\nModified a single element")
print(m)                              # this will modify the original array

print("\nAccess a subarray")
print(m[1, :])                        # access a row (to 1d array)
print(m[1:, :])                       # access a row (to 2d array)
print("\nTranspose a subarray")
print(m[1, :].T)                      # notice the difference of the dimension of resulting array
print(m[1:, :].T)                     # this will be helpful if you want to transpose it later

# Boolean array indexing
# Given a array m, create a new array with values equal to m 
# if they are greater than 0, and equal to 0 if they less than or equal 0

m = np.array([[3, 5, -2], [5, -1, 0]])
n = np.zeros(m.shape)
n[m > 0] = m[m > 0]
print("\nBoolean array indexing")
print(n)
```

    Access a single element
    [[1 2 3]
     [4 5 6]]
    [[2 3]
     [5 6]]
    3
    
    Modified a single element
    [[  1   2 252]
     [  4   5   6]]
    
    Access a subarray
    [4 5 6]
    [[4 5 6]]
    
    Transpose a subarray
    [4 5 6]
    [[4]
     [5]
     [6]]
    
    Boolean array indexing
    [[3. 5. 0.]
     [5. 0. 0.]]

### Operations on array
**Elementwise Operations**

```python
import numpy as np

a = np.array([[1, 2, 3], [2, 3, 4]], dtype=np.float64)
print(a * 2)                                            # scalar multiplication
print(a / 4)                                            # scalar division
print(np.round(a / 4))
print(np.power(a, 2))
print(np.log(a))


b = np.array([[5, 6, 7], [5, 7, 8]], dtype=np.float64)
print(a + b)                                            # elementwise sum
print(a - b)                                            # elementwise difference
print(a * b)                                            # elementwise product
print(a / b)                                            # elementwise division
```

    [[2. 4. 6.]
     [4. 6. 8.]]
    [[0.25 0.5  0.75]
     [0.5  0.75 1.  ]]
    [[0. 0. 1.]
     [0. 1. 1.]]
    [[ 1.  4.  9.]
     [ 4.  9. 16.]]
    [[0.         0.69314718 1.09861229]
     [0.69314718 1.09861229 1.38629436]]
    [[ 6.  8. 10.]
     [ 7. 10. 12.]]
    [[-4. -4. -4.]
     [-3. -4. -4.]]
    [[ 5. 12. 21.]
     [10. 21. 32.]]
    [[0.2        0.33333333 0.42857143]
     [0.4        0.42857143 0.5       ]]

**Vector Operations**

```python
import numpy as np

a = np.array([[1, 2], [3, 4]])
print("sum of array")
print(np.sum(a))                # sum of all array elements
print(np.sum(a, axis=0))        # sum of each column
print(np.sum(a, axis=1))        # sum of each row
print("\nmean of array")
print(np.mean(a))               # mean of all array elements
print(np.mean(a, axis=0))       # mean of each column
print(np.mean(a, axis=1))       # mean of each row
```

    sum of array
    10
    [4 6]
    [3 7]
    
    mean of array
    2.5
    [2. 3.]
    [1.5 3.5]

**Matrix Operations**

```python
import numpy as np

a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6], [7, 8]])
print("matrix-matrix product")
print(a.dot(b))                 # matrix product
print(a.T.dot(b.T))

x = np.array([1, 2])  
print("\nmatrix-vector product")
print(a.dot(x))                 # matrix / vector product
```

    matrix-matrix product
    [[19 22]
     [43 50]]
    [[23 31]
     [34 46]]
    
    matrix-vector product
    [ 5 11]

### SciPy image operations

SciPy builds on the Numpy array object and provides a large number of functions useful for scientific and engineering applications. We will show some examples of image operation below which are useful for this class. 

```python
from imageio import imread, imsave
import numpy as np

img = imread('Lenna.png')  # read an JPEG image into a numpy array
print(img.shape)                          # print image size and color depth

img_gb = img * np.array([0., 1., 1.])     # leave out the red channel
imsave('Lenna_gb.png', img_gb.astype(np.uint8))
```

    (512, 512, 3)

### Matplotlib

Matplotlib is a plotting library. We will use it to show result in this assignment.

```python
# this line prepares IPython for working with matplotlib
%matplotlib inline  

import numpy as np
import matplotlib.pyplot as plt
import math

x = np.arange(-24, 24) / 24. * math.pi
plt.plot(x, np.sin(x))
plt.xlabel('radians')
plt.ylabel('sin value')
plt.title('dummy')

plt.show()
```

![](assignment1_15_0.png)

```python
# images and subplot
import numpy as np
from imageio import imread
import matplotlib.pyplot as plt

img1 = imread('Lenna.png')
img2 = imread('Lenna_gb.png')

plt.subplot(1, 2, 1)  # first plot
plt.imshow(img1)

print(img1.shape)

plt.subplot(1, 2, 2) # second plot
plt.imshow(img2, cmap = 'gray')
plt.show()
```

    (512, 512, 3)

![](assignment1_16_2.png)

This breif overview introduces many basic functions from a few popular libraries, but is far from complete. Check out the documentations for [Numpy](https://docs.scipy.org/doc/numpy/reference/), [Scipy](https://docs.scipy.org/doc/scipy/reference/) and [Matplotlib](https://matplotlib.org/) to find out more.

---

## Problem 1 Function

```python
# This is the most basis practices in Python.
# Please print'Welcome to Oversea Rearch Program for Computer Vision' 
# to complete this problem.

import numpy as np

def fcn():
    print('Welcome to Oversea Rearch Program for Computer Vision')
```

```python
# test the function
fcn()
```

    Welcome to Oversea Rearch Program for Computer Vision

## Problem 2 Matrix Manipulation 

<img src="fig1.png">

```python
import numpy as np

a = np.array([[2, 59, 2, 5], [41, 11, 0, 4], [18, 2, 3, 9], [6, 23, 27, 10], [5, 8, 5, 1]])
b = np.array([[0, 1, 0, 1], [0, 1, 1, 1], [0, 0, 0, 1], [1, 1, 0, 1], [0, 1, 0, 0]])
# print(a)
# print(b)

c = a * b
# print(c)

print(np.sum(c[1] * c[4]))

print(np.min(c))
print(np.where(c == np.min(c)))
print(np.max(c))
print(np.where(c == np.max(c)))

d = np.array([c[i, :]-c[0, :]for i in range(c.shape[0])])
# print(d)

print(np.min(d))
print(np.where(d == np.min(d)))
print(np.max(d))
print(np.where(d == np.max(d)))
```

    88
    0
    (array([0, 0, 1, 1, 2, 2, 2, 3, 4, 4, 4]), array([0, 2, 0, 2, 0, 1, 2, 2, 0, 2, 3]))
    59
    (array([0]), array([1]))
    -59
    (array([2]), array([1]))
    6
    (array([3]), array([0]))


## Problem 3 Keyboard Conundrum

In problem, you will create a function merge(img1, img2, ncols) that horizontally concatenates two perfectly aligned images. (laptop_left.png and laptop_right.png).


(Note: Print out dimension of the image, if the heigh of img1 and img2 are the same, you can directly merge two image together. When you directly merge you may see overlapped part, the overlapped part need to be removed.)


The third argument ncols specifies the number of columns that must be deleted before the images are merged.

[

Note: Image format: 

Colored image has 3 layers (3D matrix), they are RGB layer, represent red, green and blue layer.


Gray Scale image and Binary image has only one layer (2D matrix). 

Gray Scale image has pixel intensity from 0 to 255 levels, total 256 pixel intensity. 

Binary image only has black and white pixel.

]


```python
import numpy as np
from imageio.v2 import imread
import matplotlib.pyplot as plt

def merge(img1, img2, ncols):
    assert img1.shape[0] == img2.shape[0]
    offset = img1.shape[1] - ncols
    img = np.zeros([img1.shape[0], img2.shape[1] + offset, 3], dtype=np.int32)
    for row in range(img.shape[0]):
        for col in range(img1.shape[1]):
            img[row, col, :] = img1[row, col, :]
        for col in range(img2.shape[1]):
            img[row, col + offset, :] = img2[row, col, :]
    return img

# Import image here
img1 = imread('laptop_left.png')
img2 = imread('laptop_right.png')

# function call
# Plot output image
junction = img2[:, 0, :]
for col in range(img1.shape[1]):
    junc = img1[:, img1.shape[1] - col - 1, :]
    if (junc == junction).all():
        ncols = col
        break
img = merge(img1, img2, ncols)
plt.imshow(img)
plt.show()
```

![](assignment1_24_0.png)

## Problem 4 Image Manipulation

In the assignment folder, you will find an image "pepsi.jpg". Import this image and write your implementation for the two function signatures given below to rotate the image by 90, 180, 270 and 360 degrees anticlockwise. You must implement these functions yourself using simple array opperations (ex: numpy.rot90 and scipy.misc.imrotate are NOT allowed as they make the problem trivial). rotate and rotate90 should be out-of-place opperations (should not modify the origional image).

You should write the rest of the code to print these results in a 2X2 grid using the subplot example. The first row, first column should contain an image rotated by 90 degrees; first row, second column an image rotated by 180 degrees, second row, first column an image rotated 270 degrees and second row second column with an image rotated 360 degrees. 
(You may not use OpenCV function for this part.)

```python
import numpy as np
from imageio.v2 import imread
import matplotlib.pyplot as plt

# Rotate image (img) by 90 anticlockwise
def rotate90(img):
    img_res = np.flipud(np.transpose(img.copy(), (1, 0, 2)))
    return img_res

# Roate image (img) by an angle (ang) in anticlockwise direction
# Angle is assumed to be divisible by 90 but may be negative
def rotate(img, ang=0):
    assert ang % 90 == 0
    img_res = img.copy()  # may need to change this line to ensure out-of-place opperation
    for i in range(ang // 90):
        img_res = rotate90(img_res)
    return img_res

# Import image here
img = imread('pepsi.jpg')

# Sample call
img90 = rotate(img, 90)

# Plotting code below
plt.subplot(2, 2, 1)
plt.imshow(rotate(img, 90))
plt.subplot(2, 2, 2)
plt.imshow(rotate(img, 180))
plt.subplot(2, 2, 3)
plt.imshow(rotate(img, 270))
plt.subplot(2, 2, 4)
plt.imshow(rotate(img, 360))
plt.show()
```

![](assignment1_26_0.png)

## Problem 5: Sampling and Quantization

In this problem, we intend to study the effects of sampling and quantization on digital images.
Your job is to write a function with the following specifications (you may use loops if necessary):

(i) The function takes one input: the image file name, 'peppers.png'.

(ii) The input image is assumed to be grayscale.

(iii) Sample the image in spatial domain with a sampling 
rate of 10 (your image should be approximately 10 times smaller along width and height, do not use any numpy functions. In every 10 pixel in horizontal and vertical direction, sample 1 pixel.)

(iv) Do a 5-level uniform quantization of the sampled image so that the bins cover the whole range of grayscale values (0 to 255). You should not use any numpy functions for this.

(v) The function returns one output: the sampled and quantized image.

```python
import numpy as np
from imageio.v2 import imread
import matplotlib.pyplot as plt

img = imread('peppers.png')

# Sampling
sampled = np.array([[img[j * 10, i * 10] for i in range(img.shape[1] // 10)] for j in range(img.shape[0] // 10)])

# Quantization
level = 5
maxv = sampled.max()
minv = sampled.min()
width = (maxv - minv) / level
scale = [minv + width * i for i in range(1, level + 1)]
quantized = np.array([[sum([sampled[j, i] > scale[k] for k in range(level)]) * 255 / level for i in range(sampled.shape[1])] for j in range(sampled.shape[0])])

# Output
plt.imshow(quantized, cmap='gray')
plt.show()
```

![](assignment1_28_0.png)

## Conclusion

Have you accomplished all parts of your assignment? What concepts did you used or learned in this assignment? What difficulties have you encountered? Explain your result for each section. Please wirte one or two short paragraph in the below Markdown window.

The problems are not too hard for me, but they still take me some time to finish because I'm not familiar with the operations related to NumPy and image processing. Plus, since English is not my native language, I might have some trouble comprehending the article and problem especially when I encounter new terms. However, those are minor problems I can overcome during the project.

---
**Submission Instructions**
Remember to submit you pdf version of this notebook to Gradescope. You can find the export option at File $\rightarrow$ Download as $\rightarrow$ PDF via LaTeX
