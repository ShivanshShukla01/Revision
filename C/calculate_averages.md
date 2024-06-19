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
