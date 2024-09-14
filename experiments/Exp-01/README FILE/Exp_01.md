#READ
import matplotlib.pyplot as plt
import matplotlib.image as mpimg

def read_bmp_header(file_path):
    with open(file_path, "rb") as file:
        # Read BMP file header (size: 54 bytes)

        header_data = file.read(54)

        # Extract header values
        signature = header_data[0:2]  # BMP signature (should be 'BM')
        if signature != b'BM':
            print("Error: Not a BMP image")
            return None

        file_size = int.from_bytes(header_data[2:6], byteorder="little")
        offset = int.from_bytes(header_data[10:14], byteorder="little")
        width = int.from_bytes(header_data[18:22], byteorder="little")
        height = int.from_bytes(header_data[22:26], byteorder="little")
        bit_depth = int.from_bytes(header_data[28:30], byteorder="little")
        color_planes = int.from_bytes(header_data[26:28], byteorder="little")
        dib_header_size = int.from_bytes(header_data[14:18], byteorder="little")
        num_colors = int.from_bytes(header_data[46:50], byteorder="little")

        # Calculate raw image size in bytes based on bit depth
        bytes_per_pixel = bit_depth // 8
        raw_image_size_bytes = width * height * bytes_per_pixel

        # Print header information
        print("BMP Signature:", signature.decode("utf-8"))
        print("File Size:", file_size, "bytes")
        print("Offset:", offset, "bytes")
        print("Width:", width, "pixels")
        print("Height:", height, "pixels")
        print("Bit Depth:", bit_depth, "bits per pixel")
        print("Color Planes:", color_planes)
        print("DIB Header Size:", dib_header_size, "bytes")
        print("Raw Image Size:", raw_image_size_bytes, "bytes")
        print("Number of Colors:", num_colors)

        return {
            "signature": signature.decode("utf-8"),
            "file_size": file_size,
            "offset": offset,
            "width": width,
            "height": height,
            "bit_depth": bit_depth,
            "color_planes": color_planes,
            "dib_header_size": dib_header_size,
            "raw_image_size_bytes": raw_image_size_bytes,
            "num_colors": num_colors
        }

def display_img(file_path):
    # Load the BMP image using matplotlib's imread function
    bmp_image = mpimg.imread(file_path)

    # Check if the image has a color palette
    if bmp_image.ndim == 2:
        # Grayscale image
        plt.imshow(bmp_image, cmap='gray', vmin=0, vmax=255)
    else:
        # RGB image
        plt.imshow(bmp_image)

    plt.axis('off')  # Turn off axis labels and ticks
    plt.show()


file_path = input("Enter path of file to be read (*.bmp): ")
header_info = read_bmp_header(file_path)
if header_info is not None:
    display_img(file_path)


# WRITE
with open(file_path,'rb') as file:
  image_data=file.read()
  offset = int.from_bytes(image_data[10:14], byteorder="little")
  pixel_data_offset=offset
  pixel_data_length=len(image_data)-pixel_data_offset
  pixel_data=image_data[pixel_data_offset:]

def write_bmp_img(file_path,image_data,pixel_data):
  bmp_image=image_data+pixel_data
  with open(file_path,'wb') as file:
    file.write(bmp_image)

output_file_path=input("Enter path of file with which you want to save (*.bmp): ")
write_bmp_img(output_file_path,image_data,pixel_data)
display_img(output_file_path)


# CHANNEL MANIPULATION
def color_channel_manipulation(image_data, file_name):
    offset = int.from_bytes(image_data[10:14], "little")
    bpp = int.from_bytes(image_data[28:30], "little")
    mylist = bytearray(image_data)

    if remove_channel not in ['b', 'g', 'r']:
        print("Error! Invalid choice.")
        return

    if bpp <= 8:
        print("Image is {} bits per pixel".format(bpp))
        if remove_channel == 'b':
            for i in range(54, offset, 4):
                mylist[i] = 0
        elif remove_channel == 'g':
            for i in range(54, offset, 4):
                mylist[i + 1] = 0
        elif remove_channel == 'r':
            for i in range(54, offset, 4):
                mylist[i + 2] = 0
        else:
            print("Error! Invalid choice.")
    elif bpp == 24:
        print("Image is {} bits per pixel".format(bpp))
        if remove_channel == 'b':
            for i in range(offset, len(mylist), 3):
                mylist[i] = 0
        elif remove_channel == 'g':
            for i in range(offset, len(mylist), 3):
                mylist[i + 1] = 0
        elif remove_channel == 'r':
            for i in range(offset, len(mylist), 3):
                mylist[i + 2] = 0
        else:
            print("Error! Invalid choice.")
    else:
        print("Unsupported bpp: {}".format(bpp))
        return

    new_image_data = bytes(mylist)
    write_new_bmp(new_image_data, file_name)

def write_new_bmp(data, file_name):
    with open(file_name, 'wb') as new_file:
        new_file.write(data)


remove_channel = input("Enter the channel you wish to make zero (b, g, r): ").lower()

# Read the BMP header
file_path = "/content/corn.bmp"
file_name = "output_without_" + remove_channel + ".bmp"
header_info = read_bmp_header(file_path)

if header_info is not None:
    with open(file_path, 'rb') as file:
        # Read the entire image data
        image_data = file.read()

color_channel_manipulation(image_data, file_name)
display_img(file_name)



# BMP Image Channel Manipulation using Google Colab

This Python program allows you to manipulate the color channels of BMP images. You can choose to set one of the color channels (blue, green, or red) to zero, effectively removing that color channel from the image. The program provides the option to save the modified image as a new BMP file and includes functions for reading BMP headers and writing BMP images.

## Getting Started on Google Colab

1. Open Google Colab (https://colab.research.google.com/).

2. Create a new Colab notebook or upload an existing one.

3. Copy and paste the entire program code into a code cell in the Colab notebook.

4. Run the code cell by clicking the "Run" button or using the keyboard shortcut Ctrl + Enter.

5. Follow the on-screen instructions to select the color channel you want to remove and provide the output file name.

6. The program will create a modified BMP image with the chosen color channel removed and display both the original and modified images.

## Program Workflow

1. The program reads the BMP header of the input image to extract essential information like dimensions, bit depth, and color channels.

2. It then provides you with the option to select the color channel you want to remove (blue, green, or red).

3. The program processes the image data based on your choice and creates a modified image.

4. You can specify the output file name for the modified image.

5. The program displays the original and modified images using the Matplotlib library.

## Reading BMP Headers

The function `read_bmp_header(file_path)` reads the BMP header of the input image and extracts information like dimensions, bit depth, color channels, and more.

## Writing BMP Images

The function `write_bmp_img(file_path, image_data, pixel_data)` combines the image header with the modified pixel data and writes the modified BMP image to a new file.

## Color Channel Manipulation

The function `color_channel_manipulation(image_data, file_name)` performs the color channel manipulation based on your choice. It supports both 8 bits per pixel and 24 bits per pixel BMP images. You can choose to remove the blue, green, or red channel from the image.

## Requirements

- Google Colab (https://colab.research.google.com/)
- Matplotlib library (already available in Colab)

## Usage Example

1. Upload your BMP image to the Colab environment.
2. Specify the input file path in the program code.
3. Specify the output file name for the modified image.
4. Run the program code and follow the on-screen instructions to remove a color channel.

## Note

- The program currently supports BMP images with 8 bits per pixel and 24 bits per pixel.
- The output image file will be named according to the chosen channel, e.g., `output_without_r.bmp` for the red channel.

## Author

Shivangi Shivmohan Singh 
Pragati Maurya

    

