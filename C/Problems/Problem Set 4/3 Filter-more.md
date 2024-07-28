Question
Perhaps the simplest way to represent an image is with a grid of pixels (i.e., dots), each of which can be of a different color. For black-and-white images, we thus need 1 bit per pixel, as 0 could represent black and 1 could represent white, as in the below.

![](https://i.imgur.com/AEaH5nA.png)

In this sense, then, is an image just a bitmap (i.e., a map of bits). For more colorful images, you simply need more bits per pixel. A file format (like [BMP](https://en.wikipedia.org/wiki/BMP_file_format), [JPEG](https://en.wikipedia.org/wiki/JPEG), or [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)) that supports “24-bit color” uses 24 bits per pixel. (BMP actually supports 1-, 4-, 8-, 16-, 24-, and 32-bit color.)

A 24-bit BMP uses 8 bits to signify the amount of red in a pixel’s color, 8 bits to signify the amount of green in a pixel’s color, and 8 bits to signify the amount of blue in a pixel’s color. If you’ve ever heard of RGB color, well, there you have it: red, green, blue.

If the R, G, and B values of some pixel in a BMP are, say, `0xff`, `0x00`, and `0x00` in hexadecimal, that pixel is purely red, as `0xff` (otherwise known as `255` in decimal) implies “a lot of red,” while `0x00` and `0x00` imply “no green” and “no blue,” respectively. In this problem, you’ll manipulate these R, G, and B values of individual pixels, ultimately creating your very own image filters.

# Assumed Output

```
$ make filter

$ ./filter -g ./images/courtyard.bmp ./images/courtyard-grayscale.bmp

$ ./filter -e ./images/stadium.bmp ./images/stadium-edges.bmp

$ ./filter -r ./images/tower.bmp ./images/tower-reflected.bmp

$ ./filter -b ./images/yard.bmp ./images/yard-blurred.bmp

$ ./filter
Usage: ./filter [flag] infile outfile
```

# Background

Recall that a file is just a sequence of bits, arranged in some fashion. A 24-bit BMP file, then, is essentially just a sequence of bits, (almost) every 24 of which happen to represent some pixel’s color. But a BMP file also contains some “metadata,” information like an image’s height and width. That metadata is stored at the beginning of the file in the form of two data structures generally referred to as “headers,” not to be confused with C’s header files. (Incidentally, these headers have evolved over time. This problem uses the latest version of Microsoft’s BMP format, 4.0, which debuted with Windows 95.)

The first of these headers, called `BITMAPFILEHEADER`, is 14 bytes long. (Recall that 1 byte equals 8 bits.) The second of these headers, called `BITMAPINFOHEADER`, is 40 bytes long. Immediately following these headers is the actual bitmap: an array of bytes, triples of which represent a pixel’s color. However, BMP stores these triples backwards (i.e., as BGR), with 8 bits for blue, followed by 8 bits for green, followed by 8 bits for red. (Some BMPs also store the entire bitmap backwards, with an image’s top row at the end of the BMP file. But we’ve stored this problem set’s BMPs as described herein, with each bitmap’s top row first and bottom row last.) In other words, were we to convert the 1-bit smiley above to a 24-bit smiley, substituting red for black, a 24-bit BMP would store this bitmap as follows, where `0000ff` signifies red and `ffffff` signifies white; we’ve highlighted in red all instances of `0000ff`.

To be clear, recall that a hexadecimal digit represents 4 bits. Accordingly, `ffffff` in hexadecimal actually signifies `111111111111111111111111` in binary.

Notice that you could represent a bitmap as a 2-dimensional array of pixels: where the image is an array of rows, each row is an array of pixels. Indeed, that’s how we’ve chosen to represent bitmap images in this problem.

##### Image Filtering

What does it even mean to filter an image? You can think of filtering an image as taking the pixels of some original image, and modifying each pixel in such a way that a particular effect is apparent in the resulting image.

# How this works ?

[Click here to go to YouTube](https://youtu.be/S1ceKJZfAjI)

# Solution

### helpers.c

Here’s where the implementation of the functions declared in `helpers.h` belong

```c
#include "helpers.h"
#include <math.h>

// Convert image to grayscale
void grayscale(int height, int width, RGBTRIPLE image[height][width])
{
    for (int h = 0; h < height; h++)
    {
        for (int w = 0; w < width; w++)
        {
            float blue = image[h][w].rgbtBlue;
            float green = image[h][w].rgbtGreen;
            float red = image[h][w].rgbtRed;

            float greyed = (blue + green + red) / 3.0;

            image[h][w].rgbtBlue = round(greyed);
            image[h][w].rgbtGreen = round(greyed);
            image[h][w].rgbtRed = round(greyed);
        }
    }
    return;
}

// Reflect image horizontally
void reflect(int height, int width, RGBTRIPLE image[height][width])
{
    for (int h = 0; h < height; h++)
    {
        for (int w = 0; w < width; w++)
        {
            if (w < (width / 2))
            {
                RGBTRIPLE temp = image[h][w];
                image[h][w] = image[h][width - w - 1];
                image[h][width - w - 1] = temp;
            }
        }
    }
    return;
}

// Blur image
void blur(int height, int width, RGBTRIPLE image[height][width])
{
    // creating a copy of the image
    RGBTRIPLE image_copy[height][width];

    // copying the image pixel by pixel
    for (int h = 0; h < height; h++)
    {
        for (int w = 0; w < width; w++)
        {
            image_copy[h][w] = image[h][w];
        }
    }

    for (int h = 0; h < height; h++)
    {
        for (int w = 0; w < width; w++)
        {

            float blue = 0;
            float red = 0;
            float green = 0;
            float count = 0;

            for (int i = -1; i < 2; i++)
            {
                for (int j = -1; j < 2; j++)
                {
                    if (((h - i) >= 0) && ((w - j) >= 0) && (((h - i) < height) && (w - j) < width))
                    {
                        blue += image_copy[h - i][w - j].rgbtBlue;
                        green += image_copy[h - i][w - j].rgbtGreen;
                        red += image_copy[h - i][w - j].rgbtRed;
                        count++;
                    }
                }
            }

            image[h][w].rgbtBlue = round(blue / count);
            image[h][w].rgbtGreen = round(green / count);
            image[h][w].rgbtRed = round(red / count);
        }
    }

    return;
}

// Detect edges
//Explanation of sobel operator is provided in the video attached
void edges(int height, int width, RGBTRIPLE image[height][width])
{
    // creating a copy of the image
    RGBTRIPLE image_copy[height][width];
    for (int h = 0; h < height; h++)
    {
        for (int w = 0; w < width; w++)
        {
            image_copy[h][w] = image[h][w];
        }
    }

    // going at each and every pixel using dual loop
    for (int h = 0; h < height; h++)
    {
        for (int w = 0; w < width; w++)
        {
            //the variables are made here as per the requirements of the sobel operator
            float red_gx = 0;
            float green_gx = 0;
            float blue_gx = 0;

            // computing Gx

            /*
            The kernel is like this

            -1  0  1
            -2  0  2
            -1  0  1

            On the basis the this kernal, the values have to be divided

            The conditionals statement are made on the basis of this kernal

            -----------------------------

            h-1, w-1    h-1, w    h-1, w+1

              h, w-1      h, w      h, w+1

            h+1, w-1    h+1, w    h+1, w+1

            -----------------------------
            */

           //these loops are made here to access the pixels in the way kernal required
           //the kernal is given above and the ways of accessing the pixel is also given above

           //the i is being used to manipulate the values of h
           //the j is being used to manipulate the values of w
            for (int i = -1; i < 2; i++)
            {
                for (int j = -1; j < 2; j++)
                {
                    if ((((h + i) >= 0) && ((w + j) >= 0)) && (((h + i) < height) && (w + j) < width))
                    {
                        // for centre row, where multiplication is by 0
                        if (j == 0)
                        {
                            red_gx += (image_copy[h + i][w + j].rgbtRed * 0);
                            green_gx += (image_copy[h + i][w + j].rgbtGreen * 0);
                            blue_gx += (image_copy[h + i][w + j].rgbtBlue * 0);
                        }

                        // where multiplication is by -1
                        else if (((i == -1) && (j == -1)) || ((i == 1) && (j == -1)))
                        {
                            red_gx += (image_copy[h + i][w + j].rgbtRed * -1);
                            green_gx += (image_copy[h + i][w + j].rgbtGreen * -1);
                            blue_gx += (image_copy[h + i][w + j].rgbtBlue * -1);
                        }

                        // where multiplication is by 1
                        else if (((i == -1) && (j == 1)) || ((i == 1) && (j == 1)))
                        {
                            red_gx += (image_copy[h + i][w + j].rgbtRed * 1);
                            green_gx += (image_copy[h + i][w + j].rgbtGreen * 1);
                            blue_gx += (image_copy[h + i][w + j].rgbtBlue * 1);
                        }

                        // where multiplication is by -2
                        else if ((i == 0) && (j == -1))
                        {
                            red_gx += (image_copy[h + i][w + j].rgbtRed * -2);
                            green_gx += (image_copy[h + i][w + j].rgbtGreen * -2);
                            blue_gx += (image_copy[h + i][w + j].rgbtBlue * -2);
                        }

                        // where multiplication is by 2
                        else if ((i == 0) && (j == 1))
                        {
                            red_gx += (image_copy[h + i][w + j].rgbtRed * 2);
                            green_gx += (image_copy[h + i][w + j].rgbtGreen * 2);
                            blue_gx += (image_copy[h + i][w + j].rgbtBlue * 2);
                        }
                    }
                    // for the cornet cells, it is told to assume that there is a padding of black pixels
                    // black do not any value hence 0 and multiple any number with 0 will give 0 hence 0 is hard-coded here
                    else
                    {
                        red_gx += 0;
                        green_gx += 0;
                        blue_gx += 0;
                    }
                }
            }

            float red_gy = 0;
            float green_gy = 0;
            float blue_gy = 0;

            // computing Gy

            /*
            The kernel is like this

            -1  -2  -1
             0   0   0
             1   2   1

            On the basis the this kernal, the values have to be divided

            The conditionals statement are made on the basis of this kernal

            -----------------------------

            h-1, w-1    h-1, w    h-1, w+1

              h, w-1      h, w      h, w+1

            h+1, w-1    h+1, w    h+1, w+1

            -----------------------------
            */
            for (int i = -1; i < 2; i++)
            {
                for (int j = -1; j < 2; j++)
                {
                    if ((((h + i) >= 0) && ((w + j) >= 0)) && (((h + i) < height) && (w + j) < width))
                    {
                        // for centre row, where multiplication is by 0
                        if (i == 0)
                        {
                            red_gy += (image_copy[h + i][w + j].rgbtRed * 0);
                            green_gy += (image_copy[h + i][w + j].rgbtGreen * 0);
                            blue_gy += (image_copy[h + i][w + j].rgbtBlue * 0);
                        }

                        // where multiplication is by -1
                        else if (((i == -1) && (j == -1)) || ((i == -1) && (j == 1)))
                        {
                            red_gy += (image_copy[h + i][w + j].rgbtRed * -1);
                            green_gy += (image_copy[h + i][w + j].rgbtGreen * -1);
                            blue_gy += (image_copy[h + i][w + j].rgbtBlue * -1);
                        }

                        // where multiplication is by 1
                        else if (((i == 1) && (j == -1)) || ((i == 1) && (j == 1)))
                        {
                            red_gy += (image_copy[h + i][w + j].rgbtRed * 1);
                            green_gy += (image_copy[h + i][w + j].rgbtGreen * 1);
                            blue_gy += (image_copy[h + i][w + j].rgbtBlue * 1);
                        }

                        // where multiplication is by -2
                        else if ((i == -1) && (j == 0))
                        {
                            red_gy += (image_copy[h + i][w + j].rgbtRed * -2);
                            green_gy += (image_copy[h + i][w + j].rgbtGreen * -2);
                            blue_gy += (image_copy[h + i][w + j].rgbtBlue * -2);
                        }

                        // where multiplication is by 2
                        else if ((i == 1) && (j == 0))
                        {
                            red_gy += (image_copy[h + i][w + j].rgbtRed * 2);
                            green_gy += (image_copy[h + i][w + j].rgbtGreen * 2);
                            blue_gy += (image_copy[h + i][w + j].rgbtBlue * 2);
                        }
                    }
                    // for the cornet cells, it is told to assume that there is a padding of black pixels
                    // black do not any value hence 0 and multiple any number with 0 will give 0 hence 0 is hard-coded here
                    else
                    {
                        red_gy += 0;
                        green_gy += 0;
                        blue_gy += 0;
                    }
                }
            }

            // assigning the values of square root of gx^2 + gy^2 of each color
            float calculated_red = sqrt((red_gx * red_gx) + (red_gy * red_gy));
            float calculated_green = sqrt((green_gx * green_gx) + (green_gy * green_gy));
            float calculated_blue = sqrt((blue_gx * blue_gx) + (blue_gy * blue_gy));

            //the above lines can be merged with this one easily but this is done here so that it will be easy to understand the flow of working of edge detection algorithm
            int gradient_red = round(calculated_red);
            int gradient_green = round(calculated_green);
            int gradient_blue = round(calculated_blue);

            //capping the values because in RGB, the maximum value of a color can be 255
            if (gradient_red > 255)
            {
                gradient_red = 255;
            }

            if (gradient_blue > 255)
            {
                gradient_blue = 255;
            }

            if (gradient_green > 255)
            {
                gradient_green = 255;
            }

            //assigning the values to the original image's pixel
            image[h][w].rgbtRed = gradient_red;
            image[h][w].rgbtGreen = gradient_green;
            image[h][w].rgbtBlue = gradient_blue;
        }
    }

    return;
}

```

# Algorithms Used

### For Greyscale

- Recall that if the red, green, and blue values are all set to `0x00` (hexadecimal for `0`), then the pixel is black. And if all values are set to `0xff` (hexadecimal for `255`), then the pixel is white. So long as the red, green, and blue values are all equal, the result will be varying shades of gray along the black-white spectrum, with higher values meaning lighter shades (closer to white) and lower values meaning darker shades (closer to black).
- So to convert a pixel to grayscale, you just need to make sure the red, green, and blue values are all the same value. But how do you know what value to make them? Well, it’s probably reasonable to expect that if the original red, green, and blue values were all pretty high, then the new value should also be pretty high. And if the original values were all low, then the new value should also be low.
- In fact, to ensure each pixel of the new image still has the same general brightness or darkness as the old image, you can take the **average** of the red, green, and blue values to determine what shade of grey to make the new pixel.

### For Reflect

- Any pixels on the left side of the image should end up on the right, and vice versa.
- Note that all of the original pixels of the original image will still be present in the reflected image, it’s just that those pixels may have been rearranged to be in a different place in the image.

### For Blur

- Consider the following grid of pixels, where we’ve numbered each pixel.
- ![](https://i.imgur.com/7sxqmqc.png)
- The new value of each pixel would be the average of the values of all of the pixels that are within 1 row and column of the original pixel (forming a 3x3 box). For example, each of the color values for pixel 6 would be obtained by averaging the original color values of pixels 1, 2, 3, 5, 6, 7, 9, 10, and 11 (note that pixel 6 itself is included in the average). Likewise, the color values for pixel 11 would be be obtained by averaging the color values of pixels 6, 7, 8, 10, 11, 12, 14, 15 and 16.
- For a pixel along the edge or corner, like pixel 15, we would still look for all pixels within 1 row and column: in this case, pixels 10, 11, 12, 14, 15, and 16.

### For Edges

Prefer to watch the video given above and writing and reading the algorithm is going to be headache and time consuming for me and you so watch the video linked above.

# Additional Required Files

### helpers.h

Take a look at `helpers.h`. This file is quite short, and just provides the function prototypes for the functions you saw earlier.

Here, take note of the fact that each function takes a 2D array called `image` as an argument, where `image` is an array of `height` many rows, and each row is itself another array of `width` many `RGBTRIPLE`s. So if `image` represents the whole picture, then `image[0]` represents the first row, and `image[0][0]` represents the pixel in the upper-left corner of the image.

```c
#include "bmp.h"

// Convert image to grayscale
void grayscale(int height, int width, RGBTRIPLE image[height][width]);

// Convert image to sepia
void edges(int height, int width, RGBTRIPLE image[height][width]);

// Reflect image horizontally
void reflect(int height, int width, RGBTRIPLE image[height][width]);

// Blur image
void blur(int height, int width, RGBTRIPLE image[height][width]);

```

### bmp.h

You’ll see definitions of the headers we’ve mentioned (`BITMAPINFOHEADER` and `BITMAPFILEHEADER`). In addition, that file defines `BYTE`, `DWORD`, `LONG`, and `WORD`, data types normally found in the world of Windows programming. Notice how they’re just aliases for primitives with which you are (hopefully) already familiar. It appears that `BITMAPFILEHEADER` and `BITMAPINFOHEADER` make use of these types.

Perhaps most importantly for you, this file also defines a `struct` called `RGBTRIPLE` that, quite simply, “encapsulates” three bytes: one blue, one green, and one red (the order, recall, in which we expect to find RGB triples actually on disk).

Why are these `struct`s useful? Well, recall that a file is just a sequence of bytes (or, ultimately, bits) on disk. But those bytes are generally ordered in such a way that the first few represent something, the next few represent something else, and so on. “File formats” exist because the world has standardized what bytes mean what. Now, we could just read a file from disk into RAM as one big array of bytes. And we could just remember that the byte at `array[i]` represents one thing, while the byte at `array[j]` represents another. But why not give some of those bytes names so that we can retrieve them from memory more easily? That’s precisely what the structs in `bmp.h` allow us to do. Rather than think of some file as one long sequence of bytes, we can instead think of it as a sequence of `struct`s.

```c
// BMP-related data types based on Microsoft's own

#include <stdint.h>

// These data types are essentially aliases for C/C++ primitive data types.
// Adapted from http://msdn.microsoft.com/en-us/library/cc230309.aspx.
// See https://en.wikipedia.org/wiki/C_data_types#stdint.h for more on stdint.h.

typedef uint8_t  BYTE;
typedef uint32_t DWORD;
typedef int32_t  LONG;
typedef uint16_t WORD;

// The BITMAPFILEHEADER structure contains information about the type, size,
// and layout of a file that contains a DIB [device-independent bitmap].
// Adapted from http://msdn.microsoft.com/en-us/library/dd183374(VS.85).aspx.

typedef struct
{
    WORD   bfType;
    DWORD  bfSize;
    WORD   bfReserved1;
    WORD   bfReserved2;
    DWORD  bfOffBits;
} __attribute__((__packed__))
BITMAPFILEHEADER;

// The BITMAPINFOHEADER structure contains information about the
// dimensions and color format of a DIB [device-independent bitmap].
// Adapted from http://msdn.microsoft.com/en-us/library/dd183376(VS.85).aspx.

typedef struct
{
    DWORD  biSize;
    LONG   biWidth;
    LONG   biHeight;
    WORD   biPlanes;
    WORD   biBitCount;
    DWORD  biCompression;
    DWORD  biSizeImage;
    LONG   biXPelsPerMeter;
    LONG   biYPelsPerMeter;
    DWORD  biClrUsed;
    DWORD  biClrImportant;
} __attribute__((__packed__))
BITMAPINFOHEADER;

// The RGBTRIPLE structure describes a color consisting of relative intensities of
// red, green, and blue. Adapted from http://msdn.microsoft.com/en-us/library/aa922590.aspx.

typedef struct
{
    BYTE  rgbtBlue;
    BYTE  rgbtGreen;
    BYTE  rgbtRed;
} __attribute__((__packed__))
RGBTRIPLE;

```

### filter.c

First, notice the definition of `filters` on line 10. That string tells the program what the allowable command-line arguments to the program are: `b`, `g`, `r`, and `s`. Each of them specifies a different filter that we might apply to our images: blur, grayscale, reflection, and sepia.

The next several lines open up an image file, make sure it’s indeed a BMP file, and read all of the pixel information into a 2D array called `image`.

Scroll down to the `switch` statement that begins on line 101. Notice that, depending on what `filter` we’ve chosen, a different function is called: if the user chooses filter `b`, the program calls the `blur` function; if `g`, then `grayscale` is called; if `r`, then `reflect` is called; and if `s`, then `sepia` is called. Notice, too, that each of these functions take as arguments the height of the image, the width of the image, and the 2D array of pixels.

These are the functions you’ll (soon!) implement. As you might imagine, the goal is for each of these functions to edit the 2D array of pixels in such a way that the desired filter is applied to the image.

The remaining lines of the program take the resulting `image` and write them out to a new image file.

```c
#include <getopt.h>
#include <stdio.h>
#include <stdlib.h>

#include "helpers.h"

int main(int argc, char *argv[])
{
    // Define allowable filters
    char *filters = "begr";

    // Get filter flag and check validity
    char filter = getopt(argc, argv, filters);
    if (filter == '?')
    {
        printf("Invalid filter.\n");
        return 1;
    }

    // Ensure only one filter
    if (getopt(argc, argv, filters) != -1)
    {
        printf("Only one filter allowed.\n");
        return 2;
    }

    // Ensure proper usage
    if (argc != optind + 2)
    {
        printf("Usage: ./filter [flag] infile outfile\n");
        return 3;
    }

    // Remember filenames
    char *infile = argv[optind];
    char *outfile = argv[optind + 1];

    // Open input file
    FILE *inptr = fopen(infile, "r");
    if (inptr == NULL)
    {
        printf("Could not open %s.\n", infile);
        return 4;
    }

    // Open output file
    FILE *outptr = fopen(outfile, "w");
    if (outptr == NULL)
    {
        fclose(inptr);
        printf("Could not create %s.\n", outfile);
        return 5;
    }

    // Read infile's BITMAPFILEHEADER
    BITMAPFILEHEADER bf;
    fread(&bf, sizeof(BITMAPFILEHEADER), 1, inptr);

    // Read infile's BITMAPINFOHEADER
    BITMAPINFOHEADER bi;
    fread(&bi, sizeof(BITMAPINFOHEADER), 1, inptr);

    // Ensure infile is (likely) a 24-bit uncompressed BMP 4.0
    if (bf.bfType != 0x4d42 || bf.bfOffBits != 54 || bi.biSize != 40 ||
        bi.biBitCount != 24 || bi.biCompression != 0)
    {
        fclose(outptr);
        fclose(inptr);
        printf("Unsupported file format.\n");
        return 6;
    }

    // Get image's dimensions
    int height = abs(bi.biHeight);
    int width = bi.biWidth;

    // Allocate memory for image
    RGBTRIPLE(*image)[width] = calloc(height, width * sizeof(RGBTRIPLE));
    if (image == NULL)
    {
        printf("Not enough memory to store image.\n");
        fclose(outptr);
        fclose(inptr);
        return 7;
    }

    // Determine padding for scanlines
    int padding = (4 - (width * sizeof(RGBTRIPLE)) % 4) % 4;

    // Iterate over infile's scanlines
    for (int i = 0; i < height; i++)
    {
        // Read row into pixel array
        fread(image[i], sizeof(RGBTRIPLE), width, inptr);

        // Skip over padding
        fseek(inptr, padding, SEEK_CUR);
    }

    // Filter image
    switch (filter)
    {
        // Blur
        case 'b':
            blur(height, width, image);
            break;

        // Edges
        case 'e':
            edges(height, width, image);
            break;

        // Grayscale
        case 'g':
            grayscale(height, width, image);
            break;

        // Reflect
        case 'r':
            reflect(height, width, image);
            break;
    }

    // Write outfile's BITMAPFILEHEADER
    fwrite(&bf, sizeof(BITMAPFILEHEADER), 1, outptr);

    // Write outfile's BITMAPINFOHEADER
    fwrite(&bi, sizeof(BITMAPINFOHEADER), 1, outptr);

    // Write new pixels to outfile
    for (int i = 0; i < height; i++)
    {
        // Write row to outfile
        fwrite(image[i], sizeof(RGBTRIPLE), width, outptr);

        // Write padding at end of row
        for (int k = 0; k < padding; k++)
        {
            fputc(0x00, outptr);
        }
    }

    // Free memory for image
    free(image);

    // Close files
    fclose(inptr);
    fclose(outptr);
    return 0;
}

```

### Makefile

Finally, let’s look at `Makefile`. This file specifies what should happen when we run a terminal command like `make filter`. Whereas programs you may have written before were confined to just one file, `filter` seems to use multiple files: `filter.c` and `helpers.c`. So we’ll need to tell `make` how to compile this file.

Try compiling `filter` for yourself by going to your terminal and running

```
$ make filter
```

```make
filter:
	clang -ggdb3 -gdwarf-4 -O0 -Qunused-arguments -std=c11 -Wall -Werror -Wextra -Wno-gnu-folding-constant -Wno-sign-compare -Wno-unused-parameter -Wno-unused-variable -Wshadow -lm -o filter filter.c helpers.c

```
