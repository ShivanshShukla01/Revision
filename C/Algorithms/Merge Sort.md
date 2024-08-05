# Merge Sort
### Click here to 👉 [Watch the Visual Explanation.](https://www.youtube.com/watch?v=oAuQ_bXr5NA)

```c
//including the necessary header files
#include <stdio.h>

//prototypes of the functions
void sort(int array[], int digits);
void merge(int array[], int lefthalf[], int righthalf[], int lefthalfsize, int righthalfsize);

//the main function
int main(void)
{   
    //initialised a variable to store the input from the user
    int digits;
    printf("Enter the number of digits in your list: ");
    scanf("%i", &digits); //storing the number of digits in the list of user
    printf("\n");

    int array[digits]; //initializing the array to store the elements of the lists of user
    for (int i = 0; i < digits; i++)
    {
        int num; //variable to store the elements of the list of user
        printf("Enter number %i: ", (i + 1));
        scanf("%i", &num); //taking input from the user 
        array[i] = num; //storing the input from the user in the array initialised above
    }

    //printing the unsorted array for presentation
    printf("--unsorted--");
    printf("\n");
    for (int i = 0; i < digits; i++)
    {
        printf("%i ", array[i]);
    }
    printf("\n");
    
    //function that sort the array using mergesort 
    sort(array, digits);

    printf("\n");
    //printing the sorted array 
    printf("--sorted--");
    printf("\n");
    for (int i = 0; i < digits; i++)
    {
        printf("%i ", array[i]);
    }
    printf("\n");
}

//takes the array of numbers and no of elements in that array
void sort(int array[], int digits)
{
    //if the number of elements is less than 2, then it is basically already sorted
    if (digits < 2)
    {
        return;
    }
    
    int mid = digits / 2; //storing the mid value from the array will be divided into two parts
    int lefthalf[mid], righthalf[digits - mid]; //initialising the array to store the divided arrays with required storage

    //storing the left part of the array in lefthalf array
    for (int i = 0; i < mid; i++)
    {
        lefthalf[i] = array[i];
    }

    //storing the right part of the originally divided array in the righthalf array
    for (int i = mid; i < digits; i++)
    {
        righthalf[i - mid] = array[i];
    }

    //recursively calling the function for each of the divided array so that the array can be divided untill it has only 1 element left 
    //because as soon as there is only 1 element left, 
    sort(lefthalf, mid);
    sort(righthalf, (digits - mid));

    //merge function basically merge the sorted arrays
    //when there is only 1 element left in an array, the array is already sorted and then array created from merging those sorted arrays is also sorted and therefore at the end, we will get the sorted array using merge sort sorting method
    merge(array, lefthalf, righthalf, mid, (digits - mid));

}

//taking the arguments: the array in which changes are to be made, the left half, right half array and their respective sizes
void merge(int array[], int lefthalf[], int righthalf[], int lefthalfsize, int righthalfsize)
{
    //initializing the variables which are to be used to move and compare within the array and elements of the array respectively
    /*
    This can be better understood from the video linked above.
    - array_pointer - marker for the original array in which changes are to made
    - lefthalf_pointer - marker for the array of elements on the left size of the mid point of the original array
    - righthalf_pointer - marker for the array of elements on the right size of the mid point of the original array
    */
    int array_pointer = 0, lefthalf_pointer = 0, righthalf_pointer = 0;

    //these conditions are better understood from the video linked above
    //untill there is any comparison possible between the lefthalf and righthalf arrays, this will go on
    while(lefthalf_pointer < lefthalfsize && righthalf_pointer < righthalfsize)
    {
        if (lefthalf[lefthalf_pointer] < righthalf[righthalf_pointer])
        {
            array[array_pointer] = lefthalf[lefthalf_pointer];
            array_pointer++, lefthalf_pointer++;
        }
        else
        {
            array[array_pointer] = righthalf[righthalf_pointer];
            array_pointer++, righthalf_pointer++;
        }
    }
    //when the comparison is not possible between the two arrays as one array's elements are included in the original array and now there are now arrays left in that array for the other array's elements to be compared with
    //hence now is the need to basically just take the leftover element in the array and put it in the original array as they must already sorted
    while(lefthalf_pointer < lefthalfsize) //for lefthalf array
    {
        array[array_pointer] = lefthalf[lefthalf_pointer];
        array_pointer++, lefthalf_pointer++;
    }

    while(righthalf_pointer < righthalfsize)//for righthalf array
    {
        array[array_pointer] = righthalf[righthalf_pointer];
        array_pointer++, righthalf_pointer++;
    }
}
```

##### This is a little different approach with similar workflow from CodeWithHarry

```c
// Include the standard input/output library
#include <stdio.h>

// Function to print an array of integers
void printArray(int *A, int n) {
    // Loop through each element of the array
    for (int i = 0; i < n; i++) {
        // Print the current element followed by a space
        printf("%d ", A[i]);
    }
    // Print a newline character to move to the next line
    printf("\n");
}

// Function to merge two sorted subarrays into a single sorted subarray
void merge(int A[], int mid, int low, int high) {
    // Declare indices for the left and right subarrays, and the temporary array
    int i, j, k;
    // Declare a temporary array to store the merged result
    int B[100];
    // Initialize the indices for the left and right subarrays
    i = low;
    j = mid + 1;
    k = low;

    // Merge smaller elements first
    while (i <= mid && j <= high) {
        // If the current element in the left subarray is smaller
        if (A[i] < A[j]) {
            // Copy it to the temporary array
            B[k] = A[i];
            // Move to the next element in the left subarray
            i++;
            // Move to the next position in the temporary array
            k++;
        }
        // If the current element in the right subarray is smaller
        else {
            // Copy it to the temporary array
            B[k] = A[j];
            // Move to the next element in the right subarray
            j++;
            // Move to the next position in the temporary array
            k++;
        }
    }

    // Copy any remaining elements from the left subarray
    while (i <= mid) {
        B[k] = A[i];
        k++;
        i++;
    }

    // Copy any remaining elements from the right subarray
    while (j <= high) {
        B[k] = A[j];
        k++;
        j++;
    }

    // Copy the merged result back to the original array
    for (int i = low; i <= high; i++) {
        A[i] = B[i];
    }
}

// Function to sort an array using the Merge Sort algorithm
void sort(int A[], int low, int high) {
    // Declare the middle index
    int mid;
    // If the subarray has more than one element
    if (low < high) {
        // Calculate the middle index
        mid = (low + high) / 2;
        // Recursively sort the left subarray
        sort(A, low, mid);
        // Recursively sort the right subarray
        sort(A, mid + 1, high);
        // Merge the sorted subarrays
        merge(A, mid, low, high);
    }
}

// Main function
int main() {
    // Declare an array of integers
    int A[] = {9, 1, 4, 14, 4, 15, 6};
    // Declare the size of the array
    int n = 7;
    // Print the original array
    printArray(A, n);
    // Sort the array using Merge Sort
    sort(A, 0, 6);
    // Print the sorted array
    printArray(A, n);
    // Return 0 to indicate successful execution
    return 0;
}
```