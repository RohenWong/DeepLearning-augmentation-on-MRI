# VAE_on_ISPY2
This project uses Variational Autoencoders (VAEs) to perform data augmentation on breast cancer MRI from the ISPY-2 clinical trial. We develop a novel augmentation architecture based on VAEs, to generate **both T2-weighted MRI and their corresponding tumour masks**. We train a U-Net segmentation model to evaluate if the augmentation proved useful for improving tumour segmentation. 

## Notebooks 

There are four notebooks, written and run (in order) on Google Collab: 

1. `ISPY_processing.ipynb` processes the 3D NIFTI images. The processing is summarised in **Fig 1**. The end result are NumPy arrays representing 2D axial slices of the images. These arrays are then loaded into Pytorch DataLoaders. 

\
**Fig 1: Overview of processing**
The 3D MRI scans are processed as follows: resample to the same resolution, take 2D axial slices, filter to slices of interest (those surrounding and including tumours). Then, crop and pad images to the same size. After that, (uniformly) crop out background. Lastly, perform mean/std normalisation on each image. The end result are numpy arrays (size 320x512) representing 2D slices of the images, which are then passed to Pytorch DataLoaders (and reshaped to size 128x128)
![alt text](https://github.com/RohenWong/VAE_on_ISPY2/blob/main/readme_pictures/processing.png?raw=true)

2. `ISPY_VAE.ipynb` specifies the architecture for the VAEs (**Fig 2**). VAEs are trained for the T1, T2-weighted MRI, as well as for the tumour masks (binary images). Training results and augmentations are visualised. 

\
**Fig 2: Simplified architecture of a VAE**
The 'image' here is a Pytorch Tensor of size (1, 128, 128). The images are grayscale, reflected in the channel size of 1. 
![alt text](https://github.com/RohenWong/VAE_on_ISPY2/blob/main/readme_pictures/VAE.png?raw=true)

3. `ISPY_VAE_multimodal.ipynb` contains our novel architecture for a VAE that works on both the T2-weighted MRI and tumour masks (multiple image modalities, hence the name). **This VAE generates a T2-weighted MRI, and its corresponding tumour mask. This overcomes the problem of augmented data not having the corresponding tumour pixel labels.**

\
**Fig 3: Simplified architecture of our multimodal VAE**
We are now working with Tensors of size (2, 128, 128). The first channel holds the (grayscale) T2, and the second holds the (grayscale) tumour mask. Encoder architecture is identical to **Fig 2**, however we now have **two decoders**, one for the T2, one for the Mask. 
![alt text](https://github.com/RohenWong/VAE_on_ISPY2/blob/main/readme_pictures/VAE_mm.png?raw=true)



4. `ISPY_UNet.ipynb` builds a U-Net architecture and trains the U-Net segmentation model on the original images. This is compared against a U-Net trained using our multimodal VAE augmentation. 


## Loading data

### Running the notebooks
I have saved two types of intermediate files: 

<b>Processed output from patient NIFTI </b>

- `t1np_stan.pkl`, `t2np_stan.pkl`, `mask_cropped.pkl` `patient_labels.pkl` 

<b>Further processed output that is to be passed to Pytorch DataLoaders </b> (these work on the previous output)

- `t1_arraysstan_32.pkl`, `t2_arraysstan_32.pkl`, `mask_arrayscropped_8.pkl`

In each notebook, I have included code chunks that use `gdown` to download the necessary datasets from my Google Drive. 

### Rerunning the processing
If you would like to rerun the processing, this is provided in `ISPY_processing.ipynb`. You will need to download the [patient NIFTI files](https://cloudstor.aarnet.edu.au/plus/s/dgiQZ2ftvWLnUq2), which have been provided by Robert Finnegan. What is not crucial, but might be of interest, is Robert's [prior code for converting the images into NIFTI](https://cloudstor.aarnet.edu.au/plus/s/1XvjIt81kN8UjBd). 

I then uploaded the NIFTI files onto my Google Drive - and each time I ran any Colab notebook, I [mounted Google Drive](https://stackoverflow.com/a/47019779) so that it had access to these NIFTI files. 
