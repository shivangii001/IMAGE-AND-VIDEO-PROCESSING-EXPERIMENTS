```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.image as mpimg
import cmath

def custom_fft(x):
    N = len(x)
    if N == 1:
        return x
    even = x[::2]
    odd = x[1::2]
    r_even = custom_fft(even)
    r_odd = custom_fft(odd)
    factor = np.exp(-2j * np.pi * np.arange(N) / N)
    return np.concatenate((r_even + factor[:N//2] * r_odd, r_even + factor[N//2:] * r_odd))

def custom_image_fft(image):
    fft_image = np.array([custom_fft(row) for row in image])
    fft_image = np.array([custom_fft(fft_image[:, i]) for i in range(image.shape[1])]).T
    return fft_image

def calculate_magnitude(fft_result):
    return np.abs(fft_result)

def apply_alternating_signs(image):
    image[1::2, 0::2] *= -1
    image[0::2, 1::2] *= -1
    return image

def reconstruct_image(fft_result, height, width):
    reconstructed_image = np.conjugate(np.transpose(fft_result))
    reconstructed_image = np.conjugate(np.transpose(custom_image_fft(reconstructed_image))) / (height * width)
    reconstructed_image = apply_alternating_signs(reconstructed_image)
    return reconstructed_image.real.astype('uint8')

def main():
    file_path = input('\nEnter the filename: ')
    i_image = np.array(mpimg.imread(file_path), dtype='float64')
    height, width = i_image.shape
    image_copy = i_image.copy()

    fig, ax = plt.subplots(2, 2, figsize=(12, 12))

    ax[1, 0].imshow(i_image, cmap='gray')
    ax[1, 0].set_title("Original Image")

    image_copy = apply_alternating_signs(image_copy)
    fft_image = custom_image_fft(image_copy)

    magnitude_spectrum = calculate_magnitude(fft_image)
    ax[0, 0].imshow(np.log(magnitude_spectrum + 1), cmap='gray')
    ax[0, 0].set_title("Log Magnitude Spectrum")

    ax[0, 1].imshow(np.vectorize(cmath.phase)(fft_image), cmap='gray')
    ax[0, 1].set_title("Phase Spectrum")

    reconstructed_image = reconstruct_image(fft_image, height, width)
    ax[1, 1].imshow(reconstructed_image, cmap="gray")
    ax[1, 1].set_title("Reconstructed Image")

    plt.show()

if __name__ == "__main__":
    main()


# Custom FFT Image Processing in Google Colab

This Python script demonstrates a custom implementation of the Fast Fourier Transform (FFT) for 2D image processing. You can run this script in Google Colab, a popular online Python environment.
    
## Usage in Google Colab

Follow these steps to use the code in Google Colab:

Open Google Colab: Open Google Colab in your web browser: Google Colab.

Create a New Notebook: Click on "File" in the menu bar, then select "New Notebook" to create a new Colab notebook.

Paste the Code: Copy and paste the code into a code cell in your Colab notebook

Save Your Colab Notebook: Save your Colab notebook by clicking "File" and then "Save" or by pressing Ctrl+S (or Cmd+S on Mac).

Upload an Image: Upload an image you want to process to Google Colab:

Click on the folder icon on the left sidebar.
Click the "Upload" button and select your image file.
Run the Code Cell: Run the code cell containing the script by clicking the "Play" button (▶️) or by pressing Shift+Enter.

Input the Image Filename: The script will prompt you to enter the filename of the uploaded image. Enter the filename and press Enter.

View Results: The script will display the following:

The original image
Log magnitude spectrum
Phase spectrum
Reconstructed image with alternating signs
Exit: You can close the image windows to exit.

## Custom FFT Implementation

The script contains a custom implementation of the 1D FFT algorithm and a 2D FFT for image processing. The code follows these steps:

Load the input image.
Apply alternating (-1)^(x+y) multiplication to every other pixel.
Compute the 2D FFT of the modified image using the custom FFT implementation.
Calculate the log magnitude spectrum and phase spectrum for visualization.
Reconstruct the image from the FFT result with proper normalization and alternating signs.

##Acknowledgments

This script is for educational purposes and demonstrates the principles of FFT and image processing. 


##Author

Shivangi Shivmohan Singh (23EC65R14)
Pragati Maurya (23EC65R30)