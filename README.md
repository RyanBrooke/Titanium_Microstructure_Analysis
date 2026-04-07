# Titanium_Microstructure_Analysis

This repository is provided as a step-by-step guide to train a U-net within a python environment for the purpose of extracting microstructural data from α+β titanium alloys. Once trained, the model implementation will provide the α-phase fraction, average α-lath width and average phase transition width using an intersection method. 


## Methodology
### 1.	Training data preparation
 The method requires pairs of matched patches (smaller segments of the original SEM micrograph) and masks (of the desired phase to measure) as ground truth to train the U-net.  For the ease of creating ground truth for the U-net, large SEM micrographs (2560x2048 pixels) are segmented into 256x256 pixels. This allows smaller sections of the micrographs to be made into masks representing the required feature identification. Then once trained, the U-net can identify all of this feature in the full micrograph. This also ensures less exhaustive mask creation and computational training.   

### 2.	Creating ground truths
  This step creates masks that identify the features of interest. An example would be the α-phase in a α+β microstructure. For training the U-net, representative segments of the microstructure will ensure accurate training. This can be iterated on, if certain areas of the microstructure is not identified well by the model. 
Ground truth masks from segments can be created using thresholding methods, such as bilateral filters or Gaussian thresholds or combination of methods. The goal is to create as accurate a mask from the segment as possible. Generally, it was found a bulk thresholding method then manual touch created the most accurate masks. 
Two scripts are provided which converts grayscale micrographs patches into clean, binary ground-truth masks either one-by-one for parameter tuning or in a batch process. Variations are provided as dependant on the supplied patches (microstructure, contract etc.) success is variable. The two variations of the script achieve this through a sequence of filters:
    1.	a non-local means filter to smooth out grain noise, an adaptive threshold to separate structural features based on local lighting, and morphological size filters to fill internal holes and erase isolated background artifacts.
    2.	applies Contrast Limited Adaptive Histogram Equalization (CLAHE) to enhance local contrast and bilateral/non-local means filters to smooth out granular noise while preserving sharp edges, before using adaptive thresholding and morphological size filters.
It is advised to begin with at least 5 mask/segments for ground truth training, but iteration on poorly identified sections will likely be required until a desired accuracy. Similarly, multiple masks/segments should be taken from different contrasted images if included in the implementation dataset. 

### 3.	Model training
The U-net model is trained on paired segments and masks. The U-net training uses the open-source segmentation models available with Pytorch. The supplied implementation uses the resnet18 encoder, 3 channels in each decoder layer and 1 output class. It uses 100 epochs for iteration and saves the model weights for the model implementation.

### 4.	Model implementation
SEM micrographs should be prepared by cropping to remove information panels. The model is implemented using test-time augmentation (TTA) creating four versions of the micrograph (original, horizontal flip, vertical flip and 180° rotation) and the final mask and measures calculated as the average. Scale is set for the measurement of the lath intercept widths. The script provides the alpha phase fraction, average α-lath width (from skeletonization), a stereologically adjusted measure of 3D α-lath width (based off [1]), the mean linear intercept (MLI) of α+β phase transitions, the length of line for the intercept method and the total number of intercepts. The sterologically adjusted 3D α-lath width and MLI are calculated from 100 random lines overlaid on the micrographs. 
A confidence measure for each model prediction is provided, calculated by the variance of prediction using the TTA. Data is saved as a .csv file.
As secondary .csv file is provided which has calculations for the average of the measurement across the file directory. Similarly, this provides the standard deviation, 95% confidence interval and a relative accuracy as highlight by ASTM E112-3.
The script saves the converted micrographs into processed masks. 

## Python Library Requirements 
opencv-python 

numpy 

matplotlib 

pandas

segmentation-models-pytorch

scipy.stats

## Contact Information
Email: ryan.brooke@rmit.edu.au

Linkedin: Ryan Brooke

## Reference 
[1] V. S. S. A. Karra, A. K. Verma, A. Guzel, A. Huck, and A. D. Rollett, "Quantification of Alpha Lath in Ti-6Al-4V using OpenCV," Materials Characterization, vol. 186, p. 111802, 2022/04/01/ 2022, doi: https://doi.org/10.1016/j.matchar.2022.111802
 
