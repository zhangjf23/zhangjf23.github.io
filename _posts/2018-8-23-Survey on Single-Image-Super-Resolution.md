---
layout: post
title: Survey on Single-Image-Super-Resolution
categories: 论文导读
description: 超分辨率
keywords: super resolution 
---

A list of resources for example-based single image super-resolution, inspired by Awesome-deep-vision and Awesome Computer Vision .

Example-based methods
Early learning-based methods
[1] Freeman, William T and Pasztor, Egon C and Carmichael, Owen T, Learning low-level vision, IJCV, 2000. [Paper] (Freeman et al. first presented example-based or learning-based super-resolution framework - learn relationships between low-resolution image patches and its high-resolution counterparts.)

[2] Freeman, William T and Jones, Thouis R and Pasztor, Egon C, Example-based super-resolution, IEEE Computer graphics and Applications, 2002. [Paper]

[3] Chang, Hong and Yeung, Dit-Yan and Xiong, Yimin, Super-resolution through neighbor embedding, CVPR, 2004. [Paper][Code] (The idea that low-resolution patches and corresponding high-resolution patches share similar local geometries highly influences the subsequent coding-based or dictionary-based methods.)

Sparsity-based methods
[1] Yang, Jianchao and Wright, John and Huang, Thomas S and Ma, Yi, Image super-resolution via sparse representation, IEEE trans. image processing 2010. [paper] [Code] (SCSR: Classical sparsity-based SISR method - use sparse coding technique to learn low-resolution and high-resolution dictionaries.)

