# Build Tools 
- use to build the program and ship what we actually want
- ex. text editor, compiler; set of build tools varies
- Audience of these build tools - tools we choose depends on audience
	- Developers - make programs/code, need build tools sppecific to their needs
	- Builders - dont write code, but compile existing source code for a particular platform, might not be same platform as developers, make sure code works on different platforms
	- Distributers - take the program  (compiled + linked ) and ship the prgram to the users, package them and send them
	- Installers+Configurer - put the program on their computers, configure it
- In larger software projects, will need communication between these roles
	- ex. Dev and Builder, gives them the source code and build instructions
	- metaprogram implements these build instructions, stop here, no more instructions for building beyind this
	- instructions could be in english, ex README.md, error prone, easy to misunderstand
	- better to have instructions written as a program, ex a shell script, build.sh / setup.py 

```sh
cc -c a.c
cc -c b.c
cc -c c.c
cc a.o b.o c.o -o foo
```
- inside these programs, will compile and link, easy for small programs
	- issues for getting larger:
		- how do we maintain the file as size changes
		- small changes means have to recompile and link (rebuild) from scratch, can get expensive as size of program increases
		- compiler version etc. (linker,os,...)
		- optional features
		- build script organization
		- This is slow. wait for each to compile, no parallelism
	- fixes
		- parallelism in compilation
		- common to take build instruction and put into seperate instruction file interpreted by build tool
## make
- most common
- lets you write something like a shell script but not so focued on order, so build tool can decide
```shell
// makefile
a.o:
	cc -c a.c
b.o:
	cc -c b.c
c.o:
	cc -c c.c
foo:
	cc a.o b.o c.o -o foo
```
- then run shell command $`make`, figures out what to run, can skip things
- relies on file timestamps to determine which files need to be rebuilt, and dependencies listed in the make file (can be lazy)
- issues/caution
	- file timestamps can be unreliable, clock skew on network system
	- what if a.c has `#include x.h` , a has another dependency, have to make sure dependencies are right
		- so in make, `a.o: RET TAB cc -c a.c x.h`
		- more dependencies also means slower, but correct builds
	- leaves other problems unattacked, but this is the point of build tools, why we use certain tools, make for ex was chosen to attack rebuilding and parallelism issues
- fixes parallelism problem
	- can schedule compilation parallel, `make -j10`, do up to 10 jobs in parallel
- macros with make
```make
OBJ = a.o b.o c.o 
...
foo: $(OBJ)
	cc $(OBJ) -o $@
	//$@ replaced by thing ur trying to build - foo
```
- what if we added that c.o depends on foo
	- make throws error msg about that bug
- main advantage of make is that its common/standardized, good for portability

# Low Level Programming
## C/C++
- C++ is an abstraction over C
	- first C++ compiler created C code then compiled
	- supplies classes and objects unlike C, object are more of a  primitive idea in C
		- polymorphism, inheritance, encapsulation
	- C++ programmers "know" how objects are implemented
	- C++ struct can have static data members and functions
		- C doesn't have that 
	- C++ has namespace control
	- C++ has simple overloading, two different sin  or cos functions, hard to do in C, not worth it
	- C++ has exception handling, pain in C
	- C++ has memory allocation built in by `new` operation, C does not, uses functions `malloc` and `free`, dangerous bc void pointer
	- C++ has `cin` and `cout` and `<< >> `built in, no IO built in C
	- C++ features are implementable in C, but have to do it yourself, no abstraction level
- Why would someone uses C instead of C++
	- simpler because no abstraction
	- performance
	- can be simpler to debug
## Architecture
- Architecture for a typical C environment
	- inherited from C
- how we turn code into instructions
- Compilation 
	- preprocessing - `gcc -c foo.c` transform c src code into a `.i` file, all macros expanded
	- Conversion to assembly - `gcc -S foo.i`
	- Conversion to `.o` file, object code `gcc -C foo.s`, assembly into bytes necessary to execute it
		- object file has holes in it representing things we dont know
	- Link object files together `gcc foo.o` ( default linked to c lib?)
		- fills in blanks into 
- After, can run with `???`  

# Debugging 
- many ppl thing of things like GDB and VS Code
- Alternatives to standard debuggers
	- often easier and cost-effective
	- advice: avoid debuggers like gdb
- Simple Alternatives
	- dev tools
		- print statements
		- `time` ./o.out foo , perf info
	- ops tools
		- `$ps -ef` process status, why is system so slow
		- `$ps -ej??` parents etc
		- `$top` system monitor of win etc, lists processes currently running chewing up resource  specified
		- `$kill 97286` - kill this process
		- `$strace ./o.out foo ` print system calls
			- puts a "spy" at system call level, see what the program is up to
			- `$strace cp a b` see how cp actually copies 
		- `$valgrind ./a.out foo` - runs program and looks at all instructions, slower than strace, even ordinary like load and store, 
		- attempts to find bugs due to things like invalid memory accesses, and accesses to freed storage
			- ex. `char a[10]; *p = &x[30]; *p='x'; `
			- if it's not sure, it won't report the (possible) error
- Using compiler to debug
	- `gcc -fstack-protector`, catch stage overflow bug 
	- inserts extra code, puts canary that will be checked by compiler, if canary changes, then crash
	- program gets a little slower, but safer
- Performance Bugs
	- gcc and other compilers have -o option, optimize
		- tell compiler to generate better code
		- cost, slows down compiler ( has to think more ), to get a faster executable
		- option to how much slower, and faster
			- gcc -o -o2 -o3 -o4
		- faster executables bc compiler does tricky stuff
			- ex. cache in register,
			- harder to debug bc doesn't match src code
			- ex out of order execution, hard to debug
		- bottom line - harder to debug
	- compiler options 
		- `gcc -flto `
			- link time optimizations, put into the`.o` file regular machine code, and a copy of the src code
			- slows down compilation
			- linking, gcc can see src code ( not just pieces )
			- program will run faster, but slower to compile
			- harder to debug
	- Performance/Bug improvements
		- gcc has standard function `__unreachable()`
		- no arguments, doesn't return anything
		- if its called, the compiler can execute whatever it wants, *behavior undefined*
		- how does it help performance, compiler can know things it wouldn't otherwise know, and go faster
		- 