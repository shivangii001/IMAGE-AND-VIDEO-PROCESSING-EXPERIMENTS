# Image Enhancement and Gaussian Unblur using Google Colab


## Image And Video Processing Lab
## Experiment 4

## By
- _Shivangi Singh , 23EC65R14_
- _Pragati Maurya , 23EC65R30_

# Problem Statement 

Q1. Write python modular functions/subroutines to design spatial filters - Mean, Median, Prewitt, Laplacian, Sobel kernels (horizontal, vertical, diagonal), Gaussian Blur, Laplacian of Gaussian on a stack of grayscale images (say, 15 images per stack).

Q2. Create a filter called Gaussian_Unblur to undo the effects of blurring.

# Tech

- Python 2+
- Open CV 

# Imports
- math
- numpy
- cv2
- os

# How to run

- Open terminal and run 'experiment4.py'.
- Enter folder path.
- Select image.
- Select Filter.
- Select kernel size.
- Click on Enhance Image to display image.

# Functions

# mean_filter_kernel(n)
- Generate the mean filter kernel of dimension nxn
- Returns the generated kernel

# median_filter(img,filter_size)
- Takes as input the img and the dimension of the filter
- Applies the required filtering and returns the new_img

# prewitt_filter_kernel(orientation)
- returns the prewitt filter for the desired orientation

# laplacian_filter_kernel()
- returns the laplacian filter kernel

# sobel_filter_kernel(orientation)
- returns the sobel filter for the desired orientation

# gaussian_filter_kernel(n,sigma)
- returns the gaussian kernel with the given n and sigma

# convolve(img,kernel)
- Takes the given image and type of kernel as input
- Flips the kernel horizontally and vertically and performs the convolution
- Returns the convoled resultant image 

# gaussian_unblur(img)
- Takes the img on which gaussian filter has been apllied
- Performs the unblur operation to sharpen the image 
- Returns the gaussian unblurred image




This project demonstrates image enhancement techniques and Gaussian unblurring using Google Colab. It provides a user-friendly interface for selecting and applying various filters to enhance images. Additionally, it includes a Gaussian unblurring algorithm.

Before running this code in Google Colab, make sure you have the following:

- A Google Colab account and environment.
- Some sample images to enhance or unblur.
- Basic knowledge of image processing concepts.

## Usage

### Image Enhancement

1. Open the provided Colab notebook in Google Colab.
2. Execute the code cells one by one.


```python
import cv2
import ipywidgets as widgets
from IPython.display import display
import os
import numpy as np

def within_bounds(i, j, n):
    if (i < 0) or (i >= n) or (j < 0) or (j >= n):
        return False
    return True
```


```python
def mean_filter_kernel(n):
    return [[1 for _ in range(n)] for __ in range(n)]

def median_filter(img, filter_size):
    img_size = len(img)
    new_img = [[0 for _ in range(img_size)] for __ in range(img_size)]

    for i in range(img_size):
        for j in range(img_size):
            pixel_vals = []
            for k in range(filter_size):
                for l in range(filter_size):
                    if within_bounds(i + k - int(filter_size / 2), j + l - int(filter_size / 2), img_size):
                        pixel_vals.append(img[i + k - int(filter_size / 2)][j + l - int(filter_size / 2)])
            pixel_vals.sort()
            new_img[i][j] = pixel_vals[int(len(pixel_vals) / 2)]
    return new_img

def prewitt_filter_kernel(orientation):
    if orientation == 'H':
        return [
            [1, 1, 1],
            [0, 0, 0],
            [-1, -1, -1]
        ]
    if orientation == 'V':
        return [
            [1, 0, -1],
            [1, 0, -1],
            [1, 0, -1]
        ]

def laplacian_filter_kernel():
    return [
        [-1, -1, -1],
        [-1, 8, -1],
        [-1, -1, -1]
    ]

def sobel_filter_kernel(orientation):
    if orientation == 'H':
        return [
            [1, 2, 1],
            [0, 0, 0],
            [-1, -2, -1]
        ]
    if orientation == 'V':
        return [
            [1, 0, -1],
            [2, 0, -2],
            [1, 0, -1]
        ]
    if orientation == 'D1':
        return [
            [0, 1, 2],
            [-1, 0, 1],
            [-2, -1, 0]
        ]
    if orientation == 'D2':
        return [
            [2, 1, 0],
            [1, 0, -1],
            [0, -1, -2]
        ]

def gaussian_filter_kernel(n, sigma):
    var = sigma * sigma
    kernel = [[0 for _ in range(n)] for __ in range(n)]
    for i in range(n):
        for j in range(n):
            x = i - int(n / 2)
            y = j - int(n / 2)
            kernel[i][j] = 1 / (2 * np.pi * var) * np.exp(-(x * x + y * y) / (2 * var))
    return kernel

def convolve(img, kernel, no_int=False):
    kernel_size = len(kernel)
    img_size = len(img)
    new_img = [[0 for _ in range(img_size)] for __ in range(img_size)]

    for i in range(img_size):
        for j in range(img_size):
            num, den = 0, 0
            for k in range(kernel_size):
                for l in range(kernel_size):
                    if within_bounds(i - k + int(kernel_size / 2), j - l + int(kernel_size / 2), img_size):
                        num += kernel[k][l] * img[i - k + int(kernel_size / 2)][j - l + int(kernel_size / 2)] # elementwise multiply with flipped kernel
                        den += kernel[k][l]
            if den == 0:
                new_img[i][j] = num
            else:
                if no_int:
                    new_img[i][j] = num / den
                else:
                    new_img[i][j] = int(num / den)
    return new_img

```


