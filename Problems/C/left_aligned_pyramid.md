#### To print :-

```
#
##
###
####
#####

or any other height that user defines
```

```c
#include <stdio.h>
#include <cs50.h>

void pattern(int length);

int main(void)
{
    int height = get_int("Enter the height: ");
    for (int i = 0; i < height; i++)
    {
        pattern(i + 1);
    }
}

void pattern(int length)
{
    for (int j = 0; j < length; j++)
    {
        printf("#");
    }
    printf("\n");
}
```
