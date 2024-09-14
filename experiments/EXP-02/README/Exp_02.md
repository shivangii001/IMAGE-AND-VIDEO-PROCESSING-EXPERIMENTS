```python
import numpy as np
import matplotlib.pyplot as plt
import math
import struct

def  bytes_to_int(x) :
  if len(x) == 1 :
    return (x[0])
  elif len(x) == 2 :
    return (x[1] << 8) | (x[0])
  elif len(x) == 3 :
    return (x[2] << 16) | (x[1] << 8) | (x[0])
  else :
    return (x[3] << 24) | (x[2] << 16) | (x[1] << 8) | (x[0])

def read_image(filename):
    if filename[-4:] != '.bmp':
        print("ERROR:Given file is not .bmp file")
        return

    file_data=open(filename,'rb')
    header_info=dict()

    #read the bmp header
    header_info['type'] = file_data.read(2).decode()
    if header_info['type'] != 'BM':
        print("Given file is not .bmp file")
        return


    header_info['imagesize_in_bytes'] = bytes_to_int(file_data.read(4))
    header_info['reserved_1'] = bytes_to_int(file_data.read(2))
    header_info['reserved_2'] = bytes_to_int(file_data.read(2))
    header_info['offset'] = bytes_to_int(file_data.read(4))
    header_info['dib_header_size'] = bytes_to_int(file_data.read(4))
    header_info['width_pixels'] = bytes_to_int(file_data.read(4))
    header_info['height_pixels'] = bytes_to_int(file_data.read(4))
    header_info['colour_planes'] = bytes_to_int(file_data.read(2))
    header_info['bit_width'] = bytes_to_int(file_data.read(2))
    header_info['compression_method'] = bytes_to_int(file_data.read(4))
    header_info['raw_image_size_bytes'] = bytes_to_int(file_data.read(4))
    header_info['horizontal_resolution'] = bytes_to_int(file_data.read(4))
    header_info['vertical_resolution'] = bytes_to_int(file_data.read(4))
    header_info['num_of_colors'] = bytes_to_int(file_data.read(4))
    header_info['important_colors'] = bytes_to_int(file_data.read(4))

    file_data.seek(header_info['offset'])

    pixel_array = [[[0 for c in range(3)] for w in range(header_info['width_pixels'])] for h in range(header_info['height_pixels'])]


    for i in range(len(pixel_array)):
        for j in range(len(pixel_array[0])):
            if header_info['num_of_colors'] != 0:
                pixel_offset = header_info['offset'] + i* len(pixel_array[0]) + j
                file_data.seek(pixel_offset)
                color_index =   int.from_bytes(file_data.read(1),byteorder='big')
                color_offset = 54 + color_index * 4
                file_data.seek(color_offset)
            for c in range(3):
                pixel_value = bytes_to_int(file_data.read(1))
                pixel_array[len(pixel_array)-1-i][j][2 - c] = pixel_value

    file_data.close()
    return header_info, pixel_array

def scale_image_by_BilinearInterpolation(pixel_array, Sx, Sy):
    height, width, channels = pixel_array.shape
    scaled_height = int(height * Sx)
    scaled_width = int(width * Sy)

    scaled_pixel_array = np.zeros((scaled_height, scaled_width, channels), dtype=np.uint8)

    for i in range(scaled_height):
        for j in range(scaled_width):
            m = i / Sx
            n = j / Sy

            m1, n1 = int(np.floor(m)), int(np.floor(n))
            m2, n2 = min(m1 + 1, height - 1), min(n1 + 1, width - 1)

            frac_m, frac_n = m - m1, n - n1

            interpolated_pixel = (1 - frac_m) * ((1 - frac_n) * pixel_array[m1, n1] + frac_n * pixel_array[m1, n2]) + frac_m * ((1 - frac_n) * pixel_array[m2, n1] + frac_n * pixel_array[m2, n2])

            scaled_pixel_array[i, j] = interpolated_pixel.astype(np.uint8)

    return scaled_pixel_array

def scale_image_by_NearestNeighbour(pixel_array,Sx,Sy):
    height, width, channels = pixel_array.shape
    scaled_height = int(height *Sx)
    scaled_width = int(width *Sy)

    scaled_pixel_array = np.zeros((scaled_height, scaled_width, channels), dtype=np.uint8)



    for i in range(scaled_height):
      for j in range(scaled_width):
          m = round(i / Sx)
          n = round(j / Sy)


          m = np.clip(m, 0, height - 1)
          n = np.clip(n, 0, width - 1)


          scaled_pixel_array[i, j, :] = pixel_array[m, n, :]

    return scaled_pixel_array

def rotate_image_nearest_neighbor(pixel_array, angle_deg):
    angle_rad = math.radians(angle_deg)

    height, width, channels = pixel_array.shape
    new_width = int(abs(width * math.cos(angle_rad)) + abs(height * math.sin(angle_rad)))
    new_height = int(abs(width * math.sin(angle_rad)) + abs(height * math.cos(angle_rad)))

    rotated_pixel_array = np.zeros((new_height, new_width, channels), dtype=np.uint8)

    center_x = width / 2
    center_y = height / 2

    for i in range(new_height):
        for j in range(new_width):
            x = int((j - new_width / 2) * math.cos(angle_rad) - (i - new_height / 2) * math.sin(angle_rad) + center_x)
            y = int((j - new_width / 2) * math.sin(angle_rad) + (i - new_height / 2) * math.cos(angle_rad) + center_y)

            if 0 <= x < width and 0 <= y < height:
                rotated_pixel_array[i, j, :] = pixel_array[y, x, :]

    return rotated_pixel_array

def rotate_image_bilinear(pixel_array, angle_deg):
    angle_rad = math.radians(angle_deg)

    height, width, channels = pixel_array.shape
    new_width = int(abs(width * math.cos(angle_rad)) + abs(height * math.sin(angle_rad)))
    new_height = int(abs(width * math.sin(angle_rad)) + abs(height * math.cos(angle_rad)))

    rotated_pixel_array = np.zeros((new_height, new_width, channels), dtype=np.uint8)

    center_x = width / 2
    center_y = height / 2

    # Determine the background color before rotation
    background_color = np.mean(pixel_array, axis=(0, 1)).astype(np.uint8)
    rotated_pixel_array[:, :] = background_color

    for i in range(new_height):
        for j in range(new_width):
            x = (j - new_width / 2) * math.cos(angle_rad) - (i - new_height / 2) * math.sin(angle_rad) + center_x
            y = (j - new_width / 2) * math.sin(angle_rad) + (i - new_height / 2) * math.cos(angle_rad) + center_y

            x = np.clip(x, 0, width - 1)  # Clip to ensure within image bounds
            y = np.clip(y, 0, height - 1)

            p1x = int(math.floor(x))
            p2x = min(int(math.ceil(x)), width - 1)
            p1y = int(math.floor(y))
            p2y = min(int(math.ceil(y)), height - 1)

            q11 = pixel_array[p1y, p1x]
            q12 = pixel_array[p1y, p2x]
            q21 = pixel_array[p2y, p1x]
            q22 = pixel_array[p2y, p2x]

            alpha = x - p1x
            beta = y - p1y

            interpolated_pixel = (1 - alpha) * (1 - beta) * q11 + alpha * (1 - beta) * q12 + (1 - alpha) * beta * q21 + alpha * beta * q22

            rotated_pixel_array[i, j, :] = interpolated_pixel

    return rotated_pixel_array

if __name__ == "__main__":
    filename = input("Enter path of the file : ")
    header_info, pixel_array = read_image(filename)

    Sx = float(input("Enter the value of scaling factor for height: "))
    Sy = float(input("Enter the value of scaling factor for width: "))
    theta = float(input("Enter the angle by which you want to rotate: "))

    pixel_array = np.array(pixel_array)

    scaled_pixel_array_nn = scale_image_by_NearestNeighbour(pixel_array, Sx, Sy)
    output_filename="scaled_NN.bmp"
    plt.imsave(output_filename,scaled_pixel_array_nn)

    scaled_pixel_array_bilinear = scale_image_by_BilinearInterpolation(pixel_array, Sx, Sy)
    output_filename="scaled_BL.bmp"
    plt.imsave(output_filename,scaled_pixel_array_bilinear)

    rotated_pixel_array_nn = rotate_image_nearest_neighbor(pixel_array, theta)
    output_filename="rotated_NN.bmp"
    plt.imsave(output_filename,rotated_pixel_array_nn)

    rotated_pixel_array_bilinear = rotate_image_bilinear(pixel_array, theta)
    output_filename="rotated_BL.bmp"
    plt.imsave(output_filename,rotated_pixel_array_bilinear)

    plt.figure(figsize=(15, 8))

    plt.subplot(2, 2, 1)
    plt.imshow(scaled_pixel_array_nn)
    plt.title(f"Scaled Image (Nearest Neighbor)\nSx: {Sx}, Sy: {Sy}")
    plt.axis('off')

    plt.subplot(2, 2, 2)
    plt.imshow(scaled_pixel_array_bilinear)
    plt.title(f"Scaled Image (Bilinear Interpolation)\nSx: {Sx}, Sy: {Sy}")
    plt.axis('off')

    plt.subplot(2, 2, 3)
    plt.imshow(rotated_pixel_array_nn)
    plt.title(f"Rotated Image (Nearest Neighbor)\nTheta: {theta} degrees")
    plt.axis('off')

    plt.subplot(2, 2, 4)
    plt.imshow(rotated_pixel_array_bilinear)
    plt.title(f"Rotated Image (Bilinear Interpolation)\nTheta: {theta} degrees")
    plt.axis('off')

    plt.tight_layout()
    plt.savefig("output_images.png")
    plt.show()
```

