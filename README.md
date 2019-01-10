# ECGI_GAN
Applying GAN to mapping torso signal into heart-surface signal.

## Introduction
This project is based on the work done in image-to-image GAN, instead, data used in this work are ECGI signals, which basically are one dimensional data. So we use 1D convolution in order to capture features as many as possible. We want to use deep learning to map two kinds of signals so that equation-solving kind of stuff is not necessary to be deployed and this sort of mapping is faster and hopefully more accurate.

## Data
Briefly, the two real data we use in this work are both related to electrocardiographic imaging. One called torso signal is formed by potentials on body surface, the other one called heart-surface signal is formed by potentials on heart surface. 
<br><br>
As the real torso signal is acquired only by 252 poles and heart-surface signal has dimension of 2022, to make the discriminator       fairly judge these pairs of signals, we interpolate all the torso signals into the same dimension of heart-surface signals.
<br><br>
There is one conumdrum regarding to data. Since potentials have no fixed range for one series of signal, for example, in Signal.1, the maximum value could be 6000, for minimum -4000, but in Signal.2, the maximum value could be 2000, for minimum -1000 and this situation can always change due to artifacts during measurements in real life. This makes it hard to project a same group of data into one normalized space where normalized data could also be backprojected into raw ones without information loss so that neural networks are less likely to get converged given more data.
<br><br>
In this work, we simply deal with this probelm by normalizing every data into [-1,1] regardless of backprojection, just to assure that the following DL work is feasible and can get a relatively positive performance regarding to the original goal.

## Networks
Two main networks form this framework named **GAN**, one is called **Generator** network and the other one is called **Discriminator** network (or **Critic** network). These two networks try to compete with each other in order to achieve a status where both networks tend to be completely converged.

### Generator
* Input: real torso signal
* Output: simulated(fake) signal in heart surface
* kernal_len = 25
* stride = 4
* Structure: C1024-C512-C256-C128-C64-C32-C64-C128-C256-C512-C1024 (not an auto-encoder so this part can also be improved)

### Critic
* Input: cat(simulated signal in heart surface, real signal in heart surface)
* Output: probability [0,1]
* kernal_len = 25
* stride = 4
* Structure: C1-C4-C8-C16-C32-C64-C128

## Discussion
### Improvement space for structures of NNs
In the first place, we have to mention that what we use as generator is not a traditional auto-encoder, instead, it follows with a decoder and an encoder after the decoder. An auto-encoder is usually used in image impainting which means the expected output is explicitly similiar to the input. However, in this case, torso signal and heart-surface signal are different from each other both in size(if interpolation isn't applied), range and apparently can not be recognized as two corresponding data. So this leads to the doubt about the feasibility of auto-encoder in this specific situation.
<br><br>
The other way can be to redesign the structure of NNs instead of auto-encoder. For example, encoder doesn't need to be the exact backward structure like the structure of decoder. A much larger encoder or a much larger decoder could change the performance of generator. What's more, it is common to change the hyperparameters like the stride, length of kernals, numbers of channels in order to improve the performance.

### Improvement space for data orgnization
The data used in this work can be treated as 1D signal only with y value information, namely, the potential. As for x value, it shows which electrode detects the potential. Since the group of electrodes are implemented one-by-one around the body surface and fully covers the heart, after squeezing them into one dimension, the information of their locations could be messed up which makes the y value disorder and then make it hard to train. One effective way is to reorgnize the data format. For example, to keep the location information of all the electrodes, we should make the data 2D instead of 1D. Actually, the data of heart structure can form a 3D mesh with a heart shape, as for 3D mesh, it only has useful information on the heart surface which can be regarded as 2D after mapping it into 2D surface map. This surface map consists the whole spatial information of all the electrodes. Here comes another trade-off. If we want to add potential information in that surface map, there are two ways to choose, one is to use 3D data(location(x,y), potentials(p)) and the other is to apply heat map using color to stand for the potential values. The former requires a 3D convolutional NN(CNN) for training process, which needs more computational power and makes the NN hard to converge, what's more, basically keeps all the spatial and potential informations. The latter only requires a 2D CNN, makes it easy to converge but it will definitely lose some of the information given different settings of heat map.


