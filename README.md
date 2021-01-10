# Naive-Bayes-Classifier
We have the trainset and testset for digit0 and digit1 respectively. Both trainset and testset are sub-dataset from the MNIST dataset. The MNIST dataset contains 70,000 images of handwritten digits, divided into 60,000 training images and 10,000 testing images. We use only a part of images for digit “0” and digit “1” in this question. 
Therefore, we have the following statistics for the given dataset:
Number of samples in the training set:  "0": 5000 ;"1": 5000.
Number of samples in the testing set: "0": 980;   "1": 1135 
We assume that the prior probabilities are the same (P(Y=0) = P(Y=1) =0.5), although we may have noticed that these two digits have different numbers of samples in testing sets.
train0 is the trainset for digit0; train1 is the trainset for digit1; test0 is the testset for digit0; and test1 is the testset for digit1. They are all Numpy Arrays. We can also convert them into python arrays.

    
