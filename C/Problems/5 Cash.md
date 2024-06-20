### Question :-

Suppose you work at a store and a customer gives you $1.00 (100 cents) for candy that costs $0.50 (50 cents). You’ll need to pay them their “change,” the amount leftover after paying for the cost of the candy. When making change, odds are you want to minimize the number of coins you’re dispensing for each customer, lest you run out (or annoy the customer!).

Implement a program in C that prints the minimum coins needed to make the given amount of change, in cents

### Assumed Output

```
Change Owed: 25
1

Change Owed: 70
4
```

### Solution :-

```c
#include <stdio.h>
#include <cs50.h>

int main(void)
{
    int change = 0;
    int coins = 0;
    do
    {
        change = get_int("Enter the Change: ");
    } while (change < 0);

    while (change != 0)
    {
        int temp;
        if (change >= 25)
        {
            temp = change % 25;
            coins += change / 25;
            change = temp;
        }
        else if (change >= 10)
        {
            temp = change % 10;
            coins += change / 10;
            change = temp;
        }
        else if (change >= 5)
        {
            temp = change % 5;
            coins += change / 5;
            change = temp;
        }
        else if (change >= 1)
        {
            temp = change % 1;
            coins += change / 1;
            change = temp;
        }
    }
    printf("%i\n", coins);
}
```

> [!NOTE]
> How this works ?

- This might seem like a tricky one but actually it is just using the right operator.
- first, it does not let the user to input 0 or less because that does not make sense and for that, `do-while loop` is implemented
- The second `while loop` ensures that no change left with the cashier.
- The greedy algorithm is implement because it leads to finding out the least number of coins required to pay
  - so following the algorithm, first the minimum number of 25¢ coins are calculated and then same went for 10¢, 5¢ and 1¢.
  - while being calculated, the number of coins are being transferred to coins variable

### Algorithm Used

> [!TIP] Greedy Algorithm

- A greedy algorithm always picks the best immediate solution to solve a problem.
- For giving change, the cashier uses the largest coin first to minimize the total number of coins.
- Example: If a customer needs 41¢ change, the cashier gives:
  - One 25¢ coin (leaves 16¢)
  - One 10¢ coin (leaves 6¢)
  - One 5¢ coin (leaves 1¢)
  - One 1¢ coin (leaves 0¢)
- This method ensures the fewest coins are given for American and European currencies.

In essence, this approach always uses the largest coins possible first and is both locally and globally optimal for these currencies.
