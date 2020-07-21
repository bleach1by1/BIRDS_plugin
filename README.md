# BIRDS_plugin
BIRDS plug-in on Fiji and attached jar package
# Overview

BIRDS（Bi-channel Image Registration and Deep-learning Segmentation for efficient, versatile mapping of mouse brain）is an open source automation pipeline suitable for brain data from different microscopy platforms.
1.	The integration of Fiji platform facilitates the import and export of big data and preprocessing;
2.	High-precision registration of mouse brain data in different modalities with Allen CCFv3;
3.	Interactive platform for visual inspection of registration results and real-time correction;
4.	Mapping the three-dimensional digital framework in Imaris, and cell counting and neuron tracing can be performed under the framework;
5.	Prediction of brain areas of incomplete mouse brain data under the trained neural network.

All Rights Reserved.
# Before installing BIRDS
## Reference
If you use BIRDS in your work please cite our preprint paper:
Bi-channel Image Registration and Deep-learning Segmentation (BIRDS) for efficient, versatile mapping of mouse brain. https://www.biorxiv.org/content/10.1101/2020.06.30.181255v1 <br>
Some of our functions are built on the following tools:<br>
* Allen CCFv3 
* Fiji
* Elastix
* Imaris
* Python
* TensorFlow
* Deeplab v3+

# Acknowledgements
The program was developed by Prof. Peng Fei’s group. Prof. Fei oversaw the project. Weilin Zeng and Xuechun Wang created the basic frameworks for the pipeline, optimized the sub-workflows, implemented the code and integrate them into a complete procedure. The example brain data are from Prof. Yunyun Han’s group.

# 1 Installation and start
## Requirements
BIRDS is written in Java 1.8.0 and Python 3.6.1, and links to Imaris software module. The software runs on the Fiji platform. The deep-learning prediction module requires a Python environment, and the visualization and quantitative analysis are based on Imaris. To run the program smoothly, we recommend a computer workstation running Windows 10 with at least 6 CPU cores, 128Gb of RAM and SSD disks. A configuration with 256GB RAM / 8 CPU cores or above will allow better performance. A good graphic card (e.g., GeForce RTX 2080 Ti) is recommended for implementing efficient computation. In addition, if Imaris is used for 3D digital framework mapping and data analysis, a large-capacity hard drive (e.g., 4Tb or larger) is also required to save the cached data. It should be noted that the output of BIRDS is partially determined by the parameters set, users with different experiences may obtain different results. Therefore, it is recommended to use the default parameters preset in the program. 
## 1.1 Install Fiji
Download link: https://imagej.net/Fiji#Downloads. Please choose any windows version.
## 1.2 Install Python and dependencies (optional)
Setting up a virtual environment is required. It’s recommended to install a Python 3.6. Please download Anaconda for Python 3.6 at: https://www.anaconda.com/. The required dependency packages and recommended versions are:<br>
    TensorFlow-GPU == 1.10.0<br>
    xlwt == 1.3.0<br>
    xlrd == 1.2.0<br>
    xlutils == 2.0.0<br>
    NumPy == 1.18.3<br> 
    LibTIFF == 0.4.2 <br>
    OpenCV == 4.1.0<br>
## 1.3 Install Imaris (optional) 
Version 9.0.0 or above is recommended. https://imaris.oxinst.com/downloads
## 1.4 Local installation
Steps to install BIRDS on a workstation (Windows system):
#### 1.4.1 Download jar packages required for BIRDS plugin:
git clone https://github.com/bleach1by1/BIRDS_plugin <br>
Unzip the downloaded zip file (containing 5 jar packages). Open the Fiji’s folder, copy the birds.jar (from zip file) to the Fiji/plugins folder of Fiji and copy junrar-0.7.jar、ant-1.6.5.jar、poi-3.17.jar、ImarisLib.jar to Fiji/ jars folder
#### 1.4.2 Training the neural network to segment brain regions (optional):
git clone https://github.com/bleach1by1/birds_dl.git <br>
#### 1.4.3 Downloading path of example data:
https://github.com/bleach1by1/BIRDS_demo
## 1.5 Modules and workflow
BIRDS program contains five modules, which are according to different functions of workflow.<br>
Find the BIRDS in the Fiji plugins<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/1.png"/><br>

