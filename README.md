# Brain Tumor Image Classification iOS application


## Overview
A brain tumor is considered to be one of the aggressive diseases among children and adults. Brain tumors account for 85 to 90% of all primary Central Nervous System(CNS) tumors. Every year, around 11,700 people are diagnosed with a brain tumor. The manual examination of neural activity can be error-prone due to the level of complexities involved in brain tumors and their properties. To improve the life expectancy of the patients, proper treatment, planning, and accurate diagnostics should be implemented. Automated interpretation and classification of functional MRI (fMRI) data is an emerging research field that enables the characterization of underlying cognitive processes with minimal human intervention. Therefore, with the help of deep learning algorithms, we aim to develop an application performing the detection and classification of brain tumors to help doctors around the world, especially in developing countries where knowledge about tumors, lack of neurosurgeons present a challenge. An edge-based computer vision solution will facilitate for doctors around the world preliminary efficient detection and classification of a brain tumor.

## Background
A brain tumor is an abnormal mass of tissue in which cells grow and multiply uncontrollably, seemingly unchecked by the mechanisms that control normal cells. More than 150 different brain tumors have been documented, the two main groups of brain tumors are termed primary and metastatic. Primary brain tumors include tumors that originate from the tissues of the brain, they are categorized as benign, malignant, and pituitary. Metastatic brain tumors include tumors that arise elsewhere in the body and migrate to the brain, they are considered cancer and are malignant. The complexity and numerous abnormalities in brain tumor sizes and location make it difficult to completely understand the nature of the tumor. fMRI data is threedimensional, containing tens of thousands of voxels. Due to the cost and time needed to capture fMRI along with other imaging modalities and clinical data, the number of available subjects is small. Usually, datasets have only a few tens of subjects. Many datasets also show high subject variability, depending upon the nature of the neuropsychological process and task. Generally, the fMRI signal is noisy. The classification has been used on brain fMRI for two goals: the prediction of cognitive states and group classification. In the prediction of cognitive states, the goal is to infer the experimental condition that a given subject was undergoing at a given time. In group classification, the goal is to infer the group membership of a given subject. In the problem, we aim to discern whether fMRI depicts a brain tumor or not.

## Data Acquisition
The brain tumor dataset is retrieved from [Kaggle, Brain MRI Images for Brain Tumor Detection](https://www.kaggle.com/jjprotube/brain-mri-images-for-brain-tumor-detection). There are 98 images without any sign of a tumor, and 155 files with various tumors.

## Data Preparation
Images took on different file formatting (e.g..jpg, .png, .jpeg) and required consistent input file extension (e.g. jpeg). The B&W images of 224 x 224 pixels represented in two classes: the images containing tumor labeled with *yes*, and the images that do not - are marked with *no*. The dataset was split into three categories: training (62%), validation (8%) and testing (30%) images.


No Tumor Brain          |  Brain Tumor Anatomy
:-------------------------:|:-------------------------:
<img src="https://storage.googleapis.com/kagglesdsdata/datasets/740566/1633128/no/no1005.jpg?X-Goog-Algorithm=GOOG4-RSA-SHA256&X-Goog-Credential=databundle-worker-v2%40kaggle-161607.iam.gserviceaccount.com%2F20201128%2Fauto%2Fstorage%2Fgoog4_request&X-Goog-Date=20201128T163110Z&X-Goog-Expires=172799&X-Goog-SignedHeaders=host&X-Goog-Signature=4674efe50025070bd9f90cfa2e5cf22ca84baa24e879d43fbc8f6e051ad7527a2577bd16f3a8dfbc7b9c15608827d795e1f6d77b6ef8dd6a67edcc0cdb585cc9292edcdd3bbeb0d3e015c1c446d4fa084adbba70bc09eeed625153f31408549e0467c0f178200d5f42e1d87f8f71312e5060c61a3572a64166c5648864aff91554ccd5a8423ecbd344956ea7ccb2bb306fc9aa76b583d8cead2a7d0c4bd5a285e2f3faef391db419b408161f7bcbbbbaec447083cbd899ec95173e6084bb6e981cd3f803b544d546d791ef2337576f91018f1f5dec21ce65bffa8332912d02aa602e9c7750594897e02788610dc91cb72d0e01296203decb607ae3456729e9bf" alt="No Tumor Brain" width="224" height="224"/> | <img src="https://lh3.googleusercontent.com/proxy/Cj5KVLSSp7dqz9oe1Wjx4lXviNT3gSCCCEyoJRb0Ckhftlr6XrXSxIy7-VX977EnZYF5453GAnae1iFA9b3uPAfyB-FEDGVUS_giBEod" alt="Tumor Evaluation" width="300" height="180" />


## Model Architecture
The research project employes [the Vision and the Core ML frameworks](https://developer.apple.com/machine-learning/core-ml/) to preprocess and classify fMRI data. The framework of Core ML is optimized for on-device performance for a variety of algorithms, it leverages Apple hardware, minimizes memory footprint and power consumption. The research project trains a brain tumor classifier using Create ML in Swift environment and applies the resulting model to real-time fMRI image classification using Vision.

## Experimentation

### Baseline brain tumor classifier with Create ML
For [image classification and object detection models, Create ML](https://developer.apple.com/documentation/vision/training_a_create_ml_model_to_classify_flowers) uses transfer learning to speed up the training process. By choosing the framework, there is no control over the chosen algorithm, limited control over hyperparameters (i.e. 25 max iterations) and performance metrics (precision, recall). The model size is relatively small due to not including the convolutional base as it is already embedded with iOS and macOS.

### Data Augmentation
Deep convolutional neural networks perform well on Computer Vision tasks, although, they are reliant on big data to avoid overfitting. Medical image analysis is among the application domains that do not have access to big data. Data augmentation is a data-space solution to the problem of limited data. The image augmentation algorithm are the techniques that enhance the size and quality of training datasets (e.g. geometric transformations, color space augmentations, kernel filters, mixing images, random erasing, feature space augmentation, adversarial training, generative adversarial networks, neural style transfer, and meta-learning). Augmentation techniques of image cropping, exposing, flipping, and rotating are added to the baseline Create ML classifier.

### Transfer Learning
Training numerous CNN hyper-parameters requires a lot of labeled training data and computing power. Therefore, using transfer learning technique allows us to take a pre-trained network to extract features, and train a new layer on top for the research task of classifying brain tumor images. The model is built with a TensorFlow-Hub image feature vector module followed by a linear classifier.

Approach | Training Accuracy | Validation Accuracy | Testing Accuracy
------------ | ------------- | ------------- | -------------
Create ML Baseline | 1 | 0.94 | 0.78
Create ML Data augmentation| 0.82 | 0.67 | 0.75
MobileNet Transfer Learning | 0.93 | 0.68 | 0.55

## Deployment
The deployment of the research project was implemented using the Create ML framework which is a Swift framework allowing to use [Xcode](https://developer.apple.com/xcode/)  and create machine learning models using Swift code. The saved project uses the Create ML Baseline Image Classifier as it demonstrated the best performance, and the app runs on an iOS device simulator 12.1. The results of the image classification process are seen on the screen.




