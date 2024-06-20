### Question :-

Create a proper pyramid of the height entered by the user while restricting the user input between 1 and 8, both inclusive.

### To print :-

```
    # #
   ## ##
  ### ###
 #### ####
##### #####

or of any height user defines
```

### Solution :-

```c
#include <stdio.h>
#include <cs50.h>

void spaces(int space);
void character(int charac);
void gap(void);
void r_character(int r_charac);

int main(void)
{
    int main_height = 1;
    do
    {
        int ask_height = get_int("Enter: ");
        main_height = ask_height;

    }while (main_height <= 0 || main_height > 8);

    for (int i = 0; i < main_height; i++)
    {
        spaces(main_height - i - 1);
        character(i + 1);
        gap();
        r_character(i + 1);
    }
}
void spaces(int space)
{
    for (int i = 0; i < space; i++)
    {
        printf(" ");
    }
}

void character(int charac)
{

    for (int i = 0; i < charac; i++)
    {
        printf("#");
    }
}

void gap(void)
{
    printf(" ");
}

void r_character(int r_charac)
{
    for (int i = 0; i < r_charac; i++)
    {
        printf("#");
    }
    printf("\n");
}
```

> [!NOTE]
> How this works ?

- This code might look hard but it is actually easy
- The only addition except the previous ones is that it will not let the user enter the value less than 0 and more than 8
- first it takes input from the user and store it in the main_height variable
- then will print spaces just like in right aligned pyramid with input `main_height - i - 1` in `spaces` function.
  - then it will print the character in the same way as well but will not enter the new line after the printing of the character instead `gap` function will use. Only `printf` would have been necessary but it is easy to understand this way
- then will print the character just like left aligned pyramid.
