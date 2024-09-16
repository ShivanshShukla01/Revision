# Question
You can find the Question [Here](https://github.com/ShivanshShukla01/Revision/blob/main/C/Problems/Problem%20Set%201/2%20Cash.md)

# Solution
```python
while True:
    try:
        change = float(input("Change: "))
        if change > 0:
            break
    except ValueError:
        pass

coins = 0

while True:
    if change >= 0.25:
        coins = coins + (change / 0.25)
        coins = int(coins)
        change = change % 0.25
        change = round(change, 2)
    elif change >= 0.10:
        coins = coins + (change / 0.10)
        coins = int(coins)
        change = change % 0.10
        change = round(change, 2)
    elif change >= 0.05:
        coins = coins + (change / 0.05)
        coins = int(coins)
        change = change % 0.05
        change = round(change, 2)
    elif change >= 0.01:
        coins = coins + (change / 0.01)
        coins = int(coins)
        change = change % 0.01
        change = round(change, 2)
    elif change == 0.00:
        break        
        
print(coins)

```