Select main-plugin to run. The GUI is shown in the below picture, with five modules indicated in the red box<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/2.png"/><br>

downsample -> image preprocessing <br>
coarse -> registration<br>
precise -> visual inspection and manual correction<br>
visual -> visualization and quantitative analysis<br>
setSeg -> prediction of brain segmentations by neural network<br>

# 2 Image import and preprocessing
The task of this module is to process the imported image to generate an isotropic low-resolution image for registration with the average Allen template. First, the users need to select a cache path to download the files necessary for running the program and save all the data generated by the program. BIRDS currently only supports TIFF format image, and users can convert other formats into “TIFF” after the image datasets being imported into Fiji (ImageJ).
## 2.1 Introduction of GUI and operations
The GUI of preprocessing module is shown in the below screenshot:<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/3.png"/><br>

The GUI contains three parts: 1: Click “choose” button to set the cache file path. BIRDS will download the necessary image files (Allen Reference Atlas, average template, annotation file) and generate 5 folders as: “cache”, “generate”, “registration”, “visual” and “dl” to store all intermediate data and final outputs. 2: Importing image datasets into Fiji. If the data is not in TIFF format, users should convert it to a TIFF sequence format. This can be easily done in Fiji as well. Then, click the “choose” button to select the image (the selected image name will be automatically displayed in the text box). Coronal view is the default view in our program (starting plane at the olfactory bulb). Users can convert their images to coronal view through the “Reslice” function in Fiji. The image resolution is set in the text box. In BIRDS, X and Y correspond to the horizontal and vertical axes of the coronal plane, respectively, and Z is the direction of the AP axis. We provided test data at https://github.com/bleach1by1/BIRDS_demo. After downloading the test data file to birdsDemo folder, the test dataset “YH298.tif” can be imported in Fiji for testing the software. 3: To accurately down-sample the imported experimental datasets, which might be obviously deformed by tissue clearing, users need to identify the corresponding feature planes on the experimental data and Allen template. 
## 2.2 Operation process and description of parameter
Import the image on the Fiji platform, as shown below (first import the image and then click “choose” button to select):<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/4.png"/><br>

The software automatically pops up “Allen Reference Atlas” as following:<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/5.png"/><br>

In the third part of the “downsample” module, users need to select 7 corresponding feature planes, and fill their serial numbers in the text boxes of “ourBrain” and “atlas”, respectively. While the first and last planes may vary by different brain datasets, the choice of five middle planes is usually fixed (atlas=43, 56, 62, 78, 105), owing to the obvious anatomical features exhibited in these five planes. As shown in the figure below, we have selected corresponding feature planes in the test dataset and Allen template. The rectangular boxes show the feature regions users should pay more attention to<br>

<img width="800" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/6.png"/><br>

After selecting corresponding feature planes, click “down” button to start the downsampling. This operation is usually finished within a few minutes, depended on the data size. After the downsampling is completed, BIRDS will generate and pop up two sets of images as: “outimage” and “temOrgimage” for next registration step. The generated images are shown as below:<br>

“outimage”:<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/7.png"/><br>

“temOrgimage”:<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/8.png"/><br>

After completing all the steps in image preprocessing, BIRDS pops up the “finish!” window. The isotropic low-resolution dataset generated by image preprocessing has been stored under the cache path, and all the downsampling information is stored in the “generate” folder of the cache path in form of a “dataset.xml” file.

# 3 Registration
The “registration” module implements the following tasks:
1.	Calculate the PC feature maps from image dataset and average template (optional);
2.	Manually extract 3D axial features from image dataset and average template (optional);
3.	Register Allen average template to image dataset;
4.	Obtain the transformation parameters and apply them to Allen annotation file.

