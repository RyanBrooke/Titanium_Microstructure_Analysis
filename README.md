Microstructural characteristics play a significant role in the mechanical properties of metals. The scale and morphology of secondary phases significantly affect strength, ductility, fatigue, isotropy, fracture and impact toughness. Quantifying characteristics of the secondary phase can be important in designing for desired mechanical properties and can dictate manufacturing and processing. A specific case exists in α+β titanium alloys, where α and β phase fraction along with α-lath width will dictate subsequent properties. Standards exist for the quantification of α-lath width (ASTM E112), however is based on arduous grid placements and intercept counts. Karra et al. [1] suggest a more automated method, using OpenCV within the Python environment to filter the images and then provide a measurement of the average α lath width. This method requires tuning of filter parameters, such as denoising and thresholding setting which are dependant on the quality and contrast of the input images. 
This provided methodology instead uses small segments of micrographs, carefully threshold to create matching mask files, to train a fully convoluted neural network architecture for semantic image segmentation, a unet. It has been found that with the careful selection of training segments, this method is more robust and requires less tuning than previous methods. This mitigates much of the inconsistency associated with tuning parameters, instead this method trains the unet to recognize morphology from different contrasted images through iteration of training. 
The U-net was originally designed for biomedical image segmentation and found to require much fewer annotated training samples [2]. Microstructure can resemble biological cell structures and is therefore a prime candidate for similar image segmentation techniques. 
The method requires pairs of matched patches and masks as ground truth to train the U-net. 
Methodology
1.	Training data preparation
For the ease of creating ground truth for the U-net, large SEM micrographs (2560x2048 pixels) are segmented into 256x256 pixels. This allows smaller sections of the micrographs to be made into masks representing the required feature identification. Then once trained, the U-net can identify all of this feature in the full micrograph. This also ensures less exhaustive mask creation and computational training.   

2.	Creating ground truths
This step creates masks that identify the features of interest. An example would be the α-phase in a α+β microstructure. For training the U-net, representative segments of the microstructure will ensure accurate training. This can be iterated on, if certain areas of the microstructure is not identified correctly. 
Ground truth masks from segments can be created using thresholding methods, such as bilateral filters or Gaussian thresholds or combination of methods. The goal is to create as accurate a mask from the segment as possible. Generally, a bulk thresholding method then manual touch up was used. This is the power of the method, where bulk thresholding methods can work generally well when setup correctly, once contrasts change it can make the threshold methods much less accurate. The unet method allows training on sections of images with various contrast, leading to more generalised accuracy of identification. 
It is advised to begin with at least 5 mask/segments for ground truth training, but iteration on poorly identified sections will likely be required until a desired accuracy. Similarly, multiple masks/segments should be taken from different contrasted images if included in the dataset. 

3.	Model training
The U-net is a fully convoluted neural network architecture for semantic image segmentation. The U-net model is trained on paired segments and masks. The U-net training uses the open-source segmentation models available with pytorch. The implementation uses the resnet18 encoder, imagenet pretraining encoder weight, 3 channels in each decoder layer and 1 output class. It uses 50 epochs for iteration and saves the training weight for the model implementation.

4.	Model implementation
SEM micrographs are cropped to remove information panels. Scale is set for the measurement of the lath intercept widths. The script provides the alpha phase fraction, average α-lath width and the width between intercepts of α+β phases. The average α-lath width uses skeletonization to find the width of each lath to average. The average width between phases is calculated from 100 random lines overlaid on the micrographs. 
The script saves the converted micrographs into processed images. 




[1]	V. S. S. A. Karra, A. K. Verma, A. Guzel, A. Huck, and A. D. Rollett, "Quantification of Alpha Lath in Ti-6Al-4V using OpenCV," Materials Characterization, vol. 186, p. 111802, 2022/04/01/ 2022, doi: https://doi.org/10.1016/j.matchar.2022.111802.
[2]	O. Ronneberger, P. Fischer, and T. Brox, "U-Net: Convolutional Networks for Biomedical Image Segmentation," in Medical Image Computing and Computer-Assisted Intervention – MICCAI 2015, Cham, N. Navab, J. Hornegger, W. M. Wells, and A. F. Frangi, Eds., 2015// 2015: Springer International Publishing, pp. 234-241. 

