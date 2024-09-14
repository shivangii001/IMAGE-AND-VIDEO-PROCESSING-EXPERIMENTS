
# Digital Image And Video Processing Lab
## Experiment 6

## By
- _Shivangi Singh , 23EC65R14_
- _Pragati Maurya , 23EC65R30_

# Problem Statement 

Write Python functions to perform the following operations on the given test image,
ricegrains_mono.bmp. All functions must support binary images.

Q1. Make separate functions for erosion, dilation, opening, and closing of binary images

# Tech

- Python 2+
- Open CV 

# Imports
- math
- numpy
- cv2

# How to run

- Open ipynb file and run all cells
- Enter the choice of kernel to apply the operations
- Enter the choise of Morphological operation you want to perform on the image

# Functions

# custom_erosion(x,b)
- Takes as input the image - x, and the kernel - b.
- Performs erosion on the image using the defined algorithm.
- Returns the modified image.

# custom_dilation(x,b)
- Takes as input the image - x, and the kernel -b.
- Performs dilation on the image using the defined algorithm.
- Returns the modified image.

# generate_custom_kernel(dim)
- Takes as input the dimension of the kernel.
- Generates a square kernel with the given dimensions.
- Returns the generated kernel.

#A dditional Information
- The code provides options for selecting predefined kernels or creating custom kernels.
- Custom kernels can be generated with specific dimensions.
- The script supports binary image processing and morphological operations.