#Image Processing Toolkit for Google Colab

This Python script provides an image processing toolkit that includes functions for scaling and rotating images. The toolkit is designed to be used in Google Colab and allows you to perform scaling using both nearest neighbor and bilinear interpolation techniques. Additionally, you can rotate images by a specified angle using both nearest neighbor and bilinear interpolation methods.


#Features

Read BMP image files and extract header information and pixel data.
Scale images using nearest neighbor and bilinear interpolation techniques.
Rotate images by a specified angle using nearest neighbor and bilinear interpolation methods.
Display and save the processed images in BMP format.
Automatically calculate the rotated image size to accommodate the rotated content.

#Usage in Google Colab

Upload the image_processing_toolkit.py script to your Google Colab environment.
Run the script using a code cell in Google Colab.
When prompted, enter the path of the BMP image file you want to process.
Enter the scaling factors for height and width (for scaling operations).
Enter the rotation angle in degrees (for rotation operations).

The script will generate four processed images:

Scaled Image (Nearest Neighbor)
Scaled Image (Bilinear Interpolation)
Rotated Image (Nearest Neighbor)
Rotated Image (Bilinear Interpolation)

Additionally, the script will display a visualization of the processed images in a single figure and save the visualization as "output_images.png" in the Colab working directory.

#Requirements

No additional installation is required. Google Colab already includes the necessary packages: NumPy and Matplotlib.

#Note

The script assumes that the input image is in BMP format.
The output images will be saved in the Colab working directory.
This script is intended for educational purposes and may not cover all edge cases.

#Authors

Shivangi Shivmohan Singh (23EC65R14)
Pragati Maurya (23EC65R30)