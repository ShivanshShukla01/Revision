#### To Print :-

```
     #
    ##
   ###
  ####
 #####

or of any height user defines
```

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
