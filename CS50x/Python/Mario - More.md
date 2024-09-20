# Question
You can find the Question [Here](https://github.com/ShivanshShukla01/Revision/blob/main/CS50x/C/Problems/Problem%20Set%200/3%20Proper%20Pyramid.md)

# Solution
```python
while True:
    try:
        height = int(input("Height: "))
        valid_heights = [x for x in range(1, 9)]
        if height in valid_heights:
            break
    except ValueError:
        pass

def spaces():
    print("  ", end = "")

for i in range(1, (height + 1)):
    print(" " * (height - i), end = "")
    print("#" * (height - (height - i)), end = "")
    spaces()
    print("#" * i)
```
