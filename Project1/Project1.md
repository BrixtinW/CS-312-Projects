# Project 1

Using Fermat's Little Theorem and the Miller-Rabin test to determine if a number is prime

## 1 - Code
```
import random

# This is main function that is connected to the Test button. You don't need to touch it.
def prime_test(N, k):
    return fermat(N,k), miller_rabin(N,k)

# You will need to implement this function and change the return value.
def mod_exp(x, y, N):
    if y == 0:
        return 1
    z = mod_exp(x, (y//2), N)
    if y % 2 == 0:
        return (z**2 % N)
    else:
        return (x*(z**2)) % N

# You will need to implement this function and change the return value.  
def fprobability(k):
    error = 1.0
    for _ in range(1, k+1):
        error = error/2
    return 1-error

# You will need to implement this function and change the return value.  
def mprobability(k):
    error = 1.0
    for _ in range(1, k+1):
        error = error/4
    return 1-error

# You will need to implement this function and change the return value, which should be
# either 'prime' or 'composite'.
#
# To generate random values for a, you will most likley want to use
# random.randint(low,hi) which gives a random integer between low and
# hi, inclusive.
def fermat(N,k):
    result = 'prime'
    for i in range(k):
        a = random.randint(1, N-1)
        if mod_exp(a, (N-1), N) != 1:
            result = 'composite'
    return result

# You will need to implement this function and change the return value, which should be
# either 'prime' or 'composite'.
#
# To generate random values for a, you will most likley want to use
# random.randint(low,hi) which gives a random integer between low and
#  hi, inclusive.
def miller_rabin(N,k):
   
    a = random.randint(2, N-2)
   
    def helper(N, k, e, a):
        if k == 0:
            return 'prime'
           
        # a = random.randint(1, e)
        modExpResult = mod_exp(a, e, N)
        print(f"calculation: {a}^{e} = {modExpResult} mod ({N})")
       
        if modExpResult == N-1 or e <= 3:
            return 'prime'
        elif modExpResult == 1:
            return helper(N, k-1, e//2, a)
        else:
            # print("Failed --> ", str(modExpResult))
            return 'composite'
   
           
           
    return helper(N, k, (N-1), a)
```

## 2 - Complexity
Discuss the time and space complexity of the Modexp and Fermat algorithms.
### mod_exp Complexity

![mod_exp Complexity Diagram](Time-Complexity-of-mod_exp.png)

### fermat Complexity
![mod_exp Complexity Diagram](CS-312-Projects/Project1/Time Complexity of mod_exp.png)

## 3 - 
## 4 - 
## 5 - 
