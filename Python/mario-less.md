# Question
You can find the Question [Here](https://github.com/ShivanshShukla01/Revision/blob/main/C/Problems/Problem%20Set%200/2%20Right%20Aligned%20Pyramid..md)

# Solution
```python
while True:
    try:
        height = int(input("Height: "))
        valid_height = [x for x in range(1, 9)]
        if height in valid_height:
            break
    except ValueError:
        pass    

for i in range(1, (height + 1)):
    print(" " * (height - i), end = "")
    print("#" * (height - (height - i)))
```
