# Optical Microscopy Data Analysis Project

## Project Overview
This project focuses on analyzing data from a single field of view from the FISH dataset `5500000013`. The data contains information on **hiPSC-derived cardiomyocytes** (heart muscle cells with striations). The goal of the project was to analyze this data and derive insights into the structure and organization of cell nuclei.

The project utilizes optical microscopy images acquired using advanced confocal microscopy techniques. 

Code was tested on data: https://open.quiltdata.com/b/allencell/packages/aics/integrated_transcriptomics_structural_organization_hipsc_cm/tree/latest/raw_images/FISH/

FISH 5500000013

## Contents
- **`Mikroskopia_optyczna`**: Contains the complete code developed for data analysis, including preprocessing, segmentation, and measurements. This file also includes detailed descriptions of the analyses performed and results obtained.  
- **`Projekt_Mikroskopia_optyczna_Oleg_Łyżwiński.pdf`**: Describes the dataset, experimental setup, and the full methodology, along with a comprehensive report on the analysis.  
- **`Microscopy.yml`**: Defines the environment used for this project, including all required libraries and dependencies.

## Data Collection
The data was collected using a **3i spinning-disk confocal microscope** equipped with:
- **63x/1.2 W C-Apochromat Korr UV-vis IR objective lens (Zeiss)**  
- **0.83x tube lens**, resulting in a final magnification of 52.29x.  

### Fluorescence Channels:
- **DAPI (450/50 nm)**: Stains DNA for visualizing cell nuclei.
- **mEGFP (525/50 nm)**: Enhanced green fluorescent protein used for tagging proteins.
- **Alexa 546 (600/50 nm)**: Fluorescent dye emitting in red-orange spectrum.
- **Alexa 647 (690/50 nm)**: Fluorescent dye emitting in far-red spectrum.

### Imaging Conditions:
Live cells were imaged in phenol-red-free RPMI-1640 medium supplemented with B27 and insulin at **37°C and 5% CO₂**. Bright-field images were obtained using an LED light source with a peak emission at 740 nm.

## Key Analysis Steps
1. **Image Preprocessing**:
   - Gaussian and median filtering for noise reduction.
2. **Segmentation**:
   - Two approaches were used:
     - Neural network-based segmentation using `napari-accelerated-pixel-and-object-classification`.
     - Classical segmentation methods (Otsu thresholding, roundness checks, and removal of small objects).
3. **Measurements**:
   - The volumes of cell nuclei were calculated using pixel dimensions and z-step size. Outlier removal was performed for accurate volume analysis.

### Results:
- Mean nuclear volume: **4.35 μm³**
- Standard deviation: **0.61 μm³**

## Tools and Technologies
- **Programming Language**: Python  
- **Libraries**: 
  - `napari`
  - `numpy`
  - `scipy`
  - `skimage`
  - `matplotlib`
  - Additional dependencies listed in `Microscopy.yml`.


# ObjectSegmenter for Microscopy Images

The `ObjectSegmenter.cl` file is part of the `apoc` library, used for object segmentation in microscopy images. Specifically, it is an OpenCL-based implementation of a machine learning model for segmenting objects (e.g., cell nuclei) in images. The `.cl` extension indicates that the file contains OpenCL code, a framework for writing programs that execute across heterogeneous platforms (e.g., CPUs, GPUs).

## Key Points About ObjectSegmenter.cl

### Purpose
The ObjectSegmenter is trained to segment objects (e.g., cell nuclei) in microscopy images. It uses features derived from the input image (e.g., Gaussian blur, Sobel filters, Laplacian filters) to learn how to distinguish objects from the background.

### Training
The `train` method is used to train the model. It takes two main inputs:
1. A string specifying the features to extract from the image (e.g., `sobel_of_gaussian_blur=2 laplace_box_of_gaussian_blur=2 gaussian_blur=2`).
2. A manually annotated image (`manual_annotations`) that serves as the ground truth for training.

The model learns to predict the segmentation based on the provided features and annotations.

### Prediction
After training, the `predict` method is used to segment new images. It applies the learned model to the input image and outputs a labeled image where each object is assigned a unique label.

### OpenCL
The `.cl` file contains OpenCL kernels that are used to accelerate the computation of image features and segmentation. OpenCL allows the code to run efficiently on GPUs or other parallel processing units.

## How It Fits into Your Project

### Input Data
The code processes microscopy images (`C1.tif`) of hiPSC-derived cardiomyocytes.

### Preprocessing
The images are filtered using Gaussian and median filters to reduce noise and enhance features.

### Training
The ObjectSegmenter is trained on manually annotated images (`layer2.tif`) to learn how to segment cell nuclei.

### Segmentation
The trained model is applied to the input images to generate segmentation masks.

### Postprocessing
The segmented objects are refined using morphological operations (e.g., opening, removing small objects) and filtered based on properties like eccentricity and solidity to ensure only well-defined, complete cells are retained.

## Example Features Used in Training
- **sobel_of_gaussian_blur=2:** Applies a Sobel filter to the Gaussian-blurred image (sigma=2).
- **laplace_box_of_gaussian_blur=2:** Applies a Laplacian filter to the Gaussian-blurred image (sigma=2).
- **gaussian_blur=2:** Applies a Gaussian blur (sigma=2).