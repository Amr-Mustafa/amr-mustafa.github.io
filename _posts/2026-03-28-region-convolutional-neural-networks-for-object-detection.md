It is always helpful to get back to basics and review the key building blocks that shaped deep learning and computer vision as we all know and enjoy today. In this post, I review and implement key components of the foundational object detection model, namely Regions with CNN features, or as more commonly known, R-CNN. Developed by Girshick et al. in 2014 at the university of Berkeley, R-CNN took the computer vision community by storm. It marked the rise of CNN-based models for object detection and motivated the development of other more efficient models, in specific Fast R-CNN and Faster R-CNN.

## Architecture Overview
The architecture of R-CNN is actually pretty similar to more classical pre-deep-learning object detectors. It consists of three main parts:
1. a region proposal module,
2. a feature extraction module,
3. and a classifier module.
The only deep-learning-enabled module is that of feature extraction. Older detectors used other methods for feature extraction such as gradient-based or other hand-engineered features. Convolutional neural networks have proven to learn useful dataset-specific filters which simplified the computer vision pipeline engineering significantly.

