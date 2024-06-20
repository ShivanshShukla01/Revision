### Question :-

A credit (or debit) card, of course, is a plastic card with which you can pay for goods and services. Printed on that card is a number that’s also stored in a database somewhere, so that when your card is used to buy something, the creditor knows whom to bill. There are a lot of people with credit cards in this world, so those numbers are pretty long: American Express uses 15-digit numbers, MasterCard uses 16-digit numbers, and Visa uses 13- and 16-digit numbers. And those are decimal numbers (0 through 9), not binary, which means, for instance, that American Express could print as many as 10^15 = 1,000,000,000,000,000 unique cards! (That’s, um, a quadrillion.)
Actually, that’s a bit of an exaggeration, because credit card numbers actually have some structure to them. All American Express numbers start with 34 or 37; most MasterCard numbers start with 51, 52, 53, 54, or 55 (they also have some other potential starting numbers which we won’t concern ourselves with for this problem); and all Visa numbers start with 4. But credit card numbers also have a “checksum” built into them, a mathematical relationship between at least one number and others. That checksum enables computers (or humans who like math) to detect typos (e.g., transpositions), if not fraudulent numbers, without having to query a database, which can be slow. Of course, a dishonest mathematician could certainly craft a fake number that nonetheless respects the mathematical constraint, so a database lookup is still necessary for more rigorous checks.

Implement a program in C that checks the validity of a given credit card number.

### Assumed Output

```
Number: 4003600000000014
VISA

Number: 6543513186548448
INVALID
```

### Solution :-

```c
#include <stdio.h>
#include <cs50.h>
#include <math.h>

int checksum(long number, int size);
int length(long number);
int first_digit(long number, int size);
int first_two_digits(long number, int size);


int main(void)
{
    // long datatype is taken as credit card numbers can be so big which will be too much for integer datatype to store
    long number = get_long("Enter the Credit Card Number: ");
    int size = length(number);

    int sum = checksum(number, size);

    int one_digit = first_digit(number, size);
    int two_digits = first_two_digits(number, size);

    if (sum % 10 == 0)
    {
        //check if VISA
        if (one_digit == 4)
        {
            printf("This is a Valid VISA Card\n");
        }

        //check if MasterCard
        else
        {
            int array_to_check_two_digits[] = {51, 52, 53, 54, 55};
            for (int i = 0; i < 5; i++)
            {
                if (two_digits == array_to_check_two_digits[i])
                {
                    printf("This is a Valid MasterCard\n");
                    return 0;
                }
                else if (two_digits == 34 || two_digits == 37)
                {
                    printf("This is a Valid American Express Card\n");
                    return 0;
                }
                else
                {
                    printf("This is a Valid Credit Card\n");
                    return 0;
                }
            }
        }
    }

    else
    {
        printf("This Card Number is Not Valid\n");
    }

}

int checksum(long number, int size)
{
    //extracting the digits individually and storing it in an array
    int digits[size];
    for (int i = 0; i < size; i++)
    {
         digits[size - i - 1] = number % 10;
         number /= 10;
    }

    //going through each digit and doubling it
    int size_of_doubled = size / 2;
    int digits_doubled[size_of_doubled];
    for (int i = size - 2, j = 0; i >= 0; i -= 2, j++)
    {
        digits_doubled[j] = digits[i] * 2;
    }

    //adding each digit of double digited numbers
    int sum = 0;
    for (int i = 0; i < size_of_doubled; i++)
    {
        if (digits_doubled[i] >= 10)
        {
            sum += digits_doubled[i] % 10;
            sum += digits_doubled[i] / 10;
        }
        else
        {
            sum += digits_doubled[i];
        }
    }

    //adding numbers to sum which were not doubled
    for (int i = size - 1; i >= 0; i -= 2)
    {
        sum += digits[i];
    }
    return sum;
}

int length(long number)
{
    int n = 0;
    while (number > 0)
    {
        number /= 10;
        n++;
    }
    return n;
}

int first_digit(long number, int size)
{
    long denominator = 1;

    //the first digit can be accessed of a 16 digit number if it is divided by 1 with 15 zeros
    //similarly number of digits in credit card number / same digit number with all zeroes and 1 at the start
    for (int i = 0; i < size-1; i++)
    {
        denominator *= 10;
    }

    int digit = number / denominator;
    return digit;
}

int first_two_digits(long number, int size)
{
    long denominator = 1;

    //the first digit can be accessed of a 16 digit number if it is divided by 1 with 14 zeros
    //similarly number of digits in credit card number / (same - 1) digit number with a zeroes and 1 at the start
    for (int i = 0; i < size-2; i++)
    {
        denominator *= 10;
    }

    int two_digits = number / denominator;

    return two_digits;
}



```

> [!NOTE]
> How this works ?

- This might be seems hard but the only confusing topics find here can be
  - accessing and storing individual digits, and
  - applying the algorithm
- first take the input from user and store in the number variable
- find the number of digits using `length` function and store it in size
- `checksum` function is used to apply algorithm
  - <u>Highlight:</u> how to store each value of a long credit number with ease by
    - getting the last value using `%10`, and
    - deleting it from the number using `/10`
    - repeating the above two steps via for loop
  - At the start of the checksum function
- `first_digit` and `first_two_digits` to identify the credit card issuer via conditions

### Algorithm Used

> [!IMPORTANT] Luhn's Algorithm

1. Multiply every other digit by 2, starting from the second-to-last digit.
2. Sum the digits of the products:
   - If a product is a two-digit number (like 12), add the individual digits (1 + 2).
3. Sum the digits that were not multiplied by 2.
4. Add the two sums together.
5. If the total ends in 0 (i.e., total % 10 == 0), the credit card number is valid.

**Example using the card number:- 4003600000000014**

- Multiply every other digit by 2:
  - 4 0 0 3 6 0 0 0 0 0 0 0 0 0 1 4
  - Becomes: 1•2, 0•2, 0•2, 0•2, 0•2, 6•2, 0•2, 4•2
  - Results: 2, 0, 0, 0, 0, 12, 0, 8
- Sum the digits of the products:
  - 2 + 0 + 0 + 0 + 0 + (1 + 2) + 0 + 8 = 13
- Sum the digits not multiplied by 2:
  - 4 + 0 + 0 + 0 + 0 + 0 + 3 + 0 = 7
- Add the two sums together:
  - 13 + 7 = 20
- Check if the total ends in 0:
  - Yes, it does (20), so the card number is valid.

**Key Points**:

- Multiply every other digit by 2 starting from the second-to-last.
- Add the digits of the products and the untouched digits.
- If the final sum ends in 0, the card number is valid.
