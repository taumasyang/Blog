---
title: 机器学习与计算机视觉：作业 6
date: 2023-04-07 13:45:00
categories: 语义分割
tags:
- Python
- 图像处理
---

# Machine Learning and Computer Vision
## Assigment 6

This assignment contains Tensorflow programming exercises.

## Problem 1: Install Tensorflow

Follow the directions on https://www.tensorflow.org/install/ to install Tensorflow on your computer.

Note: You will not need GPU support for this assignment so don't worry if you don't have one. Furthermore, installing with GPU support is often more difficult to configure so it is suggested that you install the CPU only version. However, if you have a GPU and would like to install GPU support feel free to do so at your own risk :)

Note: On windows, Tensorflow is only supported in python3, so you will need to install python3 for this assignment.

Run the following cell to verify your instalation.

```python
import tensorflow.compat.v1 as tf
tf.compat.v1.disable_v2_behavior()
hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```

	WARNING:tensorflow:From /opt/homebrew/Caskroom/miniforge/base/envs/tf/lib/python3.10/site-packages/tensorflow/python/compat/v2_compat.py:107: disable_resource_variables (from tensorflow.python.ops.variable_scope) is deprecated and will be removed in a future version.
	Instructions for updating:
	non-resource variables are not supported in the long term
	Metal device set to: Apple M1
	
	systemMemory: 16.00 GB
	maxCacheSize: 5.33 GB
	
	b'Hello, TensorFlow!'


	2023-04-07 13:31:34.575341: W tensorflow/tsl/platform/profile_utils/cpu_utils.cc:128] Failed to get CPU frequency: 0 Hz

## Problem 2: Downloading CIFAR10