## 3.1 The GUI of “registration” module

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/9.png"/><br>

The registration module contains three parts as:<br>
1. Select feature maps for multi-channel registration. Three image datasets usable for registration are: “invert”, brain images with grayscale reversed along three axes; “PC”, brain images with the edge and texture features extracted by phase congruency; “raw”, the original brain images (red box 1). While “raw” is required for registration, the selection of “invert” or “PC” is optional (check/uncheck the check boxes). With selecting the “invert” dataset, users will enter the manual parameter setting step (red box 3), which is further described in 3.2. In addition, please enter a weighting factor for each group of image dataset in the textbox. The typical weighting factors for three channels could be 0.25: 1.25: 1.25 for STP data, and 1: 0.75: 2.25 for light-sheet data. <br>

2. Select the registration model and the number of iterations (red box 3). The default setting for this step is a hybrid “rigid + affine + B-spline” registration model with 800 iterations, which means, all checkboxes are checked. A user who wants to obtain the registration result in a short time can just check “bspline” and only use the “raw” image for B-spline registration. This will possibly take only 5~8 minutes to obtain a less accurate result. <br>

3. Unlike PC feature images generated by the computer automatically, the generation of “invert” image requires manual operations to define the three axial planes for further feature enhancement by grayscale reversion. The “invert” part shows the number of marker lines labelled for defining the three axis planes, respectively. By clicking the “YZ” button, user can switch the views of three types of marker lines (yellow for defining yz plane, orange for defining xy plane and blue for define xz plane). The “delete” button allows the modification of the marker lines, and “invert” button inverts the grayscale according to the finally defined axes. The operation of grayscale reversion is further detailed in 3.2.<br>

After the abovementioned three steps finished, users can click the “OK” button to start the registration procedure. All registration files including the images and registration parameter files are generated in the “coarse” folder of the cache file path.

## 3.2 Marking line and reverse grayscale operation
After checking the “invert” checkbox in the “feature” part, the manual labeling operation is started. BIRDS will pop up the following image window for marking the axes:<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/10.png"/><br>

Please use the line drawing function on the Fiji to define the marker lines. The below picture shows the marker line (also center line) selected in a coronal plane. For accurately determining the yz plane, sufficiently many maker lines are recommended.<br>

<img width="300" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/11.png"/><br>

For defining the XY plane, users can mark two vertical lines, as exampled in the below pictures, on two selected coronal slices (here ~ 280th layer of average template features) after carefully checking the structure features “Bregma” of the mouse brain. If the brain shows very symmetrical coronal planes, users can also mark two lines on the same coronal slice.<br>

<img width="300" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/12.png"/><br>

<img width="300" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/13.png"/><br>

In a similar way, for defining the XZ plane, users can mark two horizontal lines on two coronal slices (e.g., at ~210th layer of average template, at which the corpus callosum is about to converge; and ~600th layer of average template, at which the inverted triangle appears). The examples are given in below pictures:<br>

<img width="300" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/14.png"/><br>

<img width="300" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/15.png"/><br>

After the three axial planes are successfully marked, users can click “invert” button to automatically generate the grayscale- reversed image based on the foreground of the raw image. The “invertImage” then pops up to give the preview of the generated result. It’ noted that users can use the Fiji “Orthogonal Views” to see if the axis is correctly defined. If it is not correct, users can go back to revise the marker lines and repeat the process.<br>

<img width="300" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/16.png"/><br>

# 4 Visual inspection and manual correction
This module will implement the following tasks:
1.	Read the current position of the mouse and display the annotation result for visual inspection;
2.	Real-time manual correction through interactive operation.
The GUI of this module is shown in the below picture:<br>

<img width="300" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/29.png"/><br>

The module contains two parts: <br>
1: Correction, which is sub-divided into “parameter” and “deformation” functions. <br>
2: Visual inspection. The “X” and “Y” show the coordinates of the current mouse position, and “name” shows the name of the brain area at the current mouse position.<br>

When pipeline goes to this module, BIRDS automatically pops up the annotation result generated from the finished registration, which might be not accurate enough.<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/28.png"/><br>

As shown in the example image, the input image is displayed in red color, and the annotation information is shown as the green mesh.

## 4.1 Operation of manual correction
If a slight misalignment of the annotation result is found after visual inspection, manual correctionhttps://github.com/bleach1by1/figure_github/blob/master/figure_github/18%20-%20%E5%89%AF%E6%9C%AC.pn is allowed by this step. <br>
The below example picture guides the user on how to perform the correction:<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/30.png"/><br>

Set values in “girdX” and “girdY”. These parameters represent the degree of correction, the greater is the misalignment, the larger values should be set. The default value of “girdX” (“girdY”) is 30, and the number of iterations (“iter”) is 10 times. During operation, please use the arrow (through Fiji’s line drawing function) to drag the annotation line (green line) to the correct position. “delete”: delete the current arrow; “back” return to the last correction; “previous correction ”: implement a deformation using the parameters saved for the last-time correction; “save” save the current operation after all corrections have been made.

# 5 Visualization and quantitative analysis
This module links BIRDS to Imaris, and performs the following tasks:
1.	Using the annotation file of image dataset to map the CCFv3 in Imaris;
2.	Neuron tracing under the CCFv3 in Imaris (optional);
3.	Cell counting under the CCFv3 in Imaris (optional).

After going through the workflow 1 to 4，users could map the whole CCFv3 for visualization and quantitative analysis. The GUI of this module is shown in the below picture:<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/20.png"/><br>

Since the registration is performed at an isotropic low resolution, we first need to restore the resolution of annotation files to match it with the image data. Users first input the number of “X(pixel)”， “Y(pixel)”, and  “Z(pixel)” in the original-resolution image, and then click “up” to generate the annotation file at high resolution. When finished, the “finish!” window will pop up. Users can import the generated “annotation.tif” file located at cache path>>visual>> into Imaris (X64 9.0.0), and click the “connect” button to link the program with Imaris. In the GUI, after the status of “Imaris” changing from “False” to “True”, click “Generate” to start the generation of 3D brain segmentation (volume rendering of brain surface) containing hierarchical structure in Imaris. The segmentation generation progress is displayed at the bottom, and can be stopped anytime. Interplay with Imaris is shown in the below screenshot:<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/21.png"/><br>

# 6 Prediction of brain areas by neural network
This module runs under a Python environment and implements the following tasks:
1.	Use the trained neural network parameters to predict the segmentations for brain image data directly
2.	Read the current position of the mouse brain and display the annotation result for visual inspection;
3.	Output the prediction result of the image dataset, with different brain areas being color-encoded.

The GUI of this module is shown in the below picture:<br>

<img width="400" src="https://github.com/bleach1by1/figure_github/blob/master/figure_github/22.png"/><br>

The “log” allows users to load a trained neural network node. Under the test data folder (birdsDemo), a file with name “out.pd” can be used for test (for STP image, sagittal view prediction ). “Image”: load the file path of a dataset to be predicted, and the format should be a TIFF sequence in RGB. A trial image dataset with name “YH285.tif” is provided under the test data folder (birdsDemo) for users’ test. “Parame”: input an Excel file which specifies the name and color codes of the brain areas to be predicted. An Excel file with name “region_dl.xls” is provided under the test data folder (birdsDemo) for users’ test. Clicking “upload” button to select the Excel file. It should be noted that “region_dl.xls” corresponds to “out.pd”, and both of files are located at the birdsDemo folder. In general, owing to the limitations of hardware resources and prediction accuracy, the number of brain areas to be predicted at once should not be too large (20 regions in test data). Color coding is used for specifying the different segmented areas, when outputting and displaying the predicted results. Clicking “validation” to start the prediction of the brain area. After the network prediction being finished, a pop-up window displays the generated results with X, Y coordinates of the region of interest (ROI) shown in real time. The predicted segmentation data are saved in the “dl” folder under the cache path.










