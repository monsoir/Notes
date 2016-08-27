### an array containing hashes of names

> Return: a string formatted as a list of names separated by commas except for the last two names, which should be separated by an ampersand.

```
Example:

namelist([ {'name': 'Bart'}, {'name': 'Lisa'}, {'name': 'Maggie'} ])
# returns 'Bart, Lisa & Maggie'

namelist([ {'name': 'Bart'}, {'name': 'Lisa'} ])
# returns 'Bart & Lisa'

namelist([ {'name': 'Bart'} ])
# returns 'Bart'

namelist([])
# returns ''
```

	
	```py
	def namelist(names):
    #your code here
    	name_array = []
    	for name_info in names:
        	name_array.append(name_info["name"])
    		result = ' & '.join(name_array)
    		result = result.replace(" & ", ", ", result.count('&')-1)
    	return result
	```

### Bit Counting

> Write a function that takes an (unsigned) integer as input, and returns the number of bits that are equal to one in the binary representation of that number.

```py
def countBits(n):
    num_str = bin(n)[2:]
    return num_str.count('1')
```

### Breaking chocolate problem

> Your task is to split the chocolate bar of given dimension n x m into small squares. Each square is of size 1x1 and unbreakable. Implement a function that will return minimum number of breaks needed.
> For example if you are given a chocolate bar of size 2 x 1 you can split it to single squares in just one break, but for size 3 x 1 you must do two breaks.
> If input data is invalid you should return 0 (as in no breaks are needed if we do not have any chocolate to split). Input will always be a non-negative integer.

```py
def breakChocolate(n, m):
    if n==0 or m==0: return 0
    if n==1 and m == 1: return 0
    return n*m - 1
```

```py
def breakChocolate(n, m):
    return max(n*m-1,0)
```

