[Finding Tiny Faces](https://www.cs.cmu.edu/~peiyunh/tiny/)
11/17/2018 11/18/2018
It explores three aspects of the problem in finding small faces:
1. scale invariance 
2. image resolution
3. contextual reasoning
4. https://zhuanlan.zhihu.com/p/27450260

##### Multi-task modeling of scales:

On one hand, we want a small template that can detect small faces, on the other hand, we want a large template that can exploit detailed features to increase accuracy. Training a large collection of scale-specific detectors may suffer from lacking of training data for individual scales and inefficiency from running a large number of detectors at test time.

Solution: make use of features defined over multiple layer of a single deep feature hierarchy.

##### How to generalize pre-trained networks?

1. How best to extract scale-invariant features from pretrained networks that are tuned for objects of a characteristic size
2. To extend features fined tuned from these networks to objects of novel size, we resize images at test time by interpolation and decimation.

##### How best to encode context?

We demonstrates that CNN features extracted from multiple layers are effective foveal descriptors that capture both high-resolution detail and coarse low-resolution cues across large receptive field.

##### Exploring context and resolution

1. Context: 

   - smaller receptive fields do better for small faces becuase entire face is visible; 
   - adding context almost alway helps for small faces, but beyond 300x300 will hurt; 
   - high-resoution components of foveal descriptors are crucial for accuarate detection of small faces

2. Resolution:

   - building templates at original resolution is not optimal: find small 25x20 faces, building templates at 2x resolution improves overal accuracy by 6.3%; find large 250x200 faces, building templates at 0.5x resolution improves the overal accuracy.
   - 80% of the training samples in ImageNet contain objects ofa medium size between 40~140 px

3. Scale-specific detection:

   1. t(h,w,sigma) represents a template, a template is tuned to detect objects of size (h/simga, w/sigma) at resolution sigma.

   2. Different strategies: to find large objects (greater than 140px) use 2X smaller canonical resolution; to find small objects (less than 40px) use 2X larger canonical template resolution; otherwise use the same resolution.

      ![preview](https://pic1.zhimg.com/v2-30b9341d307d437be10b9ed4458800c1_r.jpg)


