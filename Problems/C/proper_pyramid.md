#### To Print :-

```
    # #
   ## ##
  ### ###
 #### ####
##### #####

or of any height user defines
```

```c
#include <stdio.h>
#include <cs50.h>

void spaces(int space);
void character(int charac);
void gap(void);
void r_character(int r_charac);

int main(void)
{
    int main_height = get_int("Enter: ");

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
