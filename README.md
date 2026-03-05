# Titanium_Microstructure_Analysis

This repository is provided as a step-by-step guide to train a U-net within a python environment for the purpose of extracting microstructural data from α+β titanium alloys. Once trained, the model implementation will provide the α-phase fraction, average α-lath width and average phase transition width using an intersection method. 

## Abstract
Microstructural characteristics play a significant role in the mechanical properties of metals. The scale and morphology of secondary phases significantly affect strength, ductility, fatigue, isotropy, fracture and impact toughness. Quantifying characteristics of the secondary phase can be important in designing for desired mechanical properties and can dictate manufacturing and processing. A specific case exists in α+β titanium alloys, where α and β phase fraction along with α-lath width will dictate subsequent properties [1, 2]. A standard exists for the quantification of α-lath width (ASTM E112), however is based on arduous grid placements and intercept counts. Karra et al. [3] suggests a more automated method, using OpenCV within the Python environment to filter the images and then provide a measurement of the average α lath width. This method requires tuning of filter parameters, such as denoising and thresholding settings which are dependent on the quality and contrast of the input images. 

Mongkhonthanaphon and Limpiyakorn [4] demonstrate the applicability and accuracy of using a U-net in segmenting microstructure of Ti-6Al-4V. The U-net was originally designed for biomedical image segmentation and found to require much fewer annotated training samples [5]. Microstructure can resemble biological cell structures and is therefore a prime candidate for similar image segmentation techniques.

This repository is provided as a step-by-step guide to train a U-net within a python environment for the purpose of extracting microstructural data from α+β titanium alloys. The benefit of this following method is that while bulk thresholding can work generally well when tuned correctly, its accuracy drops significantly once variability is introduced to the input micrographs. The U-Net method solves this by allowing for training on image sections with varying morphology and contrast, leading to much higher accuracy and robust identification.

This methodology uses small segments of micrographs, carefully threshold to create matching mask files, to train a fully convoluted neural network architecture for semantic image segmentation, a U-net. It has been found that with the careful selection of training segments, this method is more robust and requires less tuning than previous methods. This mitigates much of the inconsistency associated with tuning parameters, instead this method trains the U-net to recognize morphology from different contrasted images through iteration of training. 

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
The U-net model is trained on paired segments and masks. The U-net training uses the open-source segmentation models available with Pytorch. The supplied implementation uses the resnet18 encoder, 3 channels in each decoder layer and 1 output class. It uses 75 epochs for iteration and saves the model weights for the model implementation.

### 4.	Model implementation
 SEM micrographs should be prepared by cropping to remove information panels. Scale is set for the measurement of the lath intercept widths. The script provides the alpha phase fraction, average α-lath width, the average distance between intercepts of α+β phases, the length of line for the intercept method and the total number of intercepts. The average α-lath width uses skeletonization to find the width of each lath to average. The average width between phases is calculated from 100 random lines overlaid on the micrographs. This data is saved as a csv file.
The script saves the converted micrographs into processed masks. 

## Python Library Requirements 
opencv-python 

numpy 

matplotlib 

pandas

segmentation-models-pytorch

## Contact Information
Email: ryan.brooke@rmit.edu.au

Linkedin: Ryan Brooke


## References

[1]	S. Sadeghpour, S. M. Abbasi, M. Morakabati, and S. Bruschi, "Correlation between alpha phase morphology and tensile properties of a new beta titanium alloy," Materials & Design, vol. 121, pp. 24-35, 2017/05/05/ 2017, doi: https://doi.org/10.1016/j.matdes.2017.02.043.

[2]	D. H. Kohn and P. Ducheyne, "Tensile and fatigue strength of hydrogen-treated Ti-6Al-4V alloy," Journal of Materials Science, vol. 26, no. 2, pp. 328-334, 1991/01/01 1991, doi: 10.1007/BF00576523.

[3]	V. S. S. A. Karra, A. K. Verma, A. Guzel, A. Huck, and A. D. Rollett, "Quantification of Alpha Lath in Ti-6Al-4V using OpenCV," Materials Characterization, vol. 186, p. 111802, 2022/04/01/ 2022, doi: https://doi.org/10.1016/j.matchar.2022.111802.

[4]	S. Mongkhonthanaphon and Y. Limpiyakorn, "A Deep Neural Network for Pixel-Wise Classification of Titanium Microstructure," International Journal of Machine Learning and Computing, vol. 10, pp. 128-133, 01/01 2020, doi: 10.18178/ijmlc.2020.10.1.909.

[5]	O. Ronneberger, P. Fischer, and T. Brox, "U-Net: Convolutional Networks for Biomedical Image Segmentation," in Medical Image Computing and Computer-Assisted Intervention – MICCAI 2015, Cham, N. Navab, J. Hornegger, W. M. Wells, and A. F. Frangi, Eds., 2015// 2015: Springer International Publishing, pp. 234-241. 

 
