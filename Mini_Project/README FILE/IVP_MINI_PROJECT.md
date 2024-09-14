
# Digital Image And Video Processing Lab
## Mini Project - JPEG Compression

## By
- _Shivangi Shivmohan Singh , 23EC65R14_
- _Pragati Maurya , 23EC65R30_

# Problem Statement 
Designing an JPEG Image compression pipeline to compress coloured images of variable 
height and width

# Tech

- Python 2+
- Open CV 

# Imports
- math
- numpy
- cv2
- struct 
- bitarray

# How to run
- Open the IVP_MINI_PROJECT.ipynb file and run all cells
- Enter the file path on which to operate
- Choose the scaling on the standard quant matrix
- Enter the output file name
-  .jpg file will be save at the current directory and the images will be diaplayed

# Generated output images
- Generated output images can be found in output folder
- The name of the files are in the form [input file name][scaling factor on quant matrix].[jpg]

# Functions

# RGB_YCBCR(r,g,b)
- Takes as input the red, green and blue data stream
- Converts it to y, cb and cr components and returns them

# calculate_dct(a):
- Computes and returns the DCT of data stream a

# calculate_dct2(a)
- Computes the 2D Fast DCT of the given data stream

# perform_quant(a,q)
- Takes as input the data stream a and the quantizing matrix q
- Returns the quantized data stream

# run_length_encoding(z)
- Generates the run length encoding of the data stream z

# gen_huff_code(x)
- Generates the huffman code for the given data stream x

# compress_image(r,b,g)
- Takes as input the red, green and blue data stream
- Performs the entire JPEG Image compression pipeline and generates the output






