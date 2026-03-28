It is always helpful to get back to basics and review the key building blocks that shaped deep learning and computer vision as we all know and enjoy today. In this post, I review and implement key components of the foundational object detection model, namely Regions with CNN features, or as more commonly known, R-CNN. Developed by Girshick et al. in 2014 at the university of Berkeley, R-CNN took the computer vision community by storm. It marked the rise of CNN-based models for object detection and motivated the development of other more efficient models, in specific Fast R-CNN and Faster R-CNN.

## Architecture Overview
The architecture of R-CNN is actually pretty similar to more classical pre-deep-learning object detectors. It consists of three main parts:
1. a region proposal module,
2. a feature extraction module,
3. and a classifier module.

The only deep-learning-enabled module is that of feature extraction. Older detectors used other methods for feature extraction such as gradient-based features or other hand-engineered features. R-CNN, on the other hand, used a convolutional network as the feature extractor. This is, of course, motivated by the fact that such networks had proven to dynamically learn useful dataset-specific filters.

The other two modules are classical non-deep-learning-based algorithms. More specifically, the region proposal module was based on the selective search algorithm; a method used to segment the input image by solving a graph minimisation problem. We can think of this module as providing an initial guess of where the individual objects in the image are. Those rough detections, or region proposals as more commonly known, would be later refined by the remaining modules.

As for the classifier module, it was based on support vector machines (SVMs). In particular, the module has `N` SVM classifiers, one for each class. Given the convolutional features for a given region proposal, the SVMs would independently score this region. The module then would assign the region to the class with the maximum score.

