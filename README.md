# VAE_on_ISPY2
This project uses Variational Autoencoders (VAEs) to perform data augmentation on breast cancer MRI from the ISPY-2 clinical trial. We develop a novel augmentation architecture based on VAEs, to generate **both T2-weighted MRI and their corresponding tumour masks**. We train a U-Net segmentation model to evaluate if the augmentation proved useful for improving tumour segmentation. 

## Notebooks 

There are four notebooks, written and run (in order) on Google Collab: 

1. `ISPY_processing.ipynb` processes the NIFTI files. Resampling to same resolution, cropping/padding to size 512 x 512, then uniformly cropping out background. Mean/std normalisation is then performed on each image. The end result are numpy arrays representing 2D slices of the images. 
2. `ISPY_VAE_working.ipynb` loads the numpy arrays into pytorch `DataLoaders`, and specifies the architecture for our developed VAEs. VAEs are trained for the T1, T2-weighted MRI, as well as for the tumour masks (binary images). Training results and augmentations are visualised. 
3. `ISPY_UNet.ipynb` builds the U-Net architecture and trains the U-Net segmentation model on the original images. This notebook also contains the architecture for our novel VAE augmentation, which generates both T2-weighted MRI and their corresponding tumour masks. Batches are augmented, and test set DICE is compared against test set DICE from the unaugmented data.  


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
