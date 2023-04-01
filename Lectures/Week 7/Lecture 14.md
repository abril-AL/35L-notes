## Intro
Debugging 
- performance - RAM, CPU, power, etc.
- correctness - results match whats expected
- oftencare more abt correctness, want to improve perf witout changing behavior
- in realtime systems, theyre equals, need to be fast to be right
	- ex brake system

## GCC stuff
`__attribute__(...)`
- basic idea, whatever we put in ... is like advice to the compiler, which it could ignore
- can take code decorated with attributes and rrun on any compiler by:
```C
#ifndef __GNC___
	#define __attribute__(x)
#endif
```
- key idea: compiler can ignore it, sometimes perf or correctness advice
- example
```C
char buf[1000] = __attribute((alighned(8)));
*aligned must be pwr of 2, sometimes cant bo above 8
allocate this array, make sure address is a multiple of 8
(long)&buf %8 == 0 should be true
this attribute might increase perf, otherwise should run the with the same behavior
```
- example
```C
void f(void) __attribute__((cold));
function that is so rarely executed, instr ptr never rly goes there
can take machine code for cold functions and put them in "corner", that is, sit in RAM, not instruction cache
void f(void) __attribute__((cold));
advises that the this function is used alot, puts it closer (cache)
```
- to ignore attributes, say `gcc -o0` , no optimizations
```C
if (x<0)
	f();
turns into code at machine level that is a branch
can also apply hot/cold to single instructions
could put f() in hot/cold section
if f() cold, we know ( and compiler knows ) x usuallly > 0
```
- main idea: can help compiler optimize
- drawback, we have to go through program and decide, extra maintenance step for developer
- Profiling
	- look at all the machine instructions
	- check "temperature" of all the code
	- can see from graph what we want to mark as hot/cold
	- `gcc --coverage`, adds extra instructions that will update the profile, at the end will have graph of profile
		- note: can be input dependent
		- important to have good test cases to test performance, try to tell compiler what typical input looks like, typically lots of data
		- makes it much slower
	- another property: correctness 
		- what if an area is hot/cold but not suppossed to be
		- ex. code not used/called, or bad test cases
		- test your test cases
- Correctness issues
	- **static checking** - check at compile time
		- compiler checks program before you run
		- know bugs cant happen at runtime
		- ex. `static.assert(0<n)` or any expression `E` that only involves constants, `E` must be a constant expression
		- useful in larger programs, document assumptions we make in our code, other ppl can see why we write things a certain way
		- ex. `static.assert(INTMAX < LONGMAX) `, assume integers larger than longs in program, if someone compiles where this is not true, compiler yells at them, cant misuse the code
		- relatively new feature (C++17)
		- different from `<assert.h>'s assert'`, which is a runtime check, more flexible
	- More static checking with warning flages (gcc flags)
		- `gcc -W` enables/disables particular warnings, depending on what we care about
		- `-Wall` turn on all warnings (...that gcc thinks you want/useful warnings for avg developer)
		- examples
```C
gcc -Wcomment

/* bad 
exit(27)...code...
/* comment */
-C sees this as all a comment, ignores code
catch dumb mistakes, compiler tries to save you from these mistakes
- this is implied by Wall
------------------------------------------------------
gcc -Wparentheses

return a<<b+c;
-warns you about  precedence stuff, in case dev assumed the opossite
-can avoid the warning by adding parentheses
------------------------------------------------------
gcc -Waddress
- warns abt use of address of abjects that are probably wrong
char *p = f(x); if (p=="abc") return 27; (???)
-----------------------------------------------------
gcc -Wstrict-aliasing
long l = -27; int *p = ( int*)(&l);
*p =???
???
store 0 over half the long, 

- aliasing: two names for same variable
- *p and l are aliases ? confuses compiler
- should avoid aliasing, gives this warning
------------------------------------------------------
gcc -Wmaybe-uninitialized
- warned when u access variable that might be uninitialized
int n
??? foo(void){
	int i;
	if (n<0)
		i = n*n;
	n=g();
	if (n<0)
		return i*r;//worst case, this is uninit 
}
- what if n was changed in g() call

fix, add bool b = n<0 at beginning, replace checks with b, dont care if n changes later
```