```python
# Function to apply image enhancement based on user selections
def apply_image_enhancement(folder_path, image_filename, filter_type, kernel_size):
    folder_path = folder_path.strip()
    image_filename = image_filename.strip()

    if not folder_path or not os.path.exists(folder_path):
        return "Invalid folder path."

    if not image_filename:
        return "Please select an image."

    image_path = os.path.join(folder_path, image_filename)
    if not os.path.exists(image_path):
        return "Image not found in the specified folder."

    image = cv2.imread(image_path)
    image = list(image[:, :, 0])

    if filter_type == 'Mean Filter':
        kernel = mean_filter_kernel(kernel_size)
    elif filter_type == 'Median Filter':
        new_img = median_filter(image, kernel_size)
        new_img = np.array(new_img)
        cv2.imwrite("output.jpg", new_img)
        return "Median filter applied and saved as 'output.jpg'."
    elif filter_type == 'Prewitt Filter':
        kernel = prewitt_filter_kernel('H')
    elif filter_type == 'Laplacian Filter':
        kernel = laplacian_filter_kernel()
    elif filter_type == 'Sobel Filter':
        kernel = sobel_filter_kernel('H')
    elif filter_type == 'Gaussian Filter':
        kernel = gaussian_filter_kernel(3, 1)

    new_img = convolve(image, kernel)
    new_img = np.array(new_img)
    cv2.imwrite("output.jpg", new_img)
    return "Selected filter applied and saved as 'output.jpg'."

# Create input widgets
operation=widgets.Dropdown(options=["filters", "gaussian unblur"], description="operation Selection")
folder_path_input = widgets.Text(placeholder="Enter folder path")
image_dropdown = widgets.Dropdown(options=[], description="Select Image")
filter_dropdown = widgets.Dropdown(options=["Mean Filter", "Median Filter", "Prewitt Filter", "Laplacian Filter", "Sobel Filter", "Gaussian Filter"], description="Filter Selection")
kernel_dropdown = widgets.Dropdown(options=[3, 5, 7, 9], description="Kernel Size")
enhance_button = widgets.Button(description="Enhance Image")
result_output = widgets.Output()

# Event handling
def folder_path_changed(change):
    folder_path = change.new
    if folder_path and os.path.isdir(folder_path):
        image_files = [file for file in os.listdir(folder_path) if file.lower().endswith(('.jpg', '.jpeg', '.png', '.bmp'))]
        image_dropdown.options = image_files
    else:
        image_dropdown.options = []

folder_path_input.observe(folder_path_changed, names='value')

def enhance_button_clicked(b):
    result_output.clear_output()
    folder_path = folder_path_input.value
    image_filename = image_dropdown.value
    filter_type = filter_dropdown.value
    kernel_size = kernel_dropdown.value
    with result_output:
        message = apply_image_enhancement(folder_path, image_filename, filter_type, kernel_size)
        print(message)

enhance_button.on_click(enhance_button_clicked)

# Display widgets

print("filter operation")
display(folder_path_input)
display(image_dropdown)
display(filter_dropdown)
display(kernel_dropdown)
display(enhance_button)
display(result_output)
```


When prompted, select the operation "filters."

Enter the folder path containing your images.

Choose an image from the dropdown menu.

Select a filter type (e.g., "Mean Filter," "Median Filter").

Choose the kernel size (e.g., 3x3, 5x5) based on the selected filter.

Click the "Enhance Image" button to apply the chosen filter to the selected image.

The enhanced image will be displayed and saved as "output.jpg."

Gaussian Unblur
Execute the following code cell:

```python
# Create input widgets
print("gaussian unblur")

def gaussian_unblur(img):
    kernel = gaussian_filter_kernel(3, 1)
    img_size = len(img)
    I_k = [[0 for _ in range(img_size)] for __ in range(img_size)]
    for i in range(img_size):
        for j in range(img_size):
            I_k[i][j] = img[i][j]
    diff, eps = float('inf'), 1e-1
    iter = 0
    while diff > eps:
        iter += 1
        print("Iteration:", iter)
        A = convolve(I_k, kernel, True)
        B = [[0 for _ in range(img_size)] for __ in range(img_size)]
        for i in range(img_size):
            for j in range(img_size):
                B[i][j] = img[i][j] / (A[i][j] + 1e-30)

        C = convolve(B, kernel, True)
        I_nxt = [[0 for _ in range(img_size)] for __ in range(img_size)]
        cur_diff = 0
        for i in range(img_size):
            for j in range(img_size):
                I_nxt[i][j] = I_k[i][j] * C[i][j]
                cur_diff += abs(I_nxt[i][j] - I_k[i][j])
        cur_diff /= (img_size * img_size)
        for i in range(img_size):
            for j in range(img_size):
                I_k[i][j] = I_nxt[i][j]
        diff = cur_diff
        print("Mean absolute change:", diff)

    # Convert I_k to a NumPy array
    final_unblurred_image = np.array(I_k, dtype=np.uint8)
    return final_unblurred_image

# Input handling
filepath = input("Enter path of file to be unblurred: ")
img = cv2.imread(filepath, cv2.IMREAD_GRAYSCALE)
final_output_image = gaussian_unblur(img)

# Save the final unblurred image
output_filename = "unblur/output_final.jpg"
cv2.imwrite(output_filename, final_output_image)

print("Final unblurred image saved as:", output_filename)
```

Enter the path of the blurred image you want to unblur when prompted.

The code will apply the Gaussian unblurring algorithm to the image and save the final unblurred image as "unblur/output_final.jpg."

Code Explanation

The code provides an interactive interface for selecting filter operations and enhancing images.
Filters like Mean Filter, Median Filter, Prewitt Filter, Laplacian Filter, Sobel Filter, and Gaussian Filter can be applied to images.
For the Gaussian unblurring operation, the code applies an iterative deconvolution algorithm to restore a blurred image.
