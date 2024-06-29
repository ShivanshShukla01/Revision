### Question

Supposedly, Caesar (yes, that Caesar) used to “encrypt” (i.e., conceal in a reversible way) confidential messages by shifting each letter therein by some number of places. For instance, he might write A as B, B as C, C as D, …, and, wrapping around alphabetically, Z as A. And so, to say HELLO to someone, Caesar might write IFMMP instead. Upon receiving such messages from Caesar, recipients would have to “decrypt” them by shifting letters in the opposite direction by the same number of places.

The secrecy of this “cryptosystem” relied on only Caesar and the recipients knowing a secret, the number of places by which Caesar had shifted his letters (e.g., 1). Not particularly secure by modern standards, but, hey, if you’re perhaps the first in the world to do it, pretty secure!

Unencrypted text is generally called *plaintext*. Encrypted text is generally called *ciphertext*. And the secret used is called a *key*

More formally, Caesar’s algorithm (i.e., cipher) encrypts messages by “rotating” each letter by 𝑘 positions. More formally, if 𝑝 is some plaintext (i.e., an unencrypted message), 𝑝𝑖 is the 𝑖𝑡ℎ character in 𝑝, and 𝑘 is a secret key (i.e., a non-negative integer), then each letter, 𝑐𝑖, in the ciphertext, 𝑐, is computed as
$c_i = (p_i + k) \% 26$

Write a program that enables you to encrypt messages using Caesar’s cipher. At the time the user executes the program, they should decide, by providing a command-line argument, what the key should be in the secret message they’ll provide at runtime. We shouldn’t necessarily assume that the user’s key is going to be a number; though you may assume that, if it is a number, it will be a positive integer.

### Assumed Output

```
$ ./caesar
Usage: ./caesar key

$ ./caesar HELLO
Usage: ./caesar key

$ ./caesar 1 2 3
Usage: ./caesar key

$ ./caesar 13
plaintext: Hi there!
ciphertext: Uv gurer!
```

### Solution

```c
#include <stdio.h>
#include <cs50.h>
//to convert string to integer
#include <stdlib.h>
//to calculate the length the array
#include <string.h>
//to check if a character is a digit
#include <ctype.h>

string conversion(string user_input);

int key;
//taking input from the user in command prompt
int main(int argc, string argv[2])
{
    //to check user must entered only 2 commands
    if (argc != 2)
    {
        printf("Usage: ./caeser key\n");
        printf("key must be a positive integer\n");
        return 1;
    }

    //to check if the second command is only digit or not
    for (int i = 0; i < strlen(argv[1]); i++)
    {
        //can also use like this, "!" before a function
        if (!isdigit(argv[1][i]))
        {
            printf("Usage: ./caeser key\n");
            printf("key must be a positive integer\n");
            return 2;
        }
    }

    //converting key to integer
    key = atoi(argv[1]);

    //checking if key is a positive integer
    if (key < 0)
    {
        printf("Usage: ./caeser key\n");
        return 2;
    }

    //taking plaintext from user
    string plaintext = get_string("Plaintext:  ");

    printf("Ciphertext:  %s\n", conversion(plaintext));

}

string conversion(string user_input)
{
    //iterating through the string
    for (int i = 0, len = strlen(user_input); i < len; i++)
    {
        if (isupper(user_input[i]))
        {
            /*
            to make the key loop back to the starting letter value in case it goes out of bounds and print a non alphabetical character

            the "% 26" is responsible for wrapping back to start
            the "- 65" is taking the whole value within the number of letters only
            the "+ 65" is taking it back to original ascii value

            suppose given "Z" which has ASCII value 90, so just using "+ key" will take it out of the bounds
            so,
            90 + 2(suppose the key given is 2) = 92 => out of bounds
            92 - 65 = 27
            27 % 26 = 1
            1 + 65 = 66 => hence wrapped back to the starting A
            */
           user_input[i] = (user_input[i] + key - 65) % 26 + 65;
        }

        //same logic but since the "a" starts from ASCII value 97
        else if (islower(user_input[i]))
        {
            user_input[i] = (user_input[i] + key - 97) % 26 + 97;
        }

        //to let anything else, " " or "." or "?" or anything else as it is
        else
        {
            user_input[i] = user_input[i];
        }
    }
    return user_input;
}

```

> [!NOTE]
> How this works ?

- the main thing to learn here is to wrapping back to the start position
- main function will take 2 inputs
  - if there are more than 2 inputs, it will show error
  - if the second input is not a digit, it will show error
- The string will be converted to integer using `atoi` function
  - key must be a posivite integer
- take plaintext from user and return ciphertext
  - iterate through each character and shift it using key and logic or wrapping to start is given above in the code in the `conversion` function
