# Introduction  
In wireless communication (like your cell phone), bits are transmitted by encoding them in periodic analog waveforms called as radio signals. This process of mapping bits into analog signals is called modulation (see this link for more detail). Several modulation schemes have been developed over the years to help transmit the most amount of digital information possible. A transmitting source uses a pre-decided modulation scheme to communicate with a receiver. However, a receiver is immersed in a sea of signals (noise of all sorts get added up by the time it reaches the receiver). Your goal in this project is to build a classifier that predicts the modulation scheme that was used for encoding an incoming signal, given its samples over a short time interval.  

# Dataset  
The training dataset contains 30k training examples where each training example is a collection of 1024 I/Q samples of a specific modulation scheme recorded through either a simulated or a real noisy channel. The test set is of the same format with 20k training examples. Class labels represent 10 types of modulation schemes namely one frequency modulation scheme FM, four amplitude modulation schemes AM-SSB-SC, AM-DSB-SC, OOK, 4ASK, and five phase modulation schemes BPSK, QPSK, OQPSK, 8PSK, 16PSK. You can refer to further details on this specific dataset in this paper.  

# Model Design
## (1) Method one: ML model with Feature Extraction
 
Fig 1  
### Data augmentation 
I used a simple model to get the prediction of validation dataset and get the confusion matrix. I found that the class 5 and class 6 was harder for model to distinguish. In order to solve the problem, I tried to do data augmentation for the two types. I used several methods, including adding noise, copy data, linear combination. However, the performance get worse with data augmentation.   
 
Fig 2  
### Feature extraction
•	Basic feature: mean, standard, deviation, max, min ……  
•	Sequential data feature: kurtosis, skewness, First order difference……  
•	Modulation feature:  Compactness of Zero Center normalized instantaneous amplitude, different accumulation features (M20, M40, C20, C40) …….  
•	Cyclic spectrum and FFT to enlarge the size of the feature.  
### Feature selection
With the feature extraction step, I got more than 1000 features. Then I used these features as input of Random forest, ranked the features with feature_importances_ of sklearn, and picked up the top 440 features.  
### Train Basic Models
Use the top 440 features to train models. I tried different models, including SVM with different kernels and others. I found the SVM with Gaussian kernel achieved the best scores.  
### Models combination
I trained several basic models and tried to combine them: stack them with a simple NN; veto mechanism. However, the results of the two methods were worse than the best basic model.  

## (2) Method two: CNN
 
Fig 3
### Model Selection -- RNN vs CNN
RNN models (GRU, LSTM) are better used for sequential data, but the parameter number is larger than CNN. Because of ban on GPU, I selected CNN rather than RNN.  
### Structure design -- CNN structure
I designed a CNN. The structure is shown below.
 
Fig 4
### Parameter adjustment
Substitute SGD with Adam; different kernel size helps increase information of different steps; substitute Maxpooling with Average Pooling; add BN layer to reduce overfitting and increase convergence speed; dropout to reduce overfitting; another loss at the middle layer helps quick convergence.   
Proper parameters also helps improve the score, such as learning rate, decay weight, kernel number, kernel size, stride size, and so on.

# Result and Conclusion
## (1) Result  
The highest score of feature extraction is 0.59950 private score, while the highest score of CNN is 0.63239 private score. However, the latter method needs about 7 times time more than the former method. 

## (2) Conclusion  

The CNN just uses the original data as input and it doesn’t need much background knowledge of the problem. Anyone who has some experience with neural network can get a good result.   
CNN could achieve better score than feature extraction for this problem. However, CNN needs more time to train with CPU. In a word, CNN uses more parameters to fit the data, while SVC and feature extraction has smaller amount of parameter but user needs some knowledge and experience of this field.


