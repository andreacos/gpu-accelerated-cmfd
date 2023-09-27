![Image](vippdiism.png)

# GPU-accelerated SIFT-based copy-move forgery detection for very high resolution images
A Python package for copy-move forgery detection based on GPU acceleration for Gigapixel imagery. This repository is an implementation of the method in: 

_[A GPU-accelerated Algorithm for Copy Move Detection in large-scale satellite images](http://clem.dii.unisi.it/~vipp/website_resources/publications/conferences/2023_SPIE_Paper_CopyMoveGPU.pdf)_

Mauro Barni, Andrea Costanzo, Giovanna Maria Dimitri and Benedetta Tondi.
Processings of SPIE Sensor+Imaging, Amsterdam, Netherlands, 3 - 6 September 2023.

A new, optimised tool that can efficiently detect and localise copy-move forgeries in satellite images where off-the-shelf, state-of-the-art copy-move detectors fail. By building upon an existing method that performed well on standard images but was unable to run successfully on satellite images, in this GPU-accelerated method each step of the detection pipeline has been either optimised or replaced with new, more efficient algorithms. By doing so, the algorithm can process satellite images with resolution above 1 Gigapixels with good performance in terms of processing time, whereas the original technique and its existing optimisations are crashing out.

## Installation

Use Anaconda and the provided **environment.yml** to set up the environment:

```
$ conda env create -f environment.yml
$ conda activate cuml-torch-new
```

## Running the copy-move detector

Use one of the following commands to check the input arguments:
```
$ python3 cmd-copy-move-detector.py -h
$ python3 cmd-copy-move-detector.py --help
```
This will print the help:
```
usage: cmd-copy-move-detector.py [-h] [-m OUT_MASK] [-o OUT_IMG] [-ty {1,2,4,6,8,16,24,32,64}]
                                 [-tx {1,2,4,6,8,16,24,32,64}] [-ls {v1.0,v1.5,v2.0,v3.0}]
                                 [-gpu {0,1}] [--maxsize MAXSIZE] [-v {0,1}]
                                 input_img

GPU-accelerated copy-move detector

positional arguments:
  input_img             path to the input image (GIF files are not supported)

optional arguments:
  -h, --help            show this help message and exit
  -m OUT_MASK, --out-mask OUT_MASK
                        path to the output binary mask. In this mask, white regions (pixel value 255)
                        indicate pristine content and black regions (pixel value 0) indicate cloned
                        regions. If no path is specified, the output mask is saved in the same
                        directory of the input image.
  -o OUT_IMG, --out-img OUT_IMG
                        path to the output RGB image, that is a composite image which makes use of
                        overlays to highlight copy-moved regions from the background.
  -ty {1,2,4,6,8,16,24,32,64}, --tiles_y {1,2,4,6,8,16,24,32,64}
                        When performing SIFT detection on image tiles, this is the number of tiles
                        along image rows. Set both -ty and -tx to 1 to work with full image
  -tx {1,2,4,6,8,16,24,32,64}, --tiles_x {1,2,4,6,8,16,24,32,64}
                        When performing SIFT detection on image tiles, this is the number of tiles
                        along image columns. Set both -ty and -tx to 1 to work with full image
  -ls {v1.0,v1.5,v2.0,v3.0}, --localization_sliding {v1.0,v1.5,v2.0,v3.0}
                        When a copy-move is detected, a sliding window approach is required to
                        estimate the localisation map. Use 'v.1.0' for the original, slow method
                        based on Skimage, 'v1.5' for the original method partially speeded up with
                        GPU, 'v2.0' for a new method fully reworked for PyTorch on GPU.
  -gpu {0,1}            Set to 1 to use GPU acceleration (if available) for SIFT computation (SILX
                        only) and matching computation (all detectors)
  --maxsize MAXSIZE     image size at which detection is performed. If the input image has width or
                        height (or both) larger than this value, detection is performed on a sub-
                        sampled version of the input image to speed up processing. The subsampling is
                        chosen in such a way that the maximum size of the image is equal --max-value.
                        Output images and masks are resized to the original input image size.
  -v {0,1}, --verbosity {0,1}
                        increase output verbosity (up to two levels)
```

## Using GPU acceleration

Use ```-gpu 1``` to enable GPU or ```-gpu 0```  to work on the CPU.

## Using tiled-SIFT

To set the number of image tiles, use ```--tx``` (vertical tiles) and ```--ty``` (horizontal tiles). This will split the image
into --tx \times --ty tiles processed separately.

## Choosing the localisation algorithm

The software supports 4 different localisation strategies, which produce similar (or exactly the same) localisation maps. The difference is in their optimisations. Use the ```-ls``` argument to select the localisation algorithm.
- v1.0: the original Amerini et al.'s method, which works only on small images and takes hours (days) on very high resolution images
- v1.5: the original method, partially speeded-up with GPU, but sharing the same problems of the original
- v2.0: new method with full GPU acceleration and no sliding window
- v3.0: new method, improving v2.0, with optimised image warping and zncc computation with full GPU acceleration and no sliding window



