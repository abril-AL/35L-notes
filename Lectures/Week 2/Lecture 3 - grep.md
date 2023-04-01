17th of January '23

# Navigating through file system
- File names
	- file name components + slashes in between
	- file name component
		- any string w no slashes and no nulls
		- something we look up in the directory
	- slashes
		- /
	- namei("usr/name/bin")  -  OS does this, no the shell
		- starts at beggining of the file, location f
		- if f[0] == / 
			- p = root directory's ID; f++
		- else 
			- p = current directory's ID
		- will walk through name left to right
		- p is like a local  pointer into the file system
```
for each file name component
	p = look it up in dir that p points at
	//that is, set p = to what p points to 
	if its a symbolic link
		if it begins with '/' 
			throw away prefix and start over
		else
			splice the contents of the symlinkinto the filename we are currently looking at
```
-  name i and links
	- hardlinks work like namei()
	- soft links do similarly, but it splices symbolic links into original string
		- if it begins with a "/", it replaces the prefix of original string passed through
	- shell automatically expands some strings when it runs a program, this is shell
		- need to keep track of difference b/w filename manipulation done by prgrams and what happens when file names are given to OS
- more namei() quirks
	- suppose we have several slashes
		- treated like one slash
	- suppose slash at the end	
		- file must be a directory
		- otherwise an error

# grep
- `gp` global print 
- `g/xyz/` print all lines containing match for xyz
	- `g/regular-expression/p`
- software tools philosophy 
	- identify small components of system
	- tool for small task
	- break app into reusable pieces that are easily composable
- grep is a single command
	- `$grep 'abc' `
		- run grep, give it a regular expression, a string, etc
		- note: this cmd has input/output on stdin/stdout
	- Use: search on other files in the system
		- `$ grep 'abc' fo.h bar.y
			- prints any line in fo.h or bar.hthat contains  the pattern 'abc'
	- `-n`  flag tells it to also print line numbers
	- `-l` print file names
	- `-v` invert sence of matching
	- `-i` ignore cases
	- how do i use grep?
		- `grep --h`
			- help, short vers. of all options
		- could also open in the manual
			- `C-h i C-s grep C-s C-s RET`
### Regular Expressions
- What can be included in the string ?
	- simplest =>"", empty string
		- matches everything
	- 2nd simplest => "x" single charachter, matches itself only
	- 3rd simplest => pq , p is a pattern q is another, as long as youd like
		- anything P matches followed by what q matches
		- concatenation of 2 regular expressions
	- `[aeiou] ` bracket expression
		- anything that matches a or e or i or ...
		- whatever is in those brackets
		- various shorthands
			- `[a-z]` any withing a through z
			- `[a-z0-9]` any a through 9
			- `[@-~]` works bc "~" is less than "@" in ascii value
		- What if we want "-" in there?
			- add it at the end => `[a-z0-9-]`
		- What about any special character
			- `[a-z0-9@/;]` go at the end
		- What if we want an open square bracket
			- `[[a-z]` or `[a-z@!-[]`
		- What about closed square bracket
			- `[]a-z]` 
			- work bc we dont allow `[]` to be syntax for 
			- ONLY works at the beginning
		- What about for exclusion with brackets
			- `[^a-z]`  - any letter that isnt a-z 
			- `[^]a-z]` - negate anything that isnt an ending bracket or a-z
		- can i have inclusion and negation in same reg exp ?
			- no, have to choose 1 or the other

## Quoting
- common for one program to generate stuff thats sysntax for another program - want compatability
	- want shell and grep to be on the same "page"
- So what about if we want to pass: `['"\]`  ?
	- shell will expect you to finish the quote	
	- here shell and grep use different syntax
		- for shell, three types
			- ![[Pasted image 20230119152725.png]]
			- 1 with backslash `\char` 
				- can escape them, theyre not special anymore ( to shell )
				- ex `\'`=>`'` or `\"`=>`"` or `\\`=> `\`
			- 2 with apostrophes
				- take all chars in apostophes and treat as a single word, 
				- limitation, no apostrophes
			- 3 inside double quotes
				- except special characters: `$ \` ` 
				- 
- So, to bypass the shell and give grep `['"\]` :
	- 1 turn into `\[\'\"\\\]`
		- escape shell for each char with \
	- 2 apostrophes  ![[Pasted image 20230119152750.png]]
		- escape with ''\\''  - backslash quoting just for the apostrophe
	- 3 double quotes 
		- `"['\"\\]"`
-  **`.`**  any single char  (other than newline)
-  **^** match the start of the line
	- ex `^a[aeiou]z$`
- `$` match the end of the line
- `P*`  zero or more instances of P, union of 
	- ex `abc*d`  -> abd , abcd , abccd , abcccd
		- !!!!!!!check on this
	- where P is an expression
- ``\(P\)`` just matches P itself
	- ex `\(abc\)*d`

## grep extended regular syntax ERE
- more power in regular expressions at diff performance
	- were previously using basic reg. exp. BRE
- have to tell grep your using it
	- ex `grep P` => `grep -E P`
- `(P) = P`, wouldnt need back slashes like BRE
- What can ERE do that BRE cant?
	- Disjunction
		- `P|Q` anything P or Q matches
			- `grep -E 'ac*c | de*f'`
			- like: `p or q`
	- P or emty
		- `P?`  P or empty 
		- like: (P|) but more convenient

- Practice
	- Find anywhere where we multipliedin a file
	- `grep '\*' *.h *.c`
		- too many false positives, like comments
	- want all starts that are not preceeded by a slash
		- `grep '[^/]*|*[*/]' *.h *.c` 
			- every line with no slash before star, and no slash after the star
			- close, but will still match comments bc they have a space after
		- `grep '[^/]\*[*/]' *.h *.c` 
			- want a start with not a slash before or after
			- close.. but wont match if line ends
		- `grep -E '[^/]\*([^/]|$)'` 
			- even closer.. but what if star is at geggining of line
		- 
	- Solution
		-  `grep -E '(^|[^/])\*([^/]|$)' *.h *.c`  
			- match either the beggining of the line or star with no slash before it and no /after or match empty line after it
		- technically still an approximation, can have * within comments
	- Easier way to keep track of regexp?
		- shell operations keep track
		- SoL = `'(^[^/])'`
		- EoL = `'([^/]|$)'`
		- `grep -E "$ SoL\\*EoL"`
			- had to use "" marks bc of special chars specific to "" vs ' '
- DIY and operator
	- `grep 'A' *.c | grep 'B'`
		- pipe result of A through B
		- still an approximation

# Shell Expansion 
- when we type into shell, the shell takes the tokens and expands it
	- what is executed isnt exactly what we typed
- variables in shell are expanded by typing
- `$?` exit status of the last command
- `$1 $2 $3 ... $n` - arguments that were passed to the shell
- `$0` - expands to the command name
- `$*` all of those ^ ( besides $0 ) one after another
- can do it ourselves
	- ex `> x = 'ab c' > echo $x  = > echo ab c`
- exceptions: `"$@" $* and "${name[@]} and ${name[*]} `
- `#` comment in the shell
