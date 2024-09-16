# Question
You can find the Question [Here](https://github.com/ShivanshShukla01/Revision/blob/main/CS50x/C/Problems/Problem%20Set%201/3%20Credit.md)

# Solution
```python
number = input("Number: ")
digits = list()

def verify(num):
    for i in num:
        digits.append(int(i))

    doubled_digits = list()
    for i in range((len(num) - 2), -1, -2):
        double_digit_nums = digits[i] * 2
        if double_digit_nums >= 10:
            double_digit_nums_first_digit = int(double_digit_nums / 10)
            double_digit_nums_second_digit = double_digit_nums % 10 
            doubled_digits.append(double_digit_nums_first_digit)
            doubled_digits.append(double_digit_nums_second_digit)
        else:
            doubled_digits.append(double_digit_nums)

    checksum = 0
    for i in doubled_digits:
        checksum += i

    for i in range((len(num) - 1), -1, -2):
        checksum += digits[i]
    
    if checksum % 10 == 0:
        return 0
    else:
        return 1
    

first_two_digits = int(number[0] + number[1])
amr_exp = [34, 37]
mstrcrd = [51, 52, 53, 54, 55]
visa_len = [13, 16]


if verify(number) == 0:
    if len(number) == 15 and first_two_digits in amr_exp:
        print("AMEX")
    elif len(number) == 16 and first_two_digits in mstrcrd:
        print("MASTERCARD")
    elif len(number) in visa_len and int(first_two_digits / 10)  == 4:
        print("VISA")
    else:
        print("VALID")
else:
    print("INVALID")

```
