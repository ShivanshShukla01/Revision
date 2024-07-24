# Bubble Sort

```c
#include <stdio.h>

// function prototype
void bubble_sort(int array[], int digits);

int main(void)
{
    // variable to store the number of digits in the list
    int digits;
    printf("Number of digits in the list: ");
    scanf("%i", &digits);

    // an array to compare the number and basically make a digital list
    int array[digits];

    // ask the user for all the digits he has in the list and putting each of the values in the list/array
    for (int i = 0; i < digits; i++)
    {
        int num;
        printf("Enter number %i: ", (i + 1));
        scanf("%i", &num);
        array[i] = num;
    }

    // only for presentation purpose
    printf("--unsorted--");
    printf("\n");
    for (int i = 0; i < digits; i++)
    {
        printf("%i\n", array[i]);
    }
    printf("\n");

    // sorting the array
    bubble_sort(array, digits);

    // just for presentation purpose
    printf("--sorted--");
    printf("\n");
    for (int i = 0; i < digits; i++)
    {
        printf("%i\n", array[i]);
    }
    printf("\n");
}

// taking the array and digits as the arguments
void bubble_sort(int array[], int digits)
{
    // if there is only one number in the list, hence there is no need to sort the list
    if (digits == 1)
    {
        return;
    }

    // since bubble sort compare the adjacent values and if [i > (i + 1)], then it swaps there places
    // so this is here to count the swaps
    // if swap made is 0, hence the list is already sorted now
    int swap_count = 0;
    // since comparison is being made with each adjacent value, indexing the last index as well will compare it with the value after it and there is no value after it which leads us to segmentation error and code with crash
    for (int i = 0; i < (digits - 1); i++)
    {
        // comparing the adjacent values and if the values and not in ascending order
        // changing the comparison operation will sort in descending order
        if (array[i] > array[i + 1])
        {
            // swap the values
            int temp = array[i];
            array[i] = array[i + 1];
            array[i + 1] = temp;
            swap_count++;
        }
    }

    // if swap count is 0, hence the list is already sorted
    if (swap_count == 0)
    {
        return;
    }

    // i use the recursion however the GPT is telling to opitimize the code and put it all in a loop but i made this myself so that's why this less optimized code is here
    bubble_sort(array, digits);
}
```
