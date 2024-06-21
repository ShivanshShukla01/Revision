### Question

Implement a program that calculates the approximate grade level needed to comprehend some text. Your program should print as output “Grade X” where “X” is the grade level computed, rounded to the nearest integer. If the grade level is 16 or higher (equivalent to or greater than a senior undergraduate reading level), your program should output “Grade 16+” instead of giving the exact index number. If the grade level is less than 1, your program should output “Before Grade 1”.

### Assumed Output

```
$ ./readability
Text: One fish. Two fish. Red fish. Blue fish.
Before Grade 1

$ ./readability
Text: Harry Potter was a highly unusual boy in many ways. For one thing, he hated the summer holidays more than any other time of year. For another, he really wanted to do his homework, but was forced to do it in secret, in the dead of the night. And he also happened to be a wizard.
Grade 5

$ ./readability
A large class of computational problems involve the determination of properties of graphs, digraphs, integers, arrays of integers, finite families of finite sets, boolean formulas and elements of other countable domains.
Grade 16+

$ ./readability
Congratulations! Today is your day. You're off to Great Places! You're off and away!
Grade 3
```

### Solution

```c
#include <stdio.h>
#include <cs50.h>
#include <string.h>
#include <math.h>
#include <ctype.h>

//prototypes for functions
int csentences(string text);
int cwords(string text);
int cletters(string text);
float average_per_100(int value, int words);

int main(void)
{
    //taking input from user
    string text = get_string("Enter the Text: ");

    // initializing variables
    int letters = cletters(text);
    int words = cwords(text);
    int sentences = csentences(text);

    //calculating the average for the algorithm of coleman liau index
    float L = average_per_100(letters, words);
    float S = average_per_100(sentences, words);

    //logic for applying the coleman liau index algorithm
    float index = ((0.0588 * L) - (0.296 * S) - 15.8);

    //rounding off the index to closest integer
    int rounded_index = round(index);

    //conditionals for printing as per question
    if (rounded_index <= 1)
    {
        printf("\nBefore Grade 1\n\n");
    }
    else if (rounded_index >= 16)
    {
        printf("\nGrade 16+\n\n");
    }
    else
    {
        printf("\nGrade %i\n\n", rounded_index);
    }

}

//function to calculate letters in string
int cletters(string text)
{
    int letters= 0;
    //start from 0 to length - 1 for complete indexing
    for (int i = 0, length = strlen(text); i < length; i++)
    {
        //function for ctype.h library which means "is alpha numeric", if alphanumeric, increment the letter count
        if (isalnum(text[i]))
        {
            letters++;
        }
    }
    return letters;
}

//function to calculate words in string
int cwords(string text)
{
    int words = 0;
    //from 0 to length for the last word as well
    for (int i = 0, length = strlen(text); i <= length; i++)
    {
        //to count the last word as well
        if (i == length)
        {
            words++;
        }
        //words are separated via spaces, so if there is a space, a word must be passed and incrementing the words counter
        if (text[i] == ' ')
        {
            words++;
        }
    }
    return words;
}

//function to calculate sentences in string
int csentences(string text)
{
    int sentences = 0;
    //starting from 0 to length for complete indexing
    for (int i = 0, length = strlen(text); i < length; i++)
    {
        //for coleman liau indexing, the sentences are terminated either by (. or ? or !) hence the logic
        //if either of the three appears in the sentence, it means the sentence is just finished
        if (text[i] == '.' || text[i] == '?' || text[i] == '!')
        {
            sentences++;
        }
    }
    return sentences;
}

//function to calculate average
float average_per_100(int value, int words)
{
    //initialization of variable
    float average;

    //formula is jitte letter/sentences, unko divide karo by number of words then multiply by 100
    average = (float)value / (float)words * 100.0;

    return average;
}

```

> [!NOTE]
> How this works ?

- The important thing to understand in this concept is the type casting from float to int and counting in strings with different scenarios
- takes input from the user and store it in the text variable
- use functions to count letters, words and sentences via `cletters`, `cwords` and `csentences`.
  - working of functions can be understood from the comments documented above
- use `average_per_100` function to calculate average letters and sentences and store them in the variable `L` and `S` respectively for the application in the algorithm
- applying the **coleman-liau index** formula and storing the result in index variable
- roundoff the index as per requirement of the question
  - the data type is take float first then type casted into integer for the accuracy
- Then displayed the result as per requirements.

### Algorithm Used

> [!info] Coleman-Liau Index.

So what sorts of traits are characteristic of higher reading levels? Well, longer words probably correlate with higher reading levels. Likewise, longer sentences probably correlate with higher reading levels, too.

A number of “readability tests” have been developed over the years that define formulas for computing the reading level of a text. One such readability test is the *Coleman-Liau index*. The Coleman-Liau index of a text is designed to output that (U.S.) grade level that is needed to understand some text. The formula is : -

```
index = 0.0588 * L - 0.296 * S - 15.8

L -> average number of letter per 100 words
S -> average number of sentences per 100 words
```
