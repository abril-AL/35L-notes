20th of January

## shell scripts
- run cmds w single script
- `*.sh`
- execute
	- `./ex.sh
	- need permission: `chmod +x ex.sh`
- set variable
	- `NAME = 1`
- use
	- `$NAME , $(NAME)`
	- ex. `echo $NAME`
- internal variables
	- $0-9 , $#
- temporary variables
	- x `echo ls -l`  , just outputs ls stuff
	- echo $(ls -li) , puts into temp var
		- can use $ to acess the temp var (?)
- Loop
```sh
for VARIABLE in 1 2 3 4 ... N    condition statment
do 
	cmd1
	cmd2
	cmdN
done
```
- if
```sh
if [ $1 -gt 100 ] 
then
	echo thats a large numner
	pwd
fi
```
- wildcard pattern matching
	- `*` match any character
	- `?` a single character	 
	- ex `ls test_?.txt , ls *.txt`
	- ` [ ] ` - specifies range
	- ` { } ` - OR
- Emacs Review
	- run command by name `M-x [command name]`


## Lisp
- all emacs functions written in lisp
- how to see src code of built in function  
- expressions written with prefix notation

### Elisp
- elisp vs lisp
	- elisp is a dialect of lisp specialized for emacs
- what does `nil` mean
	- false or not true
	- can also meant the "empty set/list"
- elisp file extension
	- `.el`
- what language language it falls under
	- functional something something idk

### LA worksheet ( # 2 Lisp)
- ( quote ( 1 2 3 ) )
	- not eval
	- output: ( 1 2 3 )
- '( 1 2 3 )
	- shorthand for quote
	- output: ( 1 2 3 )
- ( list ( + 1 2 ) '(+ 1 2 ) )
	- makes a list
	- evaluates ( + 1 2 ) and quotes ( + 1 2 )
	- output: ( 3 ( + 1 3 ) )
- ( cons ( + 1 2 ) '( 3 4 ) )
	- cons - like append
	- output ( 3 3 4 )
- ( + 10 ( car '( 1 2 3 ) ) )
	- car - head , cdr - tail
		- can do mult a's or d's to extend size
	- output: ( 11 )
- ( reverse ( append '( 1 2 ) '( 3 4 ) ) )
	- ( 4 3 2 1 ) 
- ( cdddar ( 1 2 3 4 5 6 7) ) ??
	- car , cdr cdr cdr cdr
	- nil bc tail of car is nil
- same but cdddr ??
	- error because we're treating it as a list
	- could fix with a quote

Problem 3
```ls
##write function is-even that takes an arg and returns whether its evem

( defun is-even ( x )
	( = 0 (% x 2 ) )
)
```