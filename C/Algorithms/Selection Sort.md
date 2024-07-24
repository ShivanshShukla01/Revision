# Selection Sort

```c
#include <stdio.h>

//function prototype
void selection_sort(int array[], int digits);

int main(void)
{
    //variable that will store the number of digits in array
    int digits;
    printf("Enter the number of digits in the list: ");
    //take the input of user and store it at the location in memory where digits is stores
    //or in simple language
    //store in digits
    scanf("%i", &digits);

    //create an array of that size
    int array[digits];

    //prompt user of each of the number of the list
    for (int i = 0; i < digits; i++)
    {
        int num;
        printf("Enter number %i: ", (i + 1));
        scanf("%i", &num);
        array[i] = num;
    }

    //just for presentation purpose to look at the unsorted array first
    printf("--unsorted--");

    printf("\n");
    for (int i = 0; i < digits; i++)
    {
        printf("%i\n", array[i]);
    }
    printf("\n");

    //sorting function
    selection_sort(array, digits);

    //for presentation purpose as well
    printf("--sorted--");

    printf("\n");
    for (int i = 0; i < digits; i++)
    {
        printf("%i\n", array[i]);
    }
    printf("\n");
}

//take the array created by the numbers user entered and size of it as input
void selection_sort(int array[], int digits)
{
    //the comparison have to be made with each number to the last second number because the last number does not going to have any number left for comparison since every single number from starting is getting compared with each and every number that appear after its position in the array
    for (int i = 0; i < (digits - 1); i++)
    {
        //a variable to store the smallest number's array and position of that smallest number
        int min = array[i];
        int index = i;
        //look which is initialized for the comparison for every number which comes after the position of any number
        //hence the loop is started from the position (i + 1) and going upto the last index
        for (int j = (i + 1); j < digits; j++)
        {
            //if the number is smaller than minimum
            if (min > array[j])
            {
                //the minimum value and its index will get updated
                min = array[j];
                index = j;
            }
        }
        //at the end of the above loop we get the smallest number
        //we have to swap it with the first position of the array and shift the boundry which is being done by the first loop and j = (i + 1)
        int swap = array[i];
        array[i] = min;
        array[index] = swap;
    }
}
```
