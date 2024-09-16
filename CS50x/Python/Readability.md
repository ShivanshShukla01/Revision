# Question
You can find the Question [Here](https://github.com/ShivanshShukla01/Revision/blob/main/CS50x/C/Problems/Problem%20Set%202/2%20Readability.md)

# Solution
```python
text = input("Text: ")

L = 0
W = 0
S = 0

end = [".", "!", "?"]

for i in text:
    if i not in end and i != " ":
        L+=1
    elif i in end:
        S+=1
    elif i == " ":
        W+=1
W+=1 #for final word since it might be left out as there will be no space at the end of the sentence

L = L/W * 100
S = S/W * 100

index = (0.0588 * L) - (0.296 * S) - 15.8

index = int(round(index, 2))

if index >= 16:
    print("Grade 16+")
elif index < 1:
    print("Before Grade 1")
else:
    print(f"Grade {index}")

```
