### More scripting Languages
- We'll see:
	- shell
	- emacs lisp / elisp
	- python
	- later more, javascript etc.
- Theres more to software construciton than learning diff languages
- Key takeaway, there are different attitudes towards building software, as seen in diff languages
	- shell - invokes other program, thin layer around other programs that do the real work
	- elisp - embedded language, part of a larger application, "extention language" 
	- python - libraries + object oriented,
- Build a large application with small components in a flexible way

### Background
- Lisp 
	- oldest language well do at UCLA , 1950s...
	- why/use? its ideas percolated into other new stuff, good ideas, **core language**
	- ex python takes from elisp features
	- common for AI based on things like logic
	- picked for emacs bc creator used it
### Basics of Lisp
- Data Types
	- numbers, float point numers
		- C-j evaluates expression
	- symbols
		- data w a name, nothing else
		- "atom" - atomic value
		- like a dot in memory
		- 
	- strings
		- `"an\nc"` 
		- can cross line boundaries
	- **Pair
		- piece of memory with two parts
		- pair acts like a pointer to the pair
		- represented by address
		- can chain pairs together
			- second element of each pair is the address of the next pair in the list
		- 2 words in memory
		- everything in emacs is same size
		- 1st is name , second is value
- What differs is how we build stuff out of these basic items
	- List
		- a bunch of pairs chained together
		- the end of the list, `nil` is the special symbol to represent the end of the 
		- ex `( e a g )` emacs recognizes it as a list
		- list always build from pairs
	- Calling functions looks like a list, they use the same format, good for ai where output is a program
- `C-j` tells emacs to treat it like code, not data
	- `'` tells compiler the next thing is data not code
		- ex. `( ret ( ( a '( f e g ) ... ) ) )`
- cons function
	- constructs/creates  a pair out of 2 elements
	- `(cons A B )` = pair `P`
- Car/Cdr - Take apart pairs 
	- `( car P )` or `( cdr P )`
	- have to pass it a pair
	- car = first element , cdr = second element, for a list its everything else in the list
	- these are fast operations
	- ex `( a - b )`
	- Singleton list
		- ex `(-6)` same as -6 + nil , `(-6 . ())`
		- length 1
		- cdr is nil
- Improper list
	- no `nil` to specify the end of the list
	- use `(a . b )`  , spaces are important here
	- `( 19 12 -3 . 7 )` 
- Differences from C++
	- Parenthesis Matter
		- in C++ we can say `x + ( a * b ) == ( a ( ( a ) * ( b ) )`
		- but this is not the case for lisp bc `(a)` is a singleton item/list, not an int
	- No overflow
		- will just take longer
		- if its too big, it will just put it somewhere else in memory
- Why do we have symbols
	- everytime you write the same symbol in different parts of the symbol, you get the same object associated
		- guarantees uniqueness 
	- but, with strings theyre not the same object, so we have the distinction
		- can have two strings w different content, different objects

Example
```
(let ((foo (cons 29 `(37 -6)))) 
	(cons (cdr foo) (car foo)))
