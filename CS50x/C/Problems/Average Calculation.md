### Question

Calculate the average of the scores entered by the user.

### Solution

```c
#include <stdio.h>
#include <cs50.h>

float average(int length, int array[]);

int N = 0;

int main(void)
{
    N += get_int("Enter the number of scores you have: ");
    int scores[N];
    for (int i = 0; i < N; i++)
    {
        scores[i] = get_int("Score%i: ", i + 1);
    }

    printf("Average: %f\n", average(N, scores));
}

float average(int length, int array[])
{
    int sum = 0;
    for (int i = 0; i < length; i++)
    {
        sum += array[i];
    }

    return sum / (float) length;
}
```

> [!NOTE]
> How this works ?

- This code is not hard but this is here just to get a quick glance on indexing and data-type casting.
- ask user how many scores/marks he/she has and store it in `N` variable
- create a empty array with the size of N to make the code work for any number
  - use the loop to ask the user to insert the score with ease
- `average` function simply calculate the average by
  - first counting the number of items stored in the marks array using a temporary variable and for loop to go run the loop for each item and increment the value
  - the `length` in average function was basically an int but to include the decimal, the datatype `int` is `type-casted` to `float`.
