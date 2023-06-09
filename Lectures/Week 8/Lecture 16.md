# Debugging ( cont )
- (gdb) p exit(1) 
	- to compute it must take control
	- runs that it in place of regular machine code, so exits program
- need to watch out for optimization
	1)order of execution 
	- ex gcc -O2 foo.c
	- not the same as what u wrote
	- might jump forward mult lines even if u want single step - or even jump backwards
	- machine instructions out of order, compiler is allowed to 
	- "as-if" rule, compiler can generate any code whos behavior is as if it did the obvious
2) Variables optimized away 
	- need to know language, and the next level down
- buggy program (or compiler)
	- probably not portable
- (gdb) target
	- you run gdb on say x86-64, but program runs on some other architecture - on another machine or emulator
	- target tells it what architecture it should be assuming the other machine/emulator is
	- not limited to debugging on your machine
	- common for internet of things programs (ex. thermostat, embedded applications)

- Reverse Execution
`t ----------------------------------------------->
- ^start          ^bug triggered               ^failure symptoms
	- reproduce the test case, short run, just rerun the program
	- what if running takes a long time or test case is hard to reproduce
	- program backs up instead of going forward
	- cant tell CPU to go backward/undo
	- what gdb does is track each state of the program, what memory looked like at each point, support is expensive because its constantly saving enough of the state to undo it
	- Checkpoint
		- (gdb) checkpoint - save program state
		- (gdb) restart ID - starts from the checkpoint 
		- good idea for own applications, save state in case of crashes and such, doesnt have to be only when debugging
- (gdb) watch a+b`*`c
	- put a "breakpoint" at the point when (a+b`*`c) changes
	- can be done vs single stepping (slow) 
	- ex. watch a pointer, if it becomes null (bug)
		- (gdb) watch p
	- x86-64 has hardware support for watching, up to four locations in memory 
	- too many points will make it much slower
- Catchpoint
	- stop the program if it throws an exception
- Debugging review
	- recall - try not to do it
		- static checking
		- compiler runtime flags
		- dont just jump to gdb or whatever debugger
	- test cases
		- big deal, can reproduce problem
		- TTD - test driven development  
			- write test cases first
			- tests more important than the code...
		- Use a better platform
			- ex lots of subscript errors in C++, might want to use Java, more reliable software
			- ex Rust, has similar checking, like how it checks for null ptrs
		- Other ways to avoid debugging, in case we dont have same flexibilty
		- Defensive programming
			- like driving, drive safe bc we assume others are reckless - assume other authors are creating errors/code that doesnt work
			- Runtime Checking
				- `gcc -fsanitize=undefined`
				- can put in your own checks
			- Traces and Logs
				- program prints out stuff to log important things it does, helps with degugging later 
			- Assertions
				- `#include <assert.h> ... assert(i<n)`
				- reliable crashes
				- production mode code shouldnt do checks: `gcc -DNDEGUB`
			- Exception Handling
			- Barricades
				- have external world + program internals
				- internals are a clean trustworthy data structure
				- outside world full at bad stuff trying to break program when sending requests for things like invald pointers, out of range etc
				- barricade will only pass good requests - robust 
			- Interpreters
				- fancy way of barricades
				- take code you dont entirely trust
				- dont just run it, you write a program that interprets it but avoids it doing anything bad 
				- only need software
			- Virtual Machine
				- at the hardware level
				- stops if program does anything dangerous

## Git
- Implementing git - idea is to pull together several things we've seen - version control, file systems, shell commands, debugging
- Git internals
	- data structures that persist - on permanent storage
	- plumbing vs porcelain
		- plumbing - data structures + low level commands
			- key part we want to understand
			- live in `project/.git/smthn`
		- porcelain - git commit
	- what's in `.git`
		- metadata about working files
		- branches directory - empty even though we have a branch, used to contain file for each branch and info on them, now its present elsewhere (backwards compatibility)
		- config file  - standard format, categories in brackets w pairs variable  + value, can edit this file by hand and edit it, but mistakes lead to bad configuratin, instead can use `git config` as a barricade for changing config 
		- description file - placeholder, Gitweb puts it on web w nice format, obsolete file
		- HEAD file - tells what the defaut brach is , tracks where you are
		- Hooks directory - collections of callbacks
			- extra checking, say u do a commitm itll do its own commits but at some point run a shell command called pre-commit (invoked before any commit ) used to be pickier about what commits are allowed, force a way of usage of the repo (ex, prohibit merges), better trust these scripts (bugs or traps)
		- index file - index = your plans for the next commit, but its a large file even if no changes large binary representation of what the project ill look like, starts a large data structure with not much in ii, makes room for it
		- info directory
			- exclude - blobs of shell filename patterns it will ignore these files, like git ignore, but gitignore is a working file (can share with other devs), here its private
		- logs directory
			- records where branch tips are
			- log of changes to branches
			- 2nd order history ( of the repo not the working files )
		- objects
			- most important - records objects
			- git repository contains object data base
			- contains pointers to these objects, break up history into a data structure 
			- 2 hex digit directories - inside os another 40 digit hex number
			- every object identified by checksum (in name?)
	- Note for config file
		- ex, say we have a parent repository 
		- devs with downsteam repos, standardized somw sort of content checking turned on in config file
		- when we clone repository, then the clone has its own simple config not set up right 
		- can add a shell script to fix this and setup config
			- emacs has `autogen.sh` 
		- barricade bw local and remoe repos