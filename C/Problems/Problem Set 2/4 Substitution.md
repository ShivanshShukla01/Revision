### Question

In a substitution cipher, we “encrypt” (i.e., conceal in a reversible way) a message by replacing every letter with another letter. To do so, we use a *key*: in this case, a mapping of each of the letters of the alphabet to the letter it should correspond to when we encrypt it. To “decrypt” the message, the receiver of the message would need to know the key, so that they can reverse the process: translating the encrypt text (generally called *ciphertext*) back into the original message (generally called *plaintext*).

A key, for example, might be the string `NQXPOMAFTRHLZGECYJIUWSKDVB`. This 26-character key means that `A` (the first letter of the alphabet) should be converted into `N` (the first character of the key), `B` (the second letter of the alphabet) should be converted into `Q` (the second character of the key), and so forth.

A message like `HELLO`, then, would be encrypted as `FOLLE`, replacing each of the letters according to the mapping determined by the key.

Create a program that enables you to encrypt messages using a substitution cipher. At the time the user executes the program, they should decide, by providing a command-line argument, on what the key should be in the secret message they’ll provide at runtime.

### Assumed Output

```
$ ./substitution
Usage: ./substitution key

$ ./substitution ABC
Key must contain 26 characters.

$ ./substitution 1 2 3
Usage: ./substitution key

$ ./substitution YTNSHKVEFXRBAUQZCLWDMIPGJO
plaintext: Hello!
ciphertext: Ehbbq!
```

### Solution

```c
#include <stdio.h>
#include <cs50.h>
#include <string.h>
#include <ctype.h>

string conversion(string input);

//arrays for indexing with correct key position
char lcase_letters[] = {'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z'};
char ucase_letters[] = {'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'};

string key;
int main(int argc, string argv[])
{
    //putting the key in the key variable
    key = argv[1];

    //making sure only 2 commands are given
    if (argc != 2)
    {
        printf("Usage: ./substitution key\n");
        printf("Key must be of 26 charactersn\n");
        return 1;
    }

    //making sure there are only 26 characters in the key
    if (strlen(key) != 26)
    {
        printf("Key must be of 26 characters\n");
        return 1;
    }

    //this loop is responsible for checking that is there is any duplicate characters or characters other than alphabets
    //loop through each letter of the key
    for (int i = 0; i < 26; i++)
    {
        //put the ascii value of the character in the variable to compare it with all the other ascii values, and if ascii value matches, it means there is a duplicate character
        int compare_var = key[i];
        //conditional make sure the character is either A - Z or a - z
        if ((compare_var >= 65 && compare_var <= 90) || (compare_var >= 97 && compare_var <= 122))
        {
            //loop again for comparison of compare var value with other ascii values of the string
            for (int j = 0; j < 26; j++)
            {
                if (i != j && compare_var == key[j])
                {
                    printf("There are duplicate characters in the key\n");
                    return 1;
                }
            }
        }
        else
        {
            printf("The key contains a character which is not an Alphabet\n");
            return 1;
        }

    }

    //taking input from user and providing plaintext
    string plaintext = get_string("Plaintext: ");
    printf("ciphertext: %s\n", conversion(plaintext));

}

//function to convert plaintext to ciphertext
string conversion(string input)
{

    //loop for iteration of every character of plaintext
    for (int i = 0; i < strlen(input); i++)
    {
        //loop for comparison of character with every letter of uppercase and lowercase letter arrays
        for (int j = 0; j < 26; j++)
        {
            //confirming if uppercase or not, do not use like isupper() == 0, it turns out to be checking for lowercase hence reversing the requirements
            if (isupper(input[i]))
            {
                //comparison with every items of uppercase letters array
                if (input[i] == ucase_letters[j])
                {
                    //making sure the case of plaintext and ciphertext remain same
                    input[i] = toupper(key[j]);
                    break;
                }
            }
            //if not uppercase then lowecase
            else
            {
                if (input[i] == lcase_letters[j])
                {
                    input[i] = tolower(key[j]);
                    break;
                }
            }
        }
    }

    return input;
}


```

> [!NOTE]
> How this works ?

- the main thing to learn here is indexing
- make the code take input from the command prompt
  - ensure only 2 commands are given
  - ensure only 26 letters are there are in the key
  - ensure there must not be duplicate characters and characters other than Alphabets using their ASCII value.
- taken plaintext from user and provided ciphertext
  - made function `conversion` which takes input from user as argument
  - iterate each of its letters
  - compare it with letters of array on the basis of its character case (upper or lower)
  - when found the match, replace the character of user input with the letter from the key which corresponds with the position on the arrays made for comparison
  - return the transformed user input
