### Question :-

Take input from user and create a right aligned pyramid for the user specific height.

### To print :-

```
     #
    ##
   ###
  ####
 #####

or of any height user defines
```

### Solution :-

```c
#include <stdio.h>
#include <cs50.h>

void spaces(int space);
void characters(int character);

int main(void)
{
    int height = get_int("Enter: ");
    for (int i = (height); i > 0 ; i--)
    {
        spaces(i - 1);
        characters(height - i + 1);
    }
}

void spaces(int space)
{
    for (int j = 0; j < space; j++)
    {
        printf(" ");
    }
}

void characters(int character)
{
    for (int k = 0; k < character; k++)
    {
        printf("#");
    }
    printf("\n");
}
```

> [!NOTE]
> How this works ?

- user input is stored in height variable
- in this program, first we need to print spaces than the character
  - the number of spaces is decreasing from the given height to end of the pyramid.
- so `spaces` function take input from for loop where the value of `i` started at height level and went to zero one by one hence printing less number of spaces each time.
- the `character` function is responsible for printing `#` in increasing order after spaces.
  - since the `i` of for loop in `main` function started from height, looping with `height - i + 1`, at the end, i will become 1 and cancel the other 1 leading to the printing of characters to the height given by the user and also leads to increasing order of the #'s.
