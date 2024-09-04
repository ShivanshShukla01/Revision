# Question - Change the link of youtube video
In anticipation of this problem, we spent the past several days taking photos around campus, all of which were saved on a digital camera as JPEGs on a memory card. Unfortunately, we somehow deleted them all! Thankfully, in the computer world, “deleted” tends not to mean “deleted” so much as “forgotten.” Even though the camera insists that the card is now blank, we’re pretty sure that’s not quite true. Indeed, we’re hoping (er, expecting!) you can write a program that recovers the photos for us!

In a file called `recover.c` in a folder called `recover`, write a program to recover JPEGs from a memory card.

# Background
Even though JPEGs are more complicated than BMPs, JPEGs have “signatures,” patterns of bytes that can distinguish them from other file formats. Specifically, the first three bytes of JPEGs are

```
0xff 0xd8 0xff
```

from first byte to third byte, left to right. The fourth byte, meanwhile, is either `0xe0`, `0xe1`, `0xe2`, `0xe3`, `0xe4`, `0xe5`, `0xe6`, `0xe7`, `0xe8`, `0xe9`, `0xea`, `0xeb`, `0xec`, `0xed`, `0xee`, or `0xef`. Put another way, the fourth byte’s first four bits are `1110`.

Odds are, if you find this pattern of four bytes on media known to store photos (e.g., my memory card), they demarcate the start of a JPEG. To be fair, you might encounter these patterns on some disk purely by chance, so data recovery isn’t an exact science.

Fortunately, digital cameras tend to store photographs contiguously on memory cards, whereby each photo is stored immediately after the previously taken photo. Accordingly, the start of a JPEG usually demarks the end of another. However, digital cameras often initialize cards with a FAT file system whose “block size” is 512 bytes (B). The implication is that these cameras only write to those cards in units of 512 B. A photo that’s 1 MB (i.e., 1,048,576 B) thus takes up 1048576 ÷ 512 = 2048 “blocks” on a memory card. But so does a photo that’s, say, one byte smaller (i.e., 1,048,575 B)! The wasted space on disk is called “slack space.” Forensic investigators often look at slack space for remnants of suspicious data.

The implication of all these details is that you, the investigator, can probably write a program that iterates over a copy of my memory card, looking for JPEGs’ signatures. Each time you find a signature, you can open a new file for writing and start filling that file with bytes from my memory card, closing that file only once you encounter another signature. Moreover, rather than read my memory card’s bytes one at a time, you can read 512 of them at a time into a buffer for efficiency’s sake. Thanks to FAT, you can trust that JPEGs’ signatures will be “block-aligned.” That is, you need only look for those signatures in a block’s first four bytes.

Realize, of course, that JPEGs can span contiguous blocks. Otherwise, no JPEG could be larger than 512 B. But the last byte of a JPEG might not fall at the very end of a block. Recall the possibility of slack space. But not to worry. Because this memory card was brand-new when I started snapping photos, odds are it’d been “zeroed” (i.e., filled with 0s) by the manufacturer, in which case any slack space will be filled with 0s. It’s okay if those trailing 0s end up in the JPEGs you recover; they should still be viewable.

Now, I only have one memory card, but there are a lot of you! And so I’ve gone ahead and created a “forensic image” of the card, storing its contents, byte after byte, in a file called `card.raw`. So that you don’t waste time iterating over millions of 0s unnecessarily, I’ve only imaged the first few megabytes of the memory card. But you should ultimately find that the image contains 50 JPEGs.

# How this works ?
[Click here to go to the Youtube](https://youtu.be/sN6RjIVt9gE)

# Solution
```c
// Include necessary header files for input/output operations, integer types, and boolean values
#include <stdio.h>
#include <stdint.h>
#include <stdbool.h>

// Define a constant for the block size (512 bytes)
#define BLOCK_SIZE 512

int main(int argc, char *argv[])
{
    // Check if the user provided a command-line argument
    // argc represents the number of command-line arguments, and argv is an array of strings representing the arguments
    if (argc != 2)
    {
        // If no argument is provided, print an error message to the standard error stream
        printf("Usage: ./recover image\n");
        // Return an error code (1) to indicate that the program failed
        return 1;
    }

    // Open the raw image file specified by the command-line argument
    FILE *input = fopen(argv[1], "r");
    if (input == NULL)
    {
        // If the file cannot be opened, print an error message to the standard error stream
        printf("Could not open %s\n", argv[1]);
        // Return an error code (1) to indicate that the program failed
        return 1;
    }

    // Initialize variables
    // Declare a buffer array to store 512 bytes (BLOCK_SIZE) of data
    uint8_t buffer[BLOCK_SIZE];
    int image_count = 0;
    FILE *output = NULL;

    // Read the raw image file 512 bytes at a time
    while (fread(buffer, 1, BLOCK_SIZE, input) == BLOCK_SIZE)
    {
        // Check if we've found a JPEG signature (0xff 0xd8 0xff) in the current block
        if (buffer[0] == 0xff && buffer[1] == 0xd8 && buffer[2] == 0xff)
        {
            // Print a message to indicate that a JPEG signature has been found
            printf("Found JPEG signature!\n");

            // Close the previous output file, if any
            if (output != NULL)
            {
                fclose(output);
            }

            // Create a filename in the format "XXX.jpg" using sprintf
            char filename[8];
            sprintf(filename, "%03i.jpg", image_count);
            // Open the output file in write mode
            output = fopen(filename, "w");
            if (output == NULL)
            {
                // If the file cannot be opened, print an error message to the standard error stream
                printf("Could not create %s\n", filename);
                return 1;
            }

            // Write the JPEG signature to the output file
            fwrite(buffer, 1, BLOCK_SIZE, output);

            // Increment the image count
            image_count++;
        }
        else if (output != NULL)
        {
            // If a JPEG signature has already been found, write the block to the current output file
            fwrite(buffer, 1, BLOCK_SIZE, output);
        }
        else
        {
            // If no JPEG signature has been found, print a message
            printf("No JPEG signature found in this block...\n");
        }
    }

    // Close the input and output files
    fclose(input);
    if (output != NULL)
    {
        fclose(output);
    }

    // Print a message indicating the number of recovered JPEG images
    printf("Recovered %d JPEG images.\n", image_count);

    // Return an exit code (0) to indicate that the program succeeded
    return 0;
}
```
