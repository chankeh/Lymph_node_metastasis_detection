# Lymph_node_metastasis_detection
Identify metastatic tissue in histopathologic scans of lymph node sections

Datasets obtained from kaggle competition https://www.kaggle.com/c/histopathologic-cancer-detection/data

# Project Aim:
### Create an algorithm to identify metastatic cancer in small image patches taken from larger digital pathology scans
Binary classification of H&E pathology images of lymph node sections into 2 classes:
  * 0: `neg` -- The center 32x32px region of a patch doesn't contain a single tumor cell
  * 1: `pos` -- The center 32x32px region of a patch contains at least one pixel of tumor tissue

#### Short overview of the dataset:
 * The dataset contains `train` folder and `test` folder. There are 220025 images in the `train` folder and each image has corresponding labels (0 or 1) saved in a separate file `train_labels.csv`. The `test` folder contains 57458 images to evaluate prediction performance.

 * The ratio of neg/pos images is around 60%/40% in the `train` folder.
 <img src= 'https://github.com/xiey1/Lymph_node_metastasis_detection/blob/master/images/Data_distribution.png' width=300px>
 
 * All the images have size 96x96px in RGB mode.
 Here is a panel of representative images from each class:
 <img src= 'https://github.com/xiey1/Lymph_node_metastasis_detection/blob/master/images/HE_imges_view.png' width=800px>

# Approach -- Convolutional Neural Network:
1. In order to improve prediction accuracy, avoid overfitting, and improve training efficiency, images in `train` folder first need to be split to a new set of `train`, `val`, and `test` datasets and the class ratio needs to be kept consistent across different datasets. The new directory structure is shown as below:
<img src= 'https://github.com/xiey1/Lymph_node_metastasis_detection/blob/master/images/Image1_directory_structure.png' width=300px>

2. **CNN build from scratch**
Here I first tried to build a 11-layer CNN model from scratch:

**((ConV-ReLU)x3-MaxPool)x3 --flattening-- FC1-ReLU-FC2**

![CNN_scratch](https://github.com/xiey1/Lymph_node_metastasis_detection/blob/master/images/Image2_CNN.png)

The CNN model is trained for 50 epochs, and model with the lowest lost function output based on validation dataset is saved as the optimal model for evaluation and further analysis.

# Result and Evaluation:
The trained CNN model is evaluated using the `test` dataset generated from the original `train` folder.
 * Overall prediction accuracy: 0.974
 * Precision: 0.966
 * Recall: 0.970
 * F1 score: 0.968
 * ROC_AUC: 0.983

Here is the confusion matrix and the ROC curve:

<img src= 'https://github.com/xiey1/Lymph_node_metastasis_detection/blob/master/images/Confusion_matrix.png' width=300px><img src= 'https://github.com/xiey1/Lymph_node_metastasis_detection/blob/master/images/ROC_curve.png' width=300px>

Besides, t-SNE plot generated by both raw pixels from each image and features extracted by the trained CNN model for each image gives a clear picture of how well data points are separated after being propagated through the trained CNN model.

<img src='https://github.com/xiey1/Lymph_node_metastasis_detection/blob/master/images/tSNE_plot.png' width=900px>

The trained CNN model has a Private Score of 0.8799 and Public Score of 0.9472 for prediction of images in `test` folder from Kaggle.

# CNN layer visualization

Although neural networks have proven very successuful in extracting information, identifying objects and performing classification of images, how they perform these challenging tasks remains largely a mystery. Here I drawed output of each stack of convolutional layers (conv1, conv2, conv3) as well as each convolutional layer in the first stack (conv1_1, conv1_2, conv1_3) to have a peek inside the 'black box'.

<img src='https://github.com/xiey1/Lymph_node_metastasis_detection/blob/master/images/CNN_conv_total.png'>

<img src='https://github.com/xiey1/Lymph_node_metastasis_detection/blob/master/images/CNN.png' width=900px>


As we can see from the output of `conv1_1`, `conv1_2`, and `conv1_3` as well as post activation images, the first few convolutional layers can capture basic 'content' features of images such as shape, edge, and boundaries.

Deeper convolutional layers tend to capture higher-order features such as colors and textures. It's visually obvious that output from the deeper layers (eg. `conv3`) contains very 'abstract' info that can hardly be inferred from the original input image. In contrast, features captured by the first stack of convolutional layers (`conv1`: `conv1_1`, `conv1_2`, `conv1_3`) mainly contain information about object and shape arrangement that can easily be inferred from the original image.
