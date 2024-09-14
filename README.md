# IMAGE-AND-VIDEO-PROCESSING-EXPERIMENTS
Image and Video Processing  Laboratory Experiments
# Image and Video Processing Experiments

Welcome to the **Image and Video Processing Experiments** repository! This repository contains various experiments related to image and video processing. 
Each experiment is organized into its own directory and includes detailed documentation, input and output files, and code.

## Repository Structure

- **experiment1/**: Contains files for Experiment 1, including README.md, input files, output files, and code.
- **experiment2/**: Contains files for Experiment 2, including README.md, input files, output files, and code.
- **experiment3/**: Contains files for Experiment 3, including README.md, input files, output files, and code.
- **experiment4/**: Contains files for Experiment 4, including README.md, input files, output files, and code.
- **experiment5/**: Contains files for Experiment 5, including README.md, input files, output files, and code.
- **experiment6/**: Contains files for Experiment 6, including README.md, input files, output files, and code.

## Experiments Overview

- **[Experiment 1](experiments/Exp-01)**:
All functions must support 24-bit RGB and 8-bit grayscale image
formats.
Q1. Read .bmp Image:
a) Input: Filename of input image
b) Output: BMP header structure printing height, width, bit width, file size in
bytes, and offset size, image pixel array loaded onto memory.
Note: If the input image is not a .bmp, then it should print an error message.
Q2. Write .bmp Image:
a) Input: Filename of output image, BMP header structure, and Image pixel array
(read using previous function).
b) Output: Write the loaded pixel array in to the disk as an image in .bmp file
format.

Q3. Color Channel Manipulation:
a) Input: Read the file ‘corn.bmp’ using your previous ReadBMP function.
b) Output: Set each channel of the ‘corn.bmp’ to zero at an instance (e.g., set the
‘R’ channel to zero and without changing the intensity values of other
channels, repeat it for ‘G’ and ‘B’ channel) and save the .bmp file with modified
pixel data using WriteBMP function..

- **[Experiment 2](experiments/EXP-02)**:
All functions must support 24-bit RGB and 8-bit grayscale image
formats.
1. Operations on the Image:
a) Input: Image pixel array
b) Output: Scale the images with different scale factors along with x and y axis
with–
1. Scaling factor greater than 1
2. Scaling factor less than 1

2 .Operation on the image
a) Input: Image pixel array
b) Output: 45-degree and 90-degree rotation of both colour and grayscale
images and perform interpolation(using nearest neighbour and bilinear)
- **[Experiment 3](experiments/EXP-02)**: Description and details of Experiment 3.
- **[Experiment 4](experiments/EXP-02)**: Description and details of Experiment 4.
- **[Experiment 5](experiments/EXP-02)**: Description and details of Experiment 5.
- **[Experiment 6](experiments/EXP-02)**: Description and details of Experiment 6.

## Getting Started

To get started with this repository, follow these steps:

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/shivangii001/IMAGE-AND-VIDEO-PROCESSING-EXPERIMENTS.git
2. **Navigate to the Experiment Directory:**

    For example, to work on Experiment 1:
    
    ```bash
    cd IMAGE-AND-VIDEO-PROCESSING-EXPERIMENTS/experiment1

3. **Read the Experiment's README.md:**

    Each experiment directory contains a README.md file with specific instructions, descriptions, and details about the experiment. Open this file to understand how to use the files in that directory.

4. **Run the Code:**

    Follow the instructions provided in each experiment's README.md file to run the provided code and analyze the results. This will typically involve setting up any required dependencies, executing scripts or programs, and reviewing the output files.

