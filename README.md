# ECGI_GAN
Applying GAN to mapping torso signal into heart-surface signal.

## Introduction
This project is based on the work done in image-to-image GAN, instead, data used in this work are ECGI signals, which basically are one dimensional data. So we use 1D convolution in order to capture features as many as possible. We want to use deep learning to map two kinds of signals so that equation-solving kind of stuff is not necessary to be deployed and this sort of mapping is faster and hopefully more accurate.

## Data
Briefly, the two real data we use in this work are both related to electrocardiographic imaging. One called torso signal is formed by potentials on body surface, the other one called heart-surface signal is formed by potentials on heart surface. 

## Networks
Two main networks form this framework named **GAN**, one is called **Generator** network and the other one is called **Discriminator** network (or **Critic** network). These two networks try to compete with each other in order to achieve a status where both networks tend to be completely converged.

### Generator
* Input: real torso signal
* Output: simulated(fake) signal in heart surface
* kernal_len = 25
* stride = 4
* Structure: C1024-C512-C256-C128-C64-C32-C64-C128-C256-C512-C1024

### Critic
* Input: cat(simulated signal in heart surface, real signal in heart surface)
* Output: probability [0,1]
* kernal_len = 25
* stride = 4
* Structure: C1-C4-C8-C16-C32-C64-C128