- more attribute examples
	- can get more complicated
```C
int hash (char *, ptrdiff_t)
__attribute__((pure,access(read_only,1)))
- looks at block of memory until offset
- att- no changes to user visible storage
int x = p[5]
int a = hash(p,12)
int b = hash(p,12)
int y = p[5]
means that a must equal b, and x equals y
-----------------------------------------------------------
int square(int)__attribute__((const))
- value independent of state of machine
- return square of argument
- pure + doesnt depend on user visible storage, note even going to load
- can be optimized more than a pure function


```
- these will be standardized in C24 - `[[reproducable]]` and `[[unsequenseable]]`
```C
void *my_alloc(ptrdiff_t)
__attribute__((alloc_size(1),malloc(free,1),returns_nonull));
- want it to tell compiler it acts like a memory allocator
- compiler uses this info to give better diagnostics or better code
-----------------------------------------------------------
_Noneturm void exit(int);
doesnt return at all

exit(0)
if(n<0)
	return 9;
```


## Runtime Checking
```C
for (i=m; i<=n;i++)
	x+=f(i);
executes max(n-m+1,0) times 
(assuming f doesnt change i)
compiler can generate different code that might be faster
??
what if n is INTMAX? will wrap around and loop forever
this loops behavior is undefined if i is INTMAX
check at runtime:
if (n==INTMAX) crash(); //not practical

gcc -fsanitize=undefined
- put in a runtime check everytime i add to an int and crash if theres overflow, dont have to modify source code

another set of undefined bahavior for address related undefined bahavior:
gcc -fsanitize=address

gcc -fsanitize=leak
- check for memory leaks

gcc -fsanitize=thread
- tries to catch race conditions
```
- These flags hurt performance
	- don't want to use ALL the time
	- excellent for when debugging, catch early
- These use heuristics, *not perfect*

# How to Debug
- Common sense advice
	- common mistake - guess at random where bug is
	- Better:
		1) stabilize the failure - find a test case that makes the program *not* work / fail, not always obvious
		2) locate failure source - find point of failure
	- put in print statements to find out more about the failure
		- not always *practical*
	- use a debugger
		- linux -> gdb , `gdb myproject` 
			- can be unmodified, does printing for u+
			- helpful to compile with `-g` on gcc to put info in executable that will help the debugger, ex like the names of local variables, only downside is it makes executable slower
## GDB
- have control over how the program starts
```gdb
(gdb) set cmd /usr
(gdb) set env TZ America/Chicago
(gdb) set disable-randomization on/off
 
(gdb) r -c foo <bar >baz
(gdb) r

			(process ID) gdb takes over that process
			cant debug programs that already started
(gdb) attach 91726 
```
- how to stop things
	- `ctrl-c` stop program and gdb regains control
	- or, before you run, tell gdb where to stop/wait
		- `(gdb) b foo `, plant breakpoint at start of that function
		- `(gdb) info break` list breakpoints
		- `(gdb) del 19` delete breakpoint #19
		- can continue with `(gdb) con ` or `c`
	- `(gdb) step` put a breapoint on next line and go to it
	- `(gdb) stepi` next machine instruction
	- `(gdb) next` dont stop at function calls, just execute it
	- `(gdb) fin ` finish current function
	- Examine current state`
		- `(gdb) bt `- backtrace, line num, who called this and so on, limited summary of history of program, how you got to where you are
		- `(gdb) p n` - print the value n, can put any expression there, like `n*5` or `sqrt(n)` or `p exit(1)` or `n=5` 
			- have very strong control, src code can become "irrelevant"