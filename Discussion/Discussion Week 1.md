Jan 13

## TA Slides
Practice Problems
1) translate 771 -> symbolic mode (permissions)
	1) 6 -> 110 -> rw-
	2) 7 -> 111 -> rwx
		1) 
	3) 1 -> 001 -> --x
2) add user write permission to the file `test.txt`
	1) `chmod _options_ _permissions_ _file name_`
	2) Soln: `chmod +w test.txt` ( to all )
	3) OR: `chmod u+w test.txt`( just to user )
3) What does `chmod +x test.txt`
	1) adds execution permssions, (to all?)

- scp command
	- run locally

- File Encodings
	- ex. download big endian on little endian

### I/O ( and error ) redirect/append
- shell cmds might need input or output to / from file
- use `<` or `>` to tell where shell should get input/ put output
#### Input  - stdin
-  redirect contents of file to standard input
	-  `cmd < file`  
- redirect single line of text into stdin
	- `cmd <<< "string"`
- redirect n lines to stding
	- `cmd <<- EOL line1 line12 EOL` ( is actual lines in shell..teehee )
#### Output and error - stdout / stderr
- redirect stdoutput
	- `cmd > file` , or same as `cmd 1> file`
- append output of a command
	-`cmd >>file` append stdout to file
- redirect standard error
	- `cmd 2>file`  - redirects errors to file, 2 is the file descriptor for redirect standard error
- append stderror
	-  `cmd 2>> file` append stderr to file
- standard output / error to *one* file 
	- capture *all* output
	- `cmd >file 2>&1` - not the best way...
		- writes output to file
		- error messages (2) redirected (>) to standard output file (&1)
	-  `cmd &>file` - redirect
		- to same file ( overriddes, doesnt append i think )
	- `cmd &>>file` - append
		- append standard output and error to a single file
- Discard Output
	- `cmd > /dev/null`
- Note: redirection order matters
		- `cmd > file 2>&1 `=/= `cmd 2>&1 > file`

#### Using pipes `|`
- output of cmd1 as the input of cmd2
	- `cmd1 | cmd2`
	- ex. `echo 1234 | tee /tmp/1234.txt`
		- result `1234`
		- `cat /tmp/1234.txt` , result `1234`
		- here outpur of `echo 1234` was input for tee cmd
		- ( tee cmd grabs stdin and writes it to stdout and a file, here its 1234.txt )


## LA Worksheet

- 1echo "woof" < dog
	- redirect woof into dog ( beginning )
	- dog doesnt need to exist yet
- 