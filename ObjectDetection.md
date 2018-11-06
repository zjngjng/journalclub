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
