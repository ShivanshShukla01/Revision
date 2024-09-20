### Question

WAV files are a common file format for representing audio. WAV files store audio as a sequence of “samples”: numbers that represent the value of some audio signal at a particular point in time. WAV files begin with a 44-byte “header” that contains information about the file itself, including the size of the file, the number of samples per second, and the size of each sample. After the header, the WAV file contains a sequence of samples, each a single 2-byte (16-bit) integer representing the audio signal at a particular point in time.

Scaling each sample value by a given factor has the effect of changing the volume of the audio. Multiplying each sample value by 2.0, for example, will have the effect of doubling the volume of the origin audio. Multiplying each sample by 0.5, meanwhile, will have the effect of cutting the volume in half.

### Assumed Output

```
$ make volume
$ ./volume input.wav output.wav 2.0
$ ./volume input.wav output.wav 0.5
$ ./volume
Usage: ./volume input.wav output.wav factor
$ ./volume input.mp3 output.wav 2.0
Could not open file.
```

### Solution

```c
// Modifies the volume of an audio file

#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

// Number of bytes in .wav header
const int HEADER_SIZE = 44;

//written to only make the aliases for the datatype names
typedef uint8_t byte;
typedef int16_t BYTE;

int main(int argc, char *argv[])
{
    // Check command-line arguments
    if (argc != 4)
    {
        printf("Usage: ./volume input.wav output.wav factor\n");
        return 1;
    }

    // Open files and determine scaling factor
    FILE *input = fopen(argv[1], "r");
    //if there is nothing in the input, it will return NULL hence exiting the program as there is not input file
    if (input == NULL)
    {
        printf("Could not open file.\n");
        return 1;
    }

    FILE *output = fopen(argv[2], "w");
    if (output == NULL)
    {
        printf("Could not open file.\n");
        return 1;
    }

    //the number entered by the user which is going to be used to scale the output
    float factor = atof(argv[3]);

    //datatype byte which is the alias of uint8_t means unassigned integer of 8 bits
    //the audio given was a .wav file and the header in the wav file contains the data related to the wav file

    //creating an area where the header of the file is going to be stored and they using it, we are going to put the header file of the input wav file to output wav file
    byte *header[HEADER_SIZE];

    //copied the header from the input file and stored in the header
    fread(header, HEADER_SIZE, sizeof(int), input);
    /*
    header - jahan par read kiya hua data store hoge
    HEADER_SIZE - kitna size hai data ka jisko read karna hai
    sizeof(int) - ek baar mein kitna data read karna hai
    input - jahan se data ko read karna hai
    */

    //pasted the header file which was stored in the header[] to the output file
    fwrite(header, HEADER_SIZE, sizeof(int), output);
    /*
    header - jahan se data utha ke paste karna hai
    HEADER_SIZE - jitna data karna hai uska size
    sizeof(int) - ek baar mein kitna data write karna hai
    input - jaha par data ko paste karna hai
    */

    //BYTE is just the alias of int16_t means integer which of size 16 bits
    //created a area called buffer to store the content of the sample and will take the factor to increase the  volume of the audio
    BYTE buffer;

    //until and unless there is data or sample remain in the audio,
    while (fread(&buffer, sizeof(BYTE), 1, input) != 0)
    //read the sample from the input audio file, which is as per question 16 bits of size and read 1 byte data at a time and then put or store the data at the location where buffer is stored.
    {
        //increase the buffer as per the factor
        buffer = buffer * factor;
        //write the updated buffer in the output file 1 Byte at a time
        fwrite(&buffer, sizeof(BYTE), 1, output);
    }

    // Close files
    fclose(input);
    fclose(output);
}
```