```
- first line gives us a list of 29 to 37 linked to -6
- foo is bound to the list that contains all the numbers
- now we create a list that has cdr of foo then car of foo
- this pair of 2 numbers that isnt a list is an impromper list
- `( 37 -6 . 29 )`

### Tagged Pointers
If we're on 64 bit machine, 64 bit pointers,
For some pointer to an object, the object will have to have an adress that is a multiple of 8
So, bottom 3 bits are gonna be to the right, and since bottom 3 bits are 0, we an store extra information about whether the object is big or small
Dissambiguates the pointer 

### More Lisp 

### Special forms
If we have `( * 3 5 )`
- Name of function, then its argument
Special Form look at functions but have something else before the first word
- says, "this isnt a function its something else"
- If
	- `( if A B C )`
	- if A , then B , if not then C
	- nil = false, non-nil = true
	- can be cheaper than a function call
	- or can avoide side affects like printing
- Let
	- does bindings
	- v1 to E1 etc.. v1 evaluated to e1 when you use it in code
```
( let (( v1 E1 )
        ( v2 E2 )
	    ( vn En )
	...code...
```
- Cond
	- lisps way of doing if then else
	- looks through until it finds something that returns true ( not nil )
```
cond (E1 E2) 
	 (E3 E4)
	 ...
	 (En-1 En)
```
- And
	- like `&&`
	- if one yields nil, its done
	- if they all yield nonnil, it evaluates value of the last one
```
( and E1 ... En )
```
- Or
	- evals left to right until it yields a nonnil and yields it
	- similar to `and` 
- Defun
	- special
	- define a function
```
( defun f ( a b ) )
	( ) ; code body
	( ) ; code body
	( ) )
	
;;example:

;; note:
;; message is built in print function
;; % is formatting string, subs a and c in 

( defun chatty-* ( a b ) 
	( message "multiplying %s by %s" a b )   
	( * a b ) )
;; then press C-j to eval the defun
;; now elisp has this func

;; can call out func like: 
( chatty -* 35 -27 )
-945
```
- theres a message buffer that holds all the messages emacs gives to us
- when u eval a defun, it gives you the symbol for that function
- for chatty-* , the message will go to that buffer
Ex of `cond` ,  if-then-else
```
( cond (( = 5 7 )  )
	(( < 0 1 -5 ) 'lessthan )
	(( = 0 0 ) 'aaah ) )  C-j
;;prints 'aaah' 
```
- Defvar
```
( defvar x 27 )
```


- Elisp is an extesion of emacs
	- there are functions built in that take no arguments like `(current-buffer)` 
	- `(current-buffer)` 
		- takes no arguments
		- will return an object that is the current buffer
	- what we can do is:
		- `( defvar buf ( current-buffer) ) `
		- this makes a binding between `buf` and the current buffer
	- Now, we can make a list with the current buffer followed by other objects
		- `( defvar buf1 (other-buffer) )` ; gives other buffer on window
	- Theres also other funtions that deal with buffers
		- `(switch-tobuffer buf1)` ; switches to the message buffer
		- `(kill-buffer buf1)` ; kills off other buffer, now we dont have messages that we had before
	- these functions are useful for when we want to change how emacs behaves
	- `(forward-char 10)`; moves forward the # of chars you specify
- Now we can execute code inside of emacs.
- We can also view code inside of emacs too.
- If you do: `C-h b` - this gives us a list of keybindings, they are mappings between what you type, and what emacs will execute when you type it.
- So you can search through keybindings and find for example C-g which is bound to keyboard-quit. 
	- When you type C-g, it funs function called (keyboard-quit) 
	- if you say `C-h k C-g` it describes the keypress.  
-  By convention first thing in a function is a string - documentation. 
- First call is (interactive) - tells emacs that you should be able to invoke this function at the top level by using a keymap. 
- ``(setq a b)`` - set a equal to b’s value.
	- Basically assignment.
	- Talked a little bit about what happened in C-g function. 
- think of let as creating local variable and defvar as a global variable - you can put defvar line inside a function too.


- Goal is to be able to explore how emacs works - looking at source code. You shouldn’t know what everything does
	- if you need to find out more you can find out more. 
	- `C-h f`  lets you find more functions, and now you can explore more code. 
- Top level define is like a global variable in c++ 


- nice function to know: 
	- (point-min) - tells you minimum amount of characters you can be in the buffer. 
	- (point) It tells you where you are in the buffer. (point-max) max amount you can set point to - think of end of restriction of buffer. 
	- (count-lines (point-min) (point-max) gives us how many lines we have so far. 
	- (global-set-key) lets you set a keybinds or sequence of keybinds that binds to a function and shows in the keybinds table. 


- Goal is that you have knowledge to explore emacs right away. 
	- No one understands this project right away. 
	- Some of the people who wrote the code for emacs are literally dead. 
	- Any projects of this size is supposed to be impossible to understand 100% of the project. 
	- You need to understand enough of what you’re working with to get something done. It’s ok that you don’t understand.