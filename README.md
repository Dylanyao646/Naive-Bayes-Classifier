# Naive-Bayes-Classifier
import numpy
import scipy.io
import math
import idx2numpy
import numpy as np
import matplotlib.pyplot as plt

def extarctdata(imgs,labels,n,targetlabel):
    # create empty vector
    extracted_data=np.empty([n,imgs.shape[1],imgs.shape[2]])
        
    # start extracting
    count=0
    for i in range(imgs.shape[0]):
        if labels[i]==targetlabel:
            extracted_data[count,:,:]=imgs[i]
            count+=1
        # enough sample is collected
        if count==n:
            break
            
    # check if we got enough data
    if count < n:
        print("Warning! (count < n)")
        print("Only", count, "data available for label", "\"", targetlabel, "\"." )
        # truncate extracted_data
        extracted_data=extracted_data[:count,:,:]
   
    return extracted_data

def loaddata():
    # Load train data 
    train_imgs = idx2numpy.convert_from_file('data/train-images.idx3-ubyte')
    train_labels =  idx2numpy.convert_from_file('data/train-labels.idx1-ubyte')
    
    ntrain0 = 5000
    ntrain1 = 5000 
    
    # extract train data for digit 0 and digit 1 (n samples each)
    train0=extarctdata(train_imgs, train_labels, ntrain0, 0)
    train1=extarctdata(train_imgs, train_labels, ntrain1, 1)
            
    
    # Load test data
    test_imgs = idx2numpy.convert_from_file('data/t10k-images.idx3-ubyte')
    test_labels =  idx2numpy.convert_from_file('data/t10k-labels.idx1-ubyte')
    
    ntest0 = 980
    ntest1 = 1135
    
    # extrat test data for digit 0 and digit 1 (n samples each)
    test0=extarctdata(test_imgs, test_labels, ntest0, 0)
    test1=extarctdata(test_imgs, test_labels, ntest1, 1)
    
    return [train0,train1,test0,test1]

def predict(test_features, Mean_of_feature1_for_digit0, Variance_of_feature1_for_digit0, 
                      Mean_of_feature2_for_digit0, Variance_of_feature2_for_digit0,
                      Mean_of_feature1_for_digit1, Variance_of_feature1_for_digit1, 
                      Mean_of_feature2_for_digit1, Variance_of_feature2_for_digit1):
    
    #computation of P(x1|Y=0)
    exponent_x1_Y0 = np.exp(np.divide(np.power(test_features[:,0] - Mean_of_feature1_for_digit0, 2),
                                      (-2* Variance_of_feature1_for_digit0)))
    p_x1_Y0 = np.multiply(np.divide(1, np.sqrt(2 * math.pi * Variance_of_feature1_for_digit0 )), exponent_x1_Y0)
    #computation of P(x2|Y=0)
    exponent_x2_Y0 = np.exp(np.divide(np.power((test_features[:,1] - Mean_of_feature2_for_digit0 ), 2),
                                      (-2 * Variance_of_feature2_for_digit0)))
    p_x2_Y0 = np.multiply(np.divide(1, np.sqrt(2 * math.pi * Variance_of_feature2_for_digit0)), exponent_x2_Y0)
    #computation of P(x|Y=0)
    p_x_Y0 = np.multiply(p_x1_Y0, p_x2_Y0)
    
    
    #computation of P(x1|Y=1)
    exponent_x1_Y1 = np.exp(np.divide(np.power(test_features[:,0] - Mean_of_feature1_for_digit1, 2),
                                      (-2* Variance_of_feature1_for_digit1)))
    p_x1_Y1 = np.multiply(np.divide(1, np.sqrt(2 * math.pi * Variance_of_feature1_for_digit1 )), exponent_x1_Y1)
    #computation of P(x2|Y=1)
    exponent_x2_Y1 = np.exp(np.divide(np.power((test_features[:,1] - Mean_of_feature2_for_digit1 ), 2),
                                      (-2 * Variance_of_feature2_for_digit1)))
    p_x2_Y1 = np.multiply(np.divide(1, np.sqrt(2 * math.pi * Variance_of_feature2_for_digit1)), exponent_x2_Y1)
    #computation of P(x|Y=1)
    p_x_Y1 = np.multiply(p_x1_Y1, p_x2_Y1)
   
    Y_predicted=np.empty((test_features.shape[0],1))
    for i in range(test_features.shape[0]):
        #compare P(x|Y=1) and P(x|Y=0)
        if  p_x_Y1[i]> p_x_Y0[i]:
            Y_predicted[i]=1
        else:
            Y_predicted[i]=0
    return Y_predicted



def main():
    # import data
    [train0,train1,test0,test1]=loaddata()
    print('Your trainset and testset are imported successfully!')
    # print the shape of imported data
    print("train0.shape:",train0.shape)
    print("train1.shape:",train1.shape)
    print("test0.shape:",test0.shape)
    print("test1.shape:",test1.shape)
    # show a sample image
    #plt.imshow(train1[409], cmap='gray', vmin=0, vmax=255)
    
    
    # Reshaping train0 from (5000,28,28) to (5000,784)
    train0=train0.reshape((train0.shape[0],train0.shape[1]*train0.shape[2]))
    # Reshaping train1 from (5000,28,28) to (5000,784)
    train1=train1.reshape((train1.shape[0],train1.shape[1]*train1.shape[2]))
    # Reshaping test0 from (980,28,28) to (980,784)
    test0=test0.reshape((test0.shape[0],test0.shape[1]*test0.shape[2]))
    # Reshaping test1 from (1135,28,28) to (1135,784)
    test1=test1.reshape((test1.shape[0],test1.shape[1]*test1.shape[2]))
    # print the shape of re-shaped data
    #print("train0",train0.shape)
    #print("train1",train1.shape)
    #print("test0",test0.shape)
    #print("test1",test1.shape)
    
    
    # get the features values
    train0_features=np.transpose(np.array([np.mean(train0,axis=1),np.std(train0,axis=1)]))
    train1_features=np.transpose(np.array([np.mean(train1,axis=1),np.std(train1,axis=1)]))
    test0_features=np.transpose(np.array([np.mean(test0,axis=1),np.std(test0,axis=1)]))
    test1_features=np.transpose(np.array([np.mean(test1,axis=1),np.std(test1,axis=1)]))
        
    # print shape of (train0_features,train1_features,test0_features,test1_features)
    #print("train0_features",train0_features.shape)
    #print("train1_features",train1_features.shape)
    #print("test0_features",test0_features.shape)
    #print("test1_features",test1_features.shape)
    
    
    # compute 8 training parameters
    Mean_of_feature1_for_digit0=np.mean(train0_features[:,0])
    Variance_of_feature1_for_digit0=np.var(train0_features[:,0])
    Mean_of_feature2_for_digit0=np.mean(train0_features[:,1])
    Variance_of_feature2_for_digit0=np.var(train0_features[:,1])
    Mean_of_feature1_for_digit1=np.mean(train1_features[:,0])
    Variance_of_feature1_for_digit1=np.var(train1_features[:,0])
    Mean_of_feature2_for_digit1=np.mean(train1_features[:,1])
    Variance_of_feature2_for_digit1=np.var(train1_features[:,1])
    
    # print stat results
    print("Statistical results:")
    print(Mean_of_feature1_for_digit0,Variance_of_feature1_for_digit0,Mean_of_feature2_for_digit0,
        Variance_of_feature2_for_digit0, Mean_of_feature1_for_digit1,Variance_of_feature1_for_digit1,
        Mean_of_feature2_for_digit1,Variance_of_feature2_for_digit1 )
    
           
    # predict
    Y_predict_0=predict(test0_features, Mean_of_feature1_for_digit0, Variance_of_feature1_for_digit0, 
                      Mean_of_feature2_for_digit0, Variance_of_feature2_for_digit0,
                      Mean_of_feature1_for_digit1, Variance_of_feature1_for_digit1, 
                      Mean_of_feature2_for_digit1, Variance_of_feature2_for_digit1)
    Y_predict_1=predict(test1_features, Mean_of_feature1_for_digit0, Variance_of_feature1_for_digit0, 
                      Mean_of_feature2_for_digit0, Variance_of_feature2_for_digit0,
                      Mean_of_feature1_for_digit1, Variance_of_feature1_for_digit1, 
                      Mean_of_feature2_for_digit1, Variance_of_feature2_for_digit1)
    
    #print("Y_predict_0.shape",Y_predict_0.shape)
    #print("Y_predict_1.shape",Y_predict_1.shape)
    
    # check accuracy
    correct0=np.count_nonzero(Y_predict_0==0)
    accur0=correct0/Y_predict_0.shape[0]

    correct1=np.count_nonzero(Y_predict_1==1)
    accur1=correct1/Y_predict_1.shape[0]
    
    print("Accurcy:")
    print("Label \"0\": ", correct0, " (out of ", Y_predict_0.shape[0], ") correct(s) (", correct0/Y_predict_0.shape[0], ")",sep='')
    print("Label \"1\": ", correct1, " (out of ", Y_predict_1.shape[0], ") correct(s) (", accur1, ")",sep='')   
    pass

    #correct00=
    #print(correct00)

if __name__ == '__main__':
    main()
    
