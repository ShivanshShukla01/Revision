### Question

In the game of Scrabble, players create words to score points, and the number of points is the sum of the point values of each letter in the word.

```plaintext
A            1
B            3
C            3
D            2
E            1
F            4
G            2
H            4
I            1
J            8
K            5
L            1
M            3
N            1
O            1
P            3
Q            10
R            1
S            1
T            1
U            1
V            4
W            4
X            8
Y            4
Z            10
```

For example, if we wanted to score the word “CODE”, we would note that the ‘C’ is worth 3 points, the ‘O’ is worth 1 point, the ‘D’ is worth 2 points, and the ‘E’ is worth 1 point. Summing these, we get that “CODE” is worth 7 points.

implement a program in C that determines the winner of a short Scrabble-like game. Your program should prompt for input twice: once for “Player 1” to input their word and once for “Player 2” to input their word. Then, depending on which player scores the most points, your program should either print “Player 1 wins!”, “Player 2 wins!”, or “Tie!” (in the event the two players score equal points).

### Assumed Output

```
Player 1: Question?
Player 2: Question!
TIE!

Player 1: red
Player 2: wheelbarrow
Player 2 Wins!

Player 1: Scrabble
Player 2: wiNNeR
Player 1 Wins!
```

### Solution

```c
#include <stdio.h>
#include <cs50.h>
#include <string.h>
#include <ctype.h>

string touppercase(string word);
int calculatescores(string word);

char letters[] = {'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I','J','K','L','M','N','O','P','Q','R','S','T','U','W','X','Y', 'Z'};
int marks[] = {1 ,3 ,3, 2, 1, 4 ,2, 4, 1, 8, 5, 1, 3, 1, 1, 3, 10, 1, 1, 1, 1, 4, 4, 8, 4, 10};

int main(void)
{
    //taking input from user
    string player1word = get_string("Player 1, Enter the word: ");
    string player2word = get_string("Player 2, Enter the word: ");

    //converting the input from user to uppercase for ease in comparison
    player1word = touppercase(player1word);
    player2word = touppercase(player2word);

    //calculation of scores
    int p1score = calculatescores(player1word);
    int p2score = calculatescores(player2word);

    //condition for win, lose or tie
    if (p1score > p2score)
    {
        printf("Player 1 Wins !!!\n");
    }

    else if (p1score < p2score)
    {
        printf("Player 2 Wins !!!\n");
    }

    else
    {
        printf("TIE !\n");
    }
}

//function to convert the string to uppercase
string touppercase(string word)
{
    //extracting the length (number of words) in the word
    int len = strlen(word);
    for (int i = 0; i < len; i++)
    {
        //if the character of word is between a and z, hence it is small
        if (word[i] >= 'a' && word[i] <= 'z')
        {
            //if small, subtract 32 the ascii value because refer the ascii chart
            word[i] -= 32;
        }
    }
    //get the updated word in return
    return word;
}

//function to calculate scores
int calculatescores(string word)
{
    //initializing the variable for counting scores
    int score = 0;
    //looping till the length of the word
    for (int i = 0, len = strlen(word); i < len; i++)
    {
        //looping till the array of letter for comparison
        for (int j = 0; j < 26; j++)
        {
            //comparison of each letter of words with each char in letter array
            //if matched
            if (word[i] == letters[j])
            {
                //the score will be increment as the marks alloted respective with each same indexing
                score += marks[j];
                break;
            }
        }
    }
    return score;
}

```

> [!NOTE]
> How this works ?

- The main learning perspective here is how to change to uppercase and comparison done inside the loop for `calculatescores` function
- first, array of letters and marks are created as per the question for the evaluation.
  - created in such a way that character index in letters\[ \] is same as marks\[ \] for comparison
- input from the user has been taken
- converted the text to UPPERCASE for ease in comparison with the arrays created
  - this is going on in the backend, so there is no need to reveal anything.
  - in the function `touppercase`, first taken the length of the word and stored in the **len** variable
  - the loop started from 0 to the len - 1 for indexing purpose
  - every letter for the word is compared and if found between **a** and **z**, _32 is subtracted from its ASCII value_ to convert it to the UPPERCASE variant
- after the conversion, scores have been calculated using `calculatescores` function
  - initialization of variables for the score
  - loop to run from 0 to length of the word
    - another nested loop for going through array of letters
    - when letters of words are compared with letters array
      - score variable has been incremented as per marks of the same index as the index of that character in letters array