Download the CIFAR10 dataset (http://www.cs.toronto.edu/~kriz/cifar.html). You will need the python version: http://www.cs.toronto.edu/~kriz/cifar-10-python.tar.gz 

Extract the data to `./data`
Once extracted run the following cell to view a few example images.

```python
import numpy as np

# unpickles raw data files
def unpickle(file):
	import pickle
	import sys
	with open(file, 'rb') as fo:
		if sys.version_info[0] < 3: dict = pickle.load(fo)
		else: dict = pickle.load(fo, encoding='bytes')
	return dict

# loads data from a single file
def getBatch(file):
	dict = unpickle(file)
	data = dict[b'data'].reshape(-1, 3, 32, 32).transpose(0, 2, 3, 1)
	labels = np.asarray(dict[b'labels'], dtype=np.int64)
	return data, labels

# loads all training and testing data
def getData(path='./data'):
	classes = [s.decode('UTF-8') for s in unpickle(path + '/batches.meta')[b'label_names']]

	trainData, trainLabels = [], []
	for i in range(5):
		data, labels = getBatch(path + '/data_batch_%d' % (i + 1))
		trainData.append(data)
		trainLabels.append(labels)
	trainData = np.concatenate(trainData)
	trainLabels = np.concatenate(trainLabels)

	testData, testLabels = getBatch(path + '/test_batch')
	return classes, trainData, trainLabels, testData, testLabels

# training and testing data that will be used in the following problems
classes, trainData, trainLabels, testData, testLabels = getData()

# display some example images
import matplotlib.pyplot as plt
%matplotlib inline

plt.figure(figsize=(14, 6))
for i in range(14):
	plt.subplot(2, 7, i + 1)
	plt.imshow(trainData[i])
	plt.title(classes[trainLabels[i]])
plt.show()

print('train shape: ' + str(trainData.shape) + ', ' + str(trainLabels.shape))
print('test shape : ' + str(testData.shape) + ', ' + str(testLabels.shape))
```

![](hw6_4_0.png)

	train shape: (50000, 32, 32, 3), (50000,)
	test shape : (10000, 32, 32, 3), (10000,)

Below are some helper functions that will be used in the following problems.

```python
# a generator for batches of data
# yields data (batchsize, 3, 32, 32) and labels (batchsize)
# if shuffle, it will load batches in a random order
def DataBatch(data, label, batchsize, shuffle=True):
	n = data.shape[0]
	if shuffle: index = np.random.permutation(n)
	else: index = np.arange(n)
	for i in range(int(np.ceil(n/batchsize))):
		inds = index[i * batchsize: min(n, (i + 1) * batchsize)]
		yield data[inds], label[inds]

# tests the accuracy of a classifier
def test(testData, testLabels, classifier):
	batchsize = 50
	correct = 0.
	for data, label in DataBatch(testData, testLabels, batchsize):
		prediction = classifier(data)
		correct += np.sum(prediction == label)
	return correct / testData.shape[0] * 100

# a sample classifier
# given an input it outputs a random class
class RandomClassifier():
	def __init__(self, classes=10):
		self.classes = classes

	def __call__(self, x):
		return np.random.randint(self.classes, size=x.shape[0])

randomClassifier = RandomClassifier()
print('Random classifier accuracy: %f' % test(testData, testLabels, randomClassifier))
```

	Random classifier accuracy: 10.040000

## Problem 3: Confusion Matirx

Here you will implement a test script that computes the confusion matrix for a classifier.
The matrix should be $n\times n$ where $n$ is the number of classes.
Entry `M[i,j]` should contain the number of times an image of class `i` was classified as class `j`.
`M` should be normalized such that each row sums to `1`.

Hint: see the function `test()` above for reference.

```python
def confusion(testData, testLabels, classifier):
	batchsize = 50
	M = np.zeros((len(classes), len(classes)))
	for data, label in DataBatch(testData, testLabels, batchsize):
		prediction = classifier(data)
		for i in range(len(label)):
			M[label[i]][prediction[i]] += 1
	return M

def VisualizeConfussion(M):
	plt.figure(figsize=(14, 6))
	plt.imshow(M, cmap='gray')
	plt.xticks(np.arange(len(classes)), classes, rotation='vertical')
	plt.yticks(np.arange(len(classes)), classes)
	plt.show()

M = confusion(testData, testLabels, randomClassifier)
VisualizeConfussion(M)
```

![](hw6_8_0.png)

## Problem 4: K-Nearest Neighbors (KNN)

Here you will implement a simple knn classifer. The distance metric is euclidian in pixel space. $k$ refers to the number of neighbors involved in voting on the class.

Hint: you may want to use: `sklearn.neighbors.KNeighborsClassifier`

```python
from sklearn.neighbors import KNeighborsClassifier

class KNNClassifier:
	def __init__(self, k=3):
		# k is the number of neighbors involved in voting
		self.knn = KNeighborsClassifier(n_neighbors=k)

	def train(self, trainData, trainLabels):
		self.knn.fit(trainData.reshape((trainData.shape[0], -1)), trainLabels)

	def __call__(self, x):
		# this method should take a batch of images (batchsize, 32, 32, 3) and return a batch of prediction (batchsize)
		# predictions should be int64 values in the range [0,9] corrisponding to the class that the image belongs to
		return self.knn.predict(x.reshape((x.shape[0], -1)))

# test your classifier with only the first 100 training examples (use this while debugging)
# note you should get around 10-20% accuracy
knnClassiferX = KNNClassifier()
knnClassiferX.train(trainData[:100], trainLabels[:100])
print('KNN classifier accuracy: %f' % test(testData, testLabels, knnClassiferX))
```

	KNN classifier accuracy: 16.600000

```python
# test your classifier with all the training examples (This may take a while)
# note you should get around 30% accuracy
knnClassifer = KNNClassifier()
knnClassifer.train(trainData, trainLabels)
print('KNN classifier accuracy: %f' % test(testData, testLabels, knnClassifer))

# display confusion matrix for your KNN classifier with all the training examples
M = confusion(testData, testLabels, knnClassifer)
VisualizeConfussion(M)
```

	KNN classifier accuracy: 33.030000

![](hw6_11_1.png)

## Problem 5: Principal Component Analysis (PCA) K-Nearest Neighbors (KNN)

Here you will implement a simple knn classifer in PCA space.
You should implement PCA yourself using svd (you may not use `sklearn.decomposition.PCA` or any other package that directly implements PCA transofrmations)

Hint: Don't forget to apply the same normalization at test time.

Note: you should get similar accuracy to above, but it should run faster.

```python
from scipy.spatial import distance
from scipy import stats

class PCAKNNClassifier:
	def __init__(self, components=25, k=3):
		self.components = components
		self.k = k

	def train(self, trainData, trainLabels):
		self.trainData = trainData.reshape((trainData.shape[0], -1))
		self.trainLabels = trainLabels
		self.mean = np.mean(self.trainData, axis=0)
		cov = np.cov(self.trainData.T)
		eig_vals, eig_vecs = np.linalg.eig(cov)
		idx = eig_vals.argsort()[::-1]
		eig_vals = np.real(eig_vals[idx])
		eig_vecs = np.real(eig_vecs[:, idx])
		self.eig_vecs = eig_vecs[:, :self.components]
		self.trainData = np.dot(self.trainData - self.mean, self.eig_vecs)

	def __call__(self, x):
		x = x.reshape((x.shape[0], -1)) - self.mean
		x_pca = np.dot(x, self.eig_vecs)
		dists = distance.cdist(x_pca, self.trainData)
		idx = np.argpartition(dists, self.k)[:, :self.k]
		k_labels = self.trainLabels[idx]
		labels, _ = stats.mode(k_labels, axis=1, keepdims=True)
		return labels.ravel().tolist()

# test your classifier with only the first 100 training examples (use this while debugging)
pcaknnClassiferX = PCAKNNClassifier()
pcaknnClassiferX.train(trainData[:100], trainLabels[:100])
print('PCA-KNN classifier accuracy: %f' % test(testData, testLabels, pcaknnClassiferX))
```

	PCA-KNN classifier accuracy: 16.380000

```python
# test your classifier with all the training examples (This may take a few minutes)
pcaknnClassifer = PCAKNNClassifier()
pcaknnClassifer.train(trainData, trainLabels)
print('PCA-KNN classifier accuracy: %f' % test(testData, testLabels, pcaknnClassifer))

# display the confusion matrix
M = confusion(testData, testLabels, pcaknnClassifer)
VisualizeConfussion(M)
```

	PCA-KNN classifier accuracy: 37.890000

![](hw6_14_1.png)

## Deep learning
Below is some helper code to train your deep networks

Hint: see https://www.tensorflow.org/get_started/mnist/pros or https://www.tensorflow.org/get_started/mnist/beginners for reference

```python
# base class for your Tensorflow networks. It implements the training loop (train) and prediction(__call__)  for you.
# You will need to implement the __init__ function to define the networks structures in the following problems
class TFClassifier():
	def __init__(self):
		pass

	def train(self, trainData, trainLabels, epochs=1, batchsize=50):
		self.prediction = tf.argmax(self.y, 1)
		self.cross_entropy = tf.reduce_mean(tf.nn.sparse_softmax_cross_entropy_with_logits(labels=self.y_, logits=self.y))
		self.train_step = tf.train.AdamOptimizer(1e-4).minimize(self.cross_entropy)
		self.correct_prediction = tf.equal(self.prediction, self.y_)
		self.accuracy = tf.reduce_mean(tf.cast(self.correct_prediction, tf.float32))
		self.sess.run(tf.global_variables_initializer())
		for epoch in range(epochs):
			for _, (data, label) in enumerate(DataBatch(trainData, trainLabels, batchsize, shuffle=True)):
				self.sess.run([self.train_step, self.accuracy], feed_dict={self.x: data, self.y_: label})
			print('testing epoch:%d accuracy: %f' % (epoch+1, test(testData, testLabels, self)))

	def __call__(self, x):
		return self.sess.run(self.prediction, feed_dict={self.x: x})

# helper function to get weight variable
def weight_variable(shape):
	initial = tf.truncated_normal(shape, stddev=0.01)
	return tf.Variable(initial)

# helper function to get bias variable
def bias_variable(shape):
	initial = tf.constant(0.1, shape=shape)
	return tf.Variable(initial)

# example linear classifier
class LinearClassifer(TFClassifier):
	def __init__(self, classes=10):
		self.sess = tf.Session()

		# input batch of images
		self.x = tf.placeholder(tf.float32, shape=[None, 32, 32, 3])
		self.y_ = tf.placeholder(tf.int64, shape=[None])	# input labels

		# model variables
		self.W = weight_variable([32 * 32 * 3, classes])
		self.b = bias_variable([classes])

		# linear operation
		self.y = tf.matmul(tf.reshape(self.x, (-1, 32 * 32 * 3)), self.W) + self.b

# test the example linear classifier (note you should get around 20-30% accuracy)
linearClassifer = LinearClassifer()
linearClassifer.train(trainData, trainLabels, epochs=20)

# display confusion matrix
M = confusion(testData, testLabels, linearClassifer)
VisualizeConfussion(M)
```

	testing epoch:1 accuracy: 26.460000
	testing epoch:2 accuracy: 24.450000
	testing epoch:3 accuracy: 26.660000
	testing epoch:4 accuracy: 23.370000
	testing epoch:5 accuracy: 24.430000
	testing epoch:6 accuracy: 26.540000
	testing epoch:7 accuracy: 25.520000
	testing epoch:8 accuracy: 25.540000
	testing epoch:9 accuracy: 28.160000
	testing epoch:10 accuracy: 29.270000
	testing epoch:11 accuracy: 26.830000
	testing epoch:12 accuracy: 23.750000
	testing epoch:13 accuracy: 25.610000
	testing epoch:14 accuracy: 25.580000
	testing epoch:15 accuracy: 28.590000
	testing epoch:16 accuracy: 26.770000
	testing epoch:17 accuracy: 30.600000
	testing epoch:18 accuracy: 26.050000
	testing epoch:19 accuracy: 23.430000
	testing epoch:20 accuracy: 28.290000

![](hw6_16_1.png)

## Problem 6: Multi Layer Perceptron (MLP)

Here you will implement an MLP. The MLP shoud consist of 3 linear layers (matrix multiplcation and bias offset) that map to the following feature dimensions:

32x32x3 -> hidden

hidden -> hidden

hidden -> classes

The first two linear layers should be followed with a ReLU nonlinearity. The final layer should not have a nonlinearity applied as we desire the raw logits output (see: the documentation for `tf.nn.sparse_softmax_cross_entropy_with_logits` used in the training)

The final output of the computation graph should be stored in `self.y` as that will be used in the training.

Hint: see the example linear classifier

Note: you should get around 50% accuracy

```python
class MLPClassifier(TFClassifier):
	def __init__(self, classes=10, hidden=100):
		self.sess = tf.Session()

		self.x = tf.placeholder(tf.float32, shape=[None, 32, 32, 3])	# input batch of images
		self.y_ = tf.placeholder(tf.int64, shape=[None])				# input labels

		self.hidden_layer1 = tf.layers.dense(tf.reshape(self.x, (-1, 32 * 32 * 3)), hidden, activation=tf.nn.relu)
		self.hidden_layer2 = tf.layers.dense(self.hidden_layer1, hidden, activation=tf.nn.relu)
		self.y = tf.layers.dense(self.hidden_layer2, classes)

# test your MLP classifier (note you should get around 50% accuracy)
mlpClassifer = MLPClassifier()
mlpClassifer.train(trainData, trainLabels, epochs=20)

# display confusion matrix
M = confusion(testData, testLabels, mlpClassifer)
VisualizeConfussion(M)
```

	/var/folders/3n/bxp54ts11rx346f3p1t2wr2r0000gn/T/ipykernel_39141/55173199.py:8: UserWarning: `tf.layers.dense` is deprecated and will be removed in a future version. Please use `tf.keras.layers.Dense` instead.
	  self.hidden_layer1 = tf.layers.dense(tf.reshape(self.x, (-1, 32 * 32 * 3)), hidden, activation=tf.nn.relu)
	/var/folders/3n/bxp54ts11rx346f3p1t2wr2r0000gn/T/ipykernel_39141/55173199.py:9: UserWarning: `tf.layers.dense` is deprecated and will be removed in a future version. Please use `tf.keras.layers.Dense` instead.
	  self.hidden_layer2 = tf.layers.dense(self.hidden_layer1, hidden, activation=tf.nn.relu)
	/var/folders/3n/bxp54ts11rx346f3p1t2wr2r0000gn/T/ipykernel_39141/55173199.py:10: UserWarning: `tf.layers.dense` is deprecated and will be removed in a future version. Please use `tf.keras.layers.Dense` instead.
	  self.y = tf.layers.dense(self.hidden_layer2, classes)

	testing epoch:1 accuracy: 23.210000
	testing epoch:2 accuracy: 23.820000
	testing epoch:3 accuracy: 26.860000
	testing epoch:4 accuracy: 28.390000
	testing epoch:5 accuracy: 30.990000
	testing epoch:6 accuracy: 29.820000
	testing epoch:7 accuracy: 32.660000
	testing epoch:8 accuracy: 32.770000
	testing epoch:9 accuracy: 34.700000
	testing epoch:10 accuracy: 32.690000
	testing epoch:11 accuracy: 34.400000
	testing epoch:12 accuracy: 36.680000
	testing epoch:13 accuracy: 37.770000
	testing epoch:14 accuracy: 38.000000
	testing epoch:15 accuracy: 35.700000
	testing epoch:16 accuracy: 39.830000
	testing epoch:17 accuracy: 39.340000
	testing epoch:18 accuracy: 34.240000
	testing epoch:19 accuracy: 39.530000
	testing epoch:20 accuracy: 37.900000

![](hw6_18_2.png)

## Problem 7: Convolutional Neural Netork (CNN)

Here you will implement a CNN with the following architecture:

ReLU(Conv(kernel_size=4x4 stride=2, output_features=n))

ReLU(Conv(kernel_size=4x4 stride=2, output_features=n*2))

ReLU(Conv(kernel_size=4x4 stride=2, output_features=n*4))

Linear(output_features=classes)

```python
def conv2d(x, W, stride=2):
	return tf.nn.conv2d(x, W, strides=[1, stride, stride, 1], padding='SAME')

class CNNClassifier(TFClassifier):
	def __init__(self, classes=10, n=16):
		self.sess = tf.Session()

		self.x = tf.placeholder(tf.float32, shape=[None, 32, 32, 3])	# input batch of images
		self.y_ = tf.placeholder(tf.int64, shape=[None])				# input labels

		self.W1 = tf.Variable(tf.truncated_normal([4, 4, int(self.x.shape[3]), n], stddev=0.1))
		self.conv_layer1 = tf.nn.relu(conv2d(self.x, self.W1))
		self.W2 = tf.Variable(tf.truncated_normal([4, 4, n, n * 2], stddev=0.1))
		self.conv_layer2 = tf.nn.relu(conv2d(self.conv_layer1, self.W2))
		self.W3 = tf.Variable(tf.truncated_normal([4, 4, n * 2, n * 4], stddev=0.1))
		self.conv_layer3 = tf.nn.relu(conv2d(self.conv_layer2, self.W3))
		self.flatten_layer = tf.layers.flatten(self.conv_layer3)
		self.y = tf.layers.dense(self.flatten_layer, classes)

# test your CNN classifier (note you should get around 65% accuracy)
cnnClassifer = CNNClassifier()
cnnClassifer.train(trainData, trainLabels, epochs=20)

# display confusion matrix
M = confusion(testData, testLabels, cnnClassifer)
VisualizeConfussion(M)
```

	/var/folders/3n/bxp54ts11rx346f3p1t2wr2r0000gn/T/ipykernel_39141/588842117.py:17: UserWarning: `tf.layers.flatten` is deprecated and will be removed in a future version. Please use `tf.keras.layers.Flatten` instead.
	  self.flatten_layer = tf.layers.flatten(self.conv_layer3)
	/var/folders/3n/bxp54ts11rx346f3p1t2wr2r0000gn/T/ipykernel_39141/588842117.py:18: UserWarning: `tf.layers.dense` is deprecated and will be removed in a future version. Please use `tf.keras.layers.Dense` instead.
	  self.y = tf.layers.dense(self.flatten_layer, classes)

	testing epoch:1 accuracy: 20.640000
	testing epoch:2 accuracy: 27.310000
	testing epoch:3 accuracy: 30.550000
	testing epoch:4 accuracy: 32.640000
	testing epoch:5 accuracy: 34.420000
	testing epoch:6 accuracy: 35.430000
	testing epoch:7 accuracy: 37.000000
	testing epoch:8 accuracy: 38.690000
	testing epoch:9 accuracy: 39.160000
	testing epoch:10 accuracy: 39.650000
	testing epoch:11 accuracy: 40.340000
	testing epoch:12 accuracy: 41.420000
	testing epoch:13 accuracy: 41.380000
	testing epoch:14 accuracy: 42.160000
	testing epoch:15 accuracy: 41.910000
	testing epoch:16 accuracy: 42.300000
	testing epoch:17 accuracy: 43.450000
	testing epoch:18 accuracy: 43.360000
	testing epoch:19 accuracy: 43.700000
	testing epoch:20 accuracy: 44.220000

![](hw6_20_2.png)

## Conclusion

Have you accomplished all parts of your assignment? What concepts did you used or learned in this assignment? What difficulties have you encountered? Explain your result for each section. Please wirte one or two short paragraph in the below Markdown window (double click to edit).

## Further reference

To see how state of the art deep networks do on this dataset see: https://github.com/tensorflow/models/tree/master/research/resnet