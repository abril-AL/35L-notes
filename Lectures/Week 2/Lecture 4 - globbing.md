19th of January

### Sidenote - Software Evolution and Portability
- users with diff machines, browsers, stc
- software standards
	- mostly abt software interface
	- ex. interface for a class (public) vs implementation ( private )
	- ex browser should fit certain standards
	- major standard: POSIX
		- portable OS interface X
		- standard interface for Linux stc.
		- subset of GNU Linux , mac os, etc
		- like the intersection of all APIs
		- mutually agreed upon common ground
		- who keeps POSIX up ?
			- The Open Group
		- lib stuff ????
- POSIX shell standard


### More on Extended Regular Expressions (EREs)
- if u have any pattern P , 
	- P{3,5} - repeat the pattern anywhere from 3 to 5 times
		  - same as PPP(PP?)
	- P{,5} , P{3,}
	- `[[:alpha:]]` - matches any alpha character
		- same as `[a-zA-Z]` , but international
		- could also put digit, etc. , derived from C functions -> <ctype.h>
		- can add special characters too
			- ex. `[[:alpha:]_-]` for underscore and dash
	- `)` - matches just itself
	- `a(b*c)d\1` 
		- `\1` - will match the 1st parenthesized subexpression, matches what that RE matche with
		- `\2` - matches 2nd, and so on up to 9

### Shell Scripting
- Control structures
	- if u have any cmd , !cmd is also a command
		- means exactly the same as what the cmd means, *but* exceeds even if the cmd fails and vice versa
		- exit status is 0 if cmd's exit statues is nonzero, nonzero otherwise ( if exit status of cmd is zero )
			- `while !grep x file  do   process file   done`
	- if u have 2 commands, you can do: cmd1 && cmd2
		- same as if cmd1, then run cmd2
		- same as if and else `if cmd1 then cmd2 else false fi`
	- can do cmd || cmd2 
	- note: shell has no reserved words, could name a var `if` or a command called `if`
		- goal is to prevent limiting number of keywords in future
		- shell recognizing keywords - if follows another keywords its treated like a keyword as well, ex `if if [...] fi fi`
	- other groupings
		- can put as many commands as u want, can get complicated
		- sometimes want to put mult commands where only one is wanted:
			- `cmd1 && cmd2` => want to execute 2 commands but ignore exit status of first => `{cmd1a;cmd1b;} && cmd2` , cmd only depends on `cmd1b exit status`
		- `{ and }` are keywords, treats whats inside like a single command
		- `{ } vs ( )`
			- `{ grep a file ; grep b file } >out && echo OK`
			- `( greo a file; grep b file ) >out && echo OK`
			- `( )` - starts subprocess and does it in subprcocess
			- `{ }` - says to not use subprocess if you dont need to, optimization if u dont want the command in seperate process
			- Why wouldn't we do the optimization ?
				- Ex. examine `{ cd subdir ...} and ( cd subdir ... )`
				- working directory belongs to the process
				- `{}` - changes the shells own directory, later commands will be relative to that change in workingf directory
				- `()` - shells  directry is not affected, isolated from shell, safer but slower

### Globbing
- Glob
	- like a regexp, but simpler syntax
- Basics
	- ordinary characters match themselves
	- concatination like regexp
- Special Characters
	- `*` - matches any number of chars, vs `.*` regexp
	- `?` - any single char, vs *`.`* regexp
	- `[ ]` - matches single char in a set
		- almost the same as in regexp
		- **but** negation in a `!` instead of `^` 
- Shell uses globs to expand file names
	- `ex *-h-* `to match any file name in glob with -h- in it
	- issue: * doesnt match file with a leading `.` in the name, `echo .*` would show them
 - Examples
	- `ab*c+d?` is not valid bc star means something else and no + or ? operator
	- also, no `^ or $` bc it cant match just part of a file name
	- Trouble - name file starting with `-` , ex `-x-.c`
		- if we then `grep foo *.c` , shell expansion considers - as invalid option
		- ![[Pasted image 20230124182450.png]]
		- another example, have a file named `-rf` , `remove -rf` wont delete that file
		- Fix: `--` or `./`
			- We can say rm ./-rf and it’ll not treat the - as a option 
			- Could also do rm -- (everything after – is treated as a file name). run rm -- -x-.c
- Variable Expansions
	- `x='abd'` , `echo $x` expands it
	- also note: `$#` gives the number of arguments
	- Other ways to use variables
		- `${x}` -> could then do `${x}y` to echo out contents of x folloed by `y`, whereas `echo $xy` echos the content of xy ( the file )
		- `${x-default}` expands to x if x exists, otherwise to its default
		- `${x;-default}` - expands to val of x if its *nonempty*, otherwise to default
		- `${x+set}` - expands to word set if x is set ( if x has a value it is set to)
		- `${x;-nonempty}` - expands to the string nonempty if x has a nonempty value, expands to empty string otherwise
		- `{x?}` - expands to x if x is set, otherwise shell will error out
			- ex `echo ${PATH?}`
		- `${x=default}` - expands to x if x exists, otherwise *sets it* to default
		- `${x;?default}`
		- `${x;=default}`
	- uninitialize variables in shell `$unset x`
	- export a variable `$export x`, made visible to sub commands and sub processes, anything the shell stars is visible to anything the shell starts up, ex PATH
- Built in variables
	- `$$` - expands to process ID of the shell itself
	- `$!` - process ID of the last background process
		- create background processes with `$ cmd&`
	- what can we do w process IDs ?
		- kill process `kill $$`
			- wont kill, signals to shell to kill itself, but shell thinks its too important so its "says no"
			- option 9, `kill -9 $$` kill command will definitely kill, wont be ignored
	- `$PATH`
	- `$HOME`
	- `$PWD` - full name of current working directory

### Tilde Expansion
- Phase 2
- `~` -> expands to home directory
- `~eggert` -> expands to eggerts home directory
- in sequence
	- cant do 

### Command Substitution
- Phase 3
- `$(cmd)` , shell stoips and runs the command and splices it in
- ex. `grep abc $(find* -name '*.c')` 
	- Take all the file name with output, recursively, and then search for ‘abc’ in those files
	- different from`grep abc *.c`
		- bc this only looks in the current directory. Above looks in any directory.

### Arithmetic Expansion
- Phase 4
- You can write stuff like: ((x+5)) - looks up the value  of x and adds 5 to it
- Shell is a terrible place to do arithmetic, but you can if you need to

### Field Splitting
- Phase 5 - after all these expansion
- Shell take stuff we already done and breaks into further pieces
- Ex. `x = 'a b c *.c' ; grep foo $x` 
	- first turns into `grep foo a b c *.c` - `a d c *.c` is one "thing"
	- then after field splitting, they are seperated as seperate arguments
- Interfield Separator 
	- set of characters that seperate fields
	- IFS by default contain space, tab, and newline
	-  if u change IFS, be careful what you put in it

### File name Expansion - globbing
- Phase 6
-  Take all the glob and expand them. (we’ve already talked about this)

### Redirection
- Phase 7
- Recall what weve covered, like `< , > , >> , 2>> etc`
- look at our arguments and redirect

Then u do stuff like pipe or control structures built on top of them

#### One last thing - Package up our stuff
- There is a standard way in C++ to hook together code we haven’t talked about this in shell. There’s two ways to do it.
- How do you package a bunch of commands so that other people can run it.
- Could create a script, create a file and make it executable chmod a+x file (set all executable bits).
- Tell OS you want it to be executable by putting 
```
#!/bin/bash at the top of the file.
```

```
#!/bin/sh
grep "$1" ${
find "$2" -print
}
```


Other way: shell functions. Can write a function
```
f() {
	grep '$1' ${ find ....}
}
```
- can call it by saying f(a b), a is $1 , b is $2
- Originally shell didn’t have functions, nowadays its popular to use functions, in scripts.


