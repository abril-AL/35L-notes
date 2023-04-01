# Python
- more like C++
- emacs - you write script inside
	- different from python

## Python History
- unix was invented (sh + other )  first
	- other inc other little languages good at doing some task
		- sed, grep, etc
	- hook all together by shell
	- downside: have to learn those small languages
- In reaction, perl was created
	- dont have to learn other small lang
	- has own regexp
	- code in a freeform way
- Similarly, Amsterdam replaced Basic with ABC
	- higher level
	- feature of ABC, had to indent properly
	- failed
- Ideas of simplicating are seen in python 
	- inspiration - teaching language 
- Python is not C++, C++ more prone to crashing or wrong behavior with no crash

- Python Qualities
	- interpretive language
	- runtime checking
	- wants to tell you the problem 
	- Syntax is unusual
		- relies on indentation  
		- triple singele-quote string can traverse lines


- Whats going on 
	- Python Objects - what we compute with
	- every python value is an object ( not true of C++ )
	- and, has:
		- an identity , like its address 
		- a type , type of object
		- a value '
	- identity and type is there forever, cant change them
	- value is mutable, can change if object is mutable
		- ex. cant change a strings value
	- associated with an object are:
		- attributes
			- object x -> x.a is an attribute
		- methods
			- a.m(1,x,z)
		- can decide dynamically 

- Built in functions
	- `id(x)`
		- returns x's identity as an integer
		- cant go back and get original identity from id
	- `type(x)` 
		- given object x, we get type
		- comes from flag in memory
	- `a is b `
		- only compared identity
		- very fast operation
	- ` a==b`
		- look into objects and checks if same
		- can be expensiveq
		- popular bc we care abt behavior
	- `isinstance(x,y)`
		- true is object x is an instance of class y 
		- like type checking
		- reasonably cheap


- Python success
	- good set of built in types
		- 1) NoneType - value that isnt there, like null
		- 2) Numbers
			- integers
			- floats
			- complex
			- booleans
		- 3) Sequences
			- sequences of objects
			- several types..
		- 4)  Mapping Types
			- dictionaries
		- 5) callables (?)
			- can use as if they were functions
			- classes are cullable
		- 6) Everything else
			- internal types
			- wont discuss


- Sequences
	- if we have S , we can find ith element , `[i]`
		- `[-1]` last elem `[-2]` second to last , negative to walk backwards
		- i does not need to be >= 0, really , must be  - len (S) <= i <= len ( S )
		- no overrunning problems like in C++
	- len( S ) returns length of the sequence
	- `S[i:j]` - get subsequence from i up to j exclusive
		- get a part of the array
		- `S[i:]` - goes to end by default
		- `S[:j]` - default 0 , starts from beginning
	- `min(S) , max(S)`
	- `list(S)` - returns ???
	- sequences are mutable
		- `s[i] = v` change ith value
		- `s[i:j] = s1`  change sequence by replacing subseqeunce with something else (s1)
		- `del s[i] ` or `del s[i:j]` to delete stuff

- Lists ( mutable sequences )
	- `l.append(v)`  O(1) operation
		- same as `l[len(l):] = [v]` but cleaner
		- like pushing an item 
	- `l.extend(S)`
	- `l.insert(i,v)`
		- similar to `s[i:j] = s1`
	- `l.pop(i)`
		- return what we remove
		- `l.pop() = l.pop(-1)`
	- `l.reverse(s)`
	- `l.sort(s)`

- Mapping , dictionary
	- `{}` - empty dict
	- `d = { 'a':1 , 'b':2, 'c':3}` 
		- keys: a b and c 
		- mapped to 1 2 and 3
	- lookups, index w key
		- `d['b']`
	- Operations
		- subscripting
			- `d['a']
			- `d['a'] = 9
			- `d["new"] = 12`
			- `d[19] = "def"`
		- keys must be immutable
			- llist is not a valid key
		- `d.has-key('b')`
		- `d.get('b') ` no runtime errors if dni
		- `d.get(k,v)` if no val for k, return v
		- `del d[k]` remove kth element
	- easy to write loops that 

- Callables
	- like functions
```python
def f(x,y)
	return x+y+1

//same as:

f = lambda x,y: x+y+1

//g is same function now
g = f

//runs same code as f
g(1,5)

//function is an object
```


- Special cases
	- func takes varying number of arguments
	- `def printf( idk(??) , * args)`
		- trailing arguments bound to a touple ( immutable sequence )
	- order
		- def arctan ( x,y )
		-  if we assign them in func, order dn matter
	- package up as a dictionary
		- `printx("abc" a=12, b=34, c=51) `
		- packaged up as in `printx(??, **args`


- Classes
	- package up methods
	- when we invoke method
	- -