[2] Zeyde, Roman and Elad, Michael and Protter, Matan, On single image scale-up using sparse-representations, International conference on curves and surfaces, 2010. [Paper] [Code] (Low dimension feature speeds up the algorithm. Many sparsity-based image restoration techniques can be found in Prof. Elad's Website!)

[3] Weisheng Dong, Lei Zhang, Guangming Shi, and Xiaolin Wu, Image Deblurring and Super-resolution by Adaptive Sparse Domain Selection and Adaptive Regularization, TIP, 2011. [Website] (Clustering is a very effective trick and local and nonlocal regularization terms are very powerful! Other good sparsity-based super-resolution methods can be found in Prof. Lei Zhang's and Weisheng Dong's Website!)

[4] Peleg, Tomer and Elad, Michael, A statistical prediction model based on sparse representations for single image super-resolution, TIP, 2014. [Paper] [Code] (Predict the relationships between Low-resolution and high-resolution representation coefficients.)

Super-resolution via self-examplars
[1] Daniel Glasner, Shai Bagon and Michal, Irani, Super-Resolution from a Single Image, ICCV, 2009. [Paper]

[2] Jia-Bin Huang, Abhishek Singh, and Narendra Ahuja, "Single Image Super-Resolution from Transformed Self-Exemplars", CVPR, 2015. [Project].

Locally Linear Regression
[1] Gu, Shuhang and Sang, Nong and Ma, Fan, Fast Image Super Resolution via Local Regression, ICPR, 2012. [Paper] (Kmeans clusetering + ridge regression)

[2] Timofte, Radu and De Smet, Vincent and Van Gool, Luc, Anchored neighborhood regression for fast example-based super-resolution, ICCV, 2013. [Paper] [Website] (ANR method)

[3] Yang, Chih-Yuan and Yang, Ming-Hsuan, Fast direct super-resolution by simple functions, ICCV, 2013. [Paper] [Website]

[4] Timofte, Radu and De Smet, Vincent and Van Gool, Luc, A+: Adjusted anchored neighborhood regression for fast super-resolution, ACCV, 2014. [Paper] [Website] (More data and better performance!)

[5] Schulter, Samuel and Leistner, Christian and Bischof, Horst, Fast and accurate image upscaling with super-resolution forests, CVPR, 2015. [Paper] [Code]

[6] Salvador, Jordi, and Eduardo Pérez-Pellitero, Naive Bayes Super-Resolution Forest, ICCV, 2015. [Paper] [Website] (Very fast!)

[7] E. Pérez-Pellitero and J. Salvador and J. Ruiz-Hidalgo and B. Rosenhahn, PSyCo: Manifold Span Reduction for Super Resolution, CVPR, 2016. [Paper] [Website] (choose a better distance metric!)

[8] Timofte, Radu and Rothe, Rasmus and Van Gool, Luc, Seven Ways to Improve Example-Based Single Image Super Resolution, CVPR, 2016. [Website]

Deep Architectures
[1] Chao Dong, Chen Change Loy, Kaiming He, Xiaoou Tang, Learning a deep convolutional network for image super-resolution, ECCV, 2014. [Website] (first introduce CNN to solve single image super-resolution.)

[2] Chao Dong, Chen Change Loy, Kaiming He, Xiaoou Tang, Image Super-Resolution Using Deep Convolutional Networks, IEEE Transactions on Pattern Analysis and Machine Intelligence (TPAMI), 2016. [Website] (use more training data and achieve better SR performance.) [Keras]

[3] Wang, Zhaowen and Liu, Ding and Yang, Jianchao and Han, Wei and Huang, Thomas, Deep networks for image super-resolution with sparse prior, ICCV, 2015. [Website]

[4] Jimmy SJ. Ren, Li Xu, Qiong Yan, Wenxiu Sun, Shepard Convolutional Neural Networks, NIPS, 2015. [Paper] [Code]

[5] Shi, Wenzhe and Caballero, Jose and Huszar, Ferenc and Totz, Johannes and Aitken, Andrew P. and Bishop, Rob and Rueckert, Daniel and Wang, Zehan, Real-Time Single Image and Video Super-Resolution Using an Efficient Sub-Pixel Convolutional Neural Network, CVPR, 2016. [Paper]

[6] Kim, Jiwon and Kwon Lee, Jung and Mu Lee, Kyoung, Accurate Image Super-Resolution Using Very Deep Convolutional Networks, CVPR, 2016. [Paper] [Code] [website]

[7] Kim, Jiwon and Kwon Lee, Jung and Mu Lee, Kyoung, Deeply-Recursive Convolutional Network for Image Super-Resolution, CVPR, 2016. [Paper] [website]

[8] Chao Dong, Chen Change Loy, Xiaoou Tang, Accelerating the Super-Resolution Convolutional Neural Network, ECCV, 2016. [Paper] [Code]

[9] Justin Johnson, Alexandre Alahi, Fei-Fei Li, Perceptual Losses for Real-Time Style Transfer and Super-Resolution, ECCV, 2016, [Website] (Perceptual Loss)

[10] Christian Ledig, Lucas Theis, Ferenc Huszar, Jose Caballero, Andrew Aitken, Alykhan Tejani, Johannes Totz, Zehan Wang, Wenzhe Shi, Photo-Realistic Single Image Super-Resolution Using a Generative Adversarial Network, arXiv, 2016. [Paper](Perceptual Loss, Great Performance!)

[11] Julien Maira, End-to-End Kernel Learning with Supervised Convolutional Kernel Networks, NIPS, 2016. [Paper]

[12] Xiao-Jiao Mao, Chunhua Shen, Yu-Bin Yang, Image Restoration Using Convolutional Auto-encoders with Symmetric Skip Connections, arXiv, 2016. [Paper]

[13] Joan Bruna, Pablo Sprechmann, Yann LeCun, SUPER-RESOLUTION WITH DEEP CONVOLUTIONAL SUFFICIENT STATISTICS, ICLR, 2016. [Paper] (Perceptual Loss)

[14] Mehdi S. M. Sajjadi, Bernhard Schölkopf, Michael Hirsch, EnhanceNet: Single Image Super-Resolution through Automated Texture Synthesis, ICCV, 2017. [Paper] (adversarial training + Texture matching loss to reduce unnatural textures produced by perceptual loss)

[15] Casper Kaae Sønderby, Jose Caballero, Lucas Theis, Wenzhe Shi, Ferenc Huszár, Amortised MAP Inference for Image Super-resolution, ICLR, 2017. [Paper] (calculate the MAP estimate directly using a convolutional neural network)

[16] Wei-Sheng Lai, Jia-Bin Huang, Narendra Ahuja, and Ming-Hsuan Yang, Deep Laplacian Pyramid Networks for Fast and Accurate Super-Resolution, CVPR, 2017. [Website]

[17] K. Zhang, W. Zuo, S. Gu and L. Zhang, "Learning Deep CNN Denoiser Prior for Image Restoration," CVPR, 2017. [Code]

[18] Ying Tai, Jian Yang, and Xiaoming Liu. Image Super-Resolution via Deep Recursive Residual Network, CVPR, 2017. [Code]

[19] E. Agustsson, R. Timofte, L. Van Gool. Anchored Regression Networks applied to Age Estimation and Super Resolution, ICCV, 2017. [paper]

[20] Bee Lim, Sanghyun Son, Heewon Kim, Seungjun Nah, Kyoung Mu Lee. Enhanced Deep Residual Networks for Single Image Super-Resolution. CVPRW, 2017. [paper](state-of-the-art) [Code]

[21] Ying Tai, Jian Yang, Xiaoming Liu and Chunyan Xu. MemNet: A Persistent Memory Network for Image Restoration, ICCV, 2017. [code]

[22] Radu Timofte et al. NTIRE 2017 Challenge on Single Image Super-Resolution: Methods and Results, CVPRW, 2017. [Paper]

[23] Jin Yamanaka, Shigesumi Kuwashima and Takio Kurita: Fast and Accurate Image Super Resolution by Deep CNN with Skip Connection and Network in Network, ICONIPS, 2017. [Paper][Code]

[24] Tong Tong, Gen Li, Xiejie Liu, Qinquan Gao. Image Super-Resolution Using Dense Skip Connections. ICCV, 2017. [Paper]

[25] Yulun Zhang, Yapeng Tian, Yu Kong, Bineng Zhong, Yun Fu. Residual Dense Network for Image Super-Resolution. CVPR 2018. [Paper] [code]

[26] Muhammad Haris, Greg Shakhnarovich, and Norimichi Ukita. Deep Back-Projection Networks For Super-Resolution. CVPR 2018. [Paper] [code-caffe] [code-pytorch]

[27] Kai Zhang, Wangmeng Zuo, and Lei Zhang. Learning a Single Convolutional Super-Resolution Network for Multiple Degradations. CVPR 2018. [Paper] [code]

[28] Adrian Bulat and Georgios Tzimiropoulos. Super-FAN: Integrated facial landmark localization and super-resolution of real-world low resolution faces in arbitrary poses with GANs. CVPR 2018. [Paper]

[29] Bjoern Haefner, Yvain Queau, Thomas Möllenhoff, and Daniel Cremers. Fight ill-posedness with ill-posedness: Single-shot variational depth super-resolution from shading. CVPR 2018.

[30] Xintao Wang, Ke Yu, Chao Dong, and Chen-Change Loy. Recovering Realistic Texture in Image Super-resolution by Spatial Feature Modulation. CVPR 2018. [Paper]

[31] Zheng Hui, Xiumei Wang, and Xinbo Gao. Fast and Accurate Single Image Super-Resolution via Information Distillation Network. CVPR 2018. [Paper]

[32] Xin Yu, Basura Fernando, Richard Hartley, and Fatih Porikli. Super-Resolving Very Low-Resolution Face Images with Supplementary Attributes. CVPR 2018. [Paper]

[33] Wei Han, Shiyu Chang, Ding Liu, Michael Witbrock, and Thomas Huang. Image Super-resolution via Dual-state Recurrent Neural Networks. CVPR 2018. [Paper]

[34] Yu Chen, Ying Tai, Xiaoming Liu, Chunhua Shen, and Jian Yang. FSRNet: End-to-End Learning Face Super-Resolution with Facial Priors. CVPR 2018. [Paper]

[35] Ying Qu, Hairong Qi, and Chiman Kwan. Unsupervised Sparse Dirichlet-Net for Hyperspectral Image Super-Resolution. CVPR 2018.

[36] Assaf Shocher, Nadav Cohen, and Michal Irani. “Zero-Shot” Super-Resolution using Deep Internal Learning. CVPR 2018. [Paper]

[37] Younghyun Jo, Seoung Wug Oh, JaeYeon Kang, and Seon Joo Kim. Deep Video Super-Resolution Network Using Dynamic Upsampling Filters Without Explicit Motion Compensation. CVPR 2018.

[38] Weimin Tan, Bo Yan, and Bahetiyaer Bare. Feature Super-Resolution: Make Machine See More Clearly. CVPR 2018.

[39] Mehdi S. M. Sajjadi, Raviteja Vemulapalli, and Matthew Brown. Frame-Recurrent Video Super-Resolution. CVPR 2018. [Paper]

[40] Yifan Wang, Federico Perazzi, Brian McWilliams, Alexander Sorkine-Hornung, Olga Sorkine-Hornung, Christopher Schroers. A Fully Progressive Approach to Single-Image Super-Resolution. arXiv, 2018. [Paper]

[41] Roey Mechrez, Itamar Talmi, Firas Shama, Lihi Zelnik-Manor. Learning to Maintain Natural Image Statistics. arXiv, 2018. [Paper] [Project] [ECCV SR Challenge].

[42] Yulun Zhang, Kunpeng Li, Kai Li, Lichen Wang, Bineng Zhong, Yun Fu. Image Super-Resolution Using Very Deep Residual Channel Attention Networks. ECCV 2018. [Paper] [code]

[43] Wenming Yang, Xuechen Zhang, Yapeng Tian, Wei Wang, Jing-Hao Xue. Deep Learning for Single Image Super-Resolution: A Brief Review. arxiv, 2018. [Paper] (a survey paper)