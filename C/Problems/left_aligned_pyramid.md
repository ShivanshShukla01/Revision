### Question :-

Create a program to take user from input and make a left aligned of the user-specific height

### To print :-

```
#
##
###
####
#####

or any other height that user defines
```

### Solution :-

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

> [!NOTE]
> How this works ?

- `get_int` take the integer input from the user and stored it into height variable.
- The first for loop provide argument to the pattern function.
- The `pattern` function take the argument and put it in length variable.
- Now it runs for the number of time, equal to the value of `time` and prints `#` for `i + 1` number of times and enter into a new line and this happened for the number of times, equal to the value entered by User.
