[Najibi, M., Samangouei, P., Chellappa, R. and Davis, L.S., 2017, October. SSH: Single Stage Headless Face Detector. In *ICCV* (pp. 4885-4894).](http://openaccess.thecvf.com/content_ICCV_2017/papers/Najibi_SSH_Single_Stage_ICCV_2017_paper.pdf)

This paper has three contributions:

- inference is fast by removing the head of its underlying classification network -- a fully connected layers in VGG-16
- very similar to SSD while face detection only has two classes
- matching ground-truth boxes to anchors is very different from SSD. SSD matches anchors to to any ground truth with IOU higher than a threshold while SSH distributes the anchors based on their size to different modules (i.e. different feature maps) and only back-propagate the loss for the anchors which are assigned to faces in the corresponding range. 
- context module in each prediction module. In 2-stage detection, we can incorporate context by enlarging the window around the candidate proposals, while SSH uses filters with larger kernel size (5x5 and 7x7) to enlarge the receptive field, which is implemented using two or three 3x3 convolutional layers. (similar to inception module).
- online hard negative and positive mining. 

[Singh B, Davis LS. An Analysis of 
Scale Invariance in Object Detection–SNIP. InProceedings of the IEEE 
Conference on Computer Vision and Pattern Recognition 2018 (pp. 
3578-3587).](https://www.cs.umd.edu/~bharat/snip.pdf)

When the scale of anchor doesn't match the scale of ground truth, it consists of two situations: one situation is that the anchor is very small while ground truth box is big, the other situation is that anchor is very big while ground truth box is small. For both cases, the IOU between the anchors with the ground truth box will be too low.

*Problems on scale variation and small object instances*: CNNs are pretrained on ImageNet dataset where the objects have large scale and high resolution while the COCO dataset has smaller objects, to make matters worse, the scale of smallest and largest object instances in COCO is 0.24 and 0.472 results in scale variations of almost 20 times.

The following conclusions are obtained through experiments:

1. Training at higher resolution scales up small objects for better classification, but blows up the medium-to-large scale objects which degrades performance. This is because they become too large to be correctly classified.
2. Training scale-specific detectors for small objects while ignoring all medium-to-large objects tends to degrade performance. This is because that we lost the source of variations in appearance and pose that hurt the performance more than it helped by eliminating extreme scale objects.
3. It is important to train with maximal variations in appearance and pose while restricting the scale to  a reasonable range. It seems that CNNs doesn't have the property of scale-invariance. It just memorizes objects of different scales. 

*Scale normalization for image pyramid (SNIP)*: This paper use an image pyramid of three different resolutions, and each image will be associated with a scale range. If a GT box falls outside of the specified range of an image at one resolution, it will be considered as invalid and the anchors with have overlap with invalid GT boxes higher than 0.3 will also be considered as invalid anchor. Training from those invalid anchors will be ignore during back-propagation. During inference, we generate proposals for each resolution independently and don't select detections which fall outside a specified range at each resolution.

[Kong, Tao, et al. "Ron: Reverse connection with objectness prior networks for object detection." CVPR 2017](https://arxiv.org/pdf/1707.01691.pdf)

Three contributions: 

- Multi-scale predictions from feature maps at different leves. Anchors/boxes of different scales are distributed to different feature maps, and each feature map is responsive to objects of particular scales.
- Reverse connection similiar to FPN to fuse high-level features with low-level features.
- Addition of prediciton of objectness prior parallel to the bounding box regression and classification. For backward propagation, the prior mask is binarized to select samples for udating the detection branch. (Faster R-CNN use RPN to filter background samples, objectness prior is used to guide the search of objects)
- Two inception modules was used in the classification branch.

[Cai, Zhaowei, and Nuno Vasconcelos. "Cascade R-CNN: Delving into High Quality Object Detection." CVPR 2018.](http://openaccess.thecvf.com/content_cvpr_2018/papers/Cai_Cascade_R-CNN_Delving_CVPR_2018_paper.pdf)

An object detector trained with IOU threshold=0.5 produces noisy detections, while training with increased IOU has worse performance, this is because higher IOU threshold means less positive training samples and may result in overfitting, another reason is the mismatch of optimal IOU threshold between training and inference stages. In the training stage, if we use high quality proposals, the trained detector will only be optimal for high quality proposals but not for low-quality proposals during inference stage.

In order to reduce the false positives (whose scores are a little higher than old IOU threshold=0.5), this paper propose a cascade CNN that uses the output of a detector to train the subsequent detector. This is motivate by the obeservation that the output IOU (prediction with gorund truth) is always better than input IOU (proposal with ground truth).

Detection其实并不是一个很合适的分类问题，没有一个明确的离散的正负样本的定义，而是通过IoU来连续定义的。但是IoU这个指标很难通过gradient descent来优化，虽然之前也有一些IoU loss的工作，但是效果并不理想。Cascade RCNN便是一个在这个方向上很好的尝试。

