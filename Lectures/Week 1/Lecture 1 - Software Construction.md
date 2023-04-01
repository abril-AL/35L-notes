10/10/23
# Intro/Logistics
- Software coonstruciton - part of larger idea of softaware engineering
- Management of a process
## Logistics
- 30% of grade is project - group of 5
- 13% Homework - individual , abt 6, each ~2%
	- deadlines on website
- 1.5% class participation - on piazza
- 0.5% 2 feedback surveys for LAs
- 30% Final Exam
	- open book/notes
- 20% Midterm
	- open book/notes
- suggested to print homework, questions abt hws
- deadlines - 12:55 that day
- lateness - penalty doubles every day from 1

## Homework
- Lab Part
	- could do during discussion
- Homework part - to be done afterwords, own thinking
	- should be "robust" - should not break given unusual inputs, no arbitrary limits
		- pain for c++, luckily wont be using c++ at all
- First Assignment Due 21st
	- note - citations have info, prob too long to read, need to read efficiently
	- WE need to figure it out
- Citations - need to credit others
	- DOI - like a url, but more permanent
		- https://doi.org/__DOI____
		- permanent way to cite scholarly work

## Project
- Presentation abt project



# Two Programs
## sh - shell
- An OS is complex, lots of stuff: data, programs, etc.
- shell is a little program that surrounds the OS ( C has few thousand line shell )
- shell cant know abt OS stuff, simple
- shell is an interface bw you and OS
	- ex. text string input
	- gives response
	- "real work" done by OS 
- shell is a glorified configuration language processor
![[Pasted image 20230112130949.png]]
- command lines interface ( CLI )
	- type/enter commands, and its executed
	- we still use sometimes bc they have a property that graphical interfaces dont
		- ! Theyre scriptable ! - scripting languages
		- ex. shell will solve home directory problem --> 
			- `rm -rf ~`  
			- dont have to do complicated stuff

## emacs / Emacs

### Trash pdf files with emacs
- Emacs - `M-x shell RET` 
	- M ( meta ) is alt or windows key or esc, FOR ME its Alt
	- laptop runs OS - emacs running in OS
	- cmd runs subsidiary process that runs shell
	- typing sent to emacs -> emacs sends to shell -> sends response back to emacs -> we see it on monitor
- Lets be more selective - want to see whats in the directory
	- Graphical interface ? 
	- Ctrl-x d ENT
		- Lists all files in directory - file system has tree structure from root directory to subdirectories 
		- can edit directories
	- directory starts with `d`
	- regulat file starts with `-`
	- ![[Pasted image 20230112132148.png]]
- Lets delete all those pdf files
	- `rm *.pdf`
	- or graphical - physically go through and delete each "by hand", but we want to do stuff programatically
- What if you do `rm * .pdf`  ( with space )
	* 1st, * (star) means all files, so it will delete all pdf files, dont want to do that,
	* next, it will try to delete the file `.pdf` ( doesnt exist here )
- Echo cmd
	- $ echo a b c
		- outputs arguments a b c 
	- ex. `$ echo rm * .pdf`
		- outputs every file that will be deleted ( `rm` )
	- prevents cmd from being exeuted - see what will happen w/o executing
	- Sidenote, what is the `-rf`?
		- `-r` means its okay to delete directorys, will do so *r*ecursively  
		- `-f` okay to delete read only files
		- why are they together - single letter options can be mashed together

- Trash Directory 
	- lets move all the pdf files to trash directory
	- `$mkdir trash 
	- `$ echo mv *.pdf trash`
		- make sure its what we want trash...
	- `$ mv *.pdf trash`
		- `*.pdf` moved to /trash dir
	- `mv a b`
		- if both files, renames a to b 
		- if b is a directory
			- remanes a to be /b/a
			- retains base name ( so if a is a dir, its still /b/a/ )
			- ex /trash/foo.pdf


- If we move the example pdf files to trash directory, they are still listed as if nothing changed... why ?
- Issue - doesnt match what actually happening

### Performance / Recover Issue - BUFFERS
- Three goals for our apps ( at once )
	- ( 1 ) want to recover from hardware failures
		- what if laptop dies ? want it to survive
		- what if OS crashes ? want it to survive
	- ( 2 ) want it to be fast
		- but: speed/recover goals compete...
		- recover - save often, disk or network
		- speed - disks are slow
	- ( 3 ) want it to be understandable
		- even if complicated ( million+ lines ), want to know how it works so we can reliably change or predict behavior
- Solution/Compromise
	- persistent storage -ex flash storage 
	- Best/common way is with *cache* of whats in persistent storage
		- Cache/buffer of whats in persistent storage
		- Say we type into buffer then,
			- `Ctrl+x  Ctrl-f filename RET`  - tell emacs we want to edit a file, promts for file name
			- `sample.txt` - name of file we want to edit ( if dne, will exist in cache only, but not persistent storage )
			- it saves whatever we typed into persistent storage ( buffer )
			- `Ctrl-x Ctrl-s` will save this buffer 
			- `Ctrl-x Ctrl-d` can see it listed, unsaved changes not accounted for ( ex if we typed more, but didnt `C-x C-s` to save)
		- buffers may not match whats in persistent storage

- More Commands
		- `Ctrlx Ctrl-b` list buffers - each are arrays of memory, come correspond to files, or directories, any work in buffer not saved will dissapear 
		- `$ du `- prints whats in directory

- Files -> ls -l output 
	- column 1 file type + permissions
		- `-` regular file
		- `d` directory
		- `l` symbolic link - can sub stuff to get different names for same files
		- EX: exrwxr-xr--
			- first three - user and permissions
				- exr read wrtie and execute permission
				- second three - group permissions
					- r-x	read and execute no write
				- third three - other/everybody else
					- r-- read permission only
	- column 2 ( hard ) link count
		- number of names
		- file system doesnt *have* to be tree, can be graph
		- graphs - same files can me in multiple directories, or different places in same directory
		- `$ cd ` change working directory , `$ cd /usr/bin`
			- relative to current directory 
		- `mkdir e`, `mkdir f`, `mkdir e/g` in /home/eggert
		- ![[Pasted image 20230112142102.png]]
			- `ls -l e/foo` - lists whats in foo
			- Now want link to foo
				- ln e/foo e/bar
				- foo and bar are same file in same directory
		- make another but diff directory - `ln e/g/foo f/bar` 
		- `$ cat a b c` outputs contents ( concatenate content to output )
			- say we link again: `ln e/g/foo f/baz` then do  `$ echo new text >f/baz`
			- `$ cat e/foo` outputs `new text` ( we put it there with ref to link )
		- cant create hard links to directorys - too complicated `ln . foobar`
	- column 3 owner
	- column 4 group
	- column 5 bytes
	- column 6,7,8 last modified
	- column 9 filename 
		- unless its symbolic link ( name -> contents)

- linked directorys
	- `/` root dir , `.` current dir , `..` parent dir
		- 
	- User cant create hard links to directories
	- ex. etc - where linux puts misc files, so theres a lot of subdirectories w own files/subdir
	- allows short "loops", cant create/destroy them
	- ex. column 2 is 143 for dir /etc/
		- 1 is `.` curr dir pointing to itself
		- 1 is pointer from `/` (home) to etc/ , parent to directory
		- 141 are `..` subdir in etc , `..` ,pointers to parent dir 
		- ![[Pasted image 20230112152522.png]]
- can have hard link to symbolic link
	- first `~/e$  -s ln g h`
		- have symbolic link that points to g   `h -> g`
		- if we want to see: ` $ ls -al`  ,  `-al` says to include `.`'s and '..`'s
	- `$ ln h i `
		- hard link i points to g
		- link count for symbolic link is 2
	- symbolic that doesnt start with / is relative to dir its in ( so it might not exist , its just a symbol )
		- ex. need to be in ????TO DO???
- hard vs symbolic
	- hard - another name for same file, not a seperate name
		- similar to a pointer
	- symbolic - seperate entity, contents is a file name, when we use it , OS splices and uses it to figure out what you mean
		- doesnt create pointer, creates a name, gets interpeted when we use it
		- like a "smart pointer"
/ root
. current dir
.. parent directory 








## My notes

### emacs on putty
- to open
	- login like usual
	- type `emacs RET` - will open emacs
	- FNC F10 - open menu bar 
- emacs 
	- emacs has no file tree in UI built in
	- Window
		- pane inside out emacs window ( putty in my case )
	- Frame
		- what a window is in the context of emacs
		- can open multiple - share info from same emacs process
		- will usually only use one...
	- Buffers
		- object that holds text to be displayed 
		- special buffers can be used for displaying temp info or UI elements
		- have no indication of what buffers are open until u try to switch to another buffer
		- `CTRL-x b ` brings up buffer list ??TO DO??
		- Types
			- `*`scratch`*` - like a blank sheet of paper to write notes etc, 
			- `*`messages`*` - log messages can go back and see other messges from emacs, history of msgs
		- Mode Line
			- like a status bar
			- what buffer we are in, and where ( in percent )
			- what type of file were in
			- one per window
	- Echo Area
		- line where messages get written
	-  Minibuffer 
		- where we perfrom certain operations
		- can have mult lines	
 - Commands
		- special commands
		- to run a commands, press `Alt+x` ( same as `M+x` )
			- will bring up prompt to type in
			- `TAB` will list all cmds with what ever prefix is curently there ( blank lists all )
		- Examples
			- dired - directory browser, buffer lists files and dir, like `ls`, but can select file/dir with `ENTER` and open it, or navigat the directories 
			- `calendar` - teehee
			- `eshell` - embedd shell, emulates shell, emacs shell environment
			- `tetris` - tetris teehee..
	- Major and Minor Modes
		- Major Mode
			- how it opens files, ex .py files
			- like, color highlighting
			- core functionallity for a specific buffer
			- can activate w commands
				- ex `M-x org-mode` - any file that ends in .org, theres some mapping turns on org mode whenever org file opened
			- only one major mode can be open at once
		- Minor Mode
			- many can be active in a single buffer
			- can provide helpful functionality ot specific to currebnt major mode of current buffer
			- some can be applied globally
			- ex `Hl-Line` - highlight line in that buffer, can be global
- Basic Key Bindings
	- ![[Pasted image 20230111205841.png]]
		- pairs - continue holding lead leatter ( TODO  cant just press ctrl again after letting go ? )
	- `C` - Ctrl
	- `M` - Meta, Alt
	- `S` - Shift
	- `s`- Superkey, Windows Key
	- Combining
		- `C-M-s` or `M-S-d`
	- Prefix
		- emacs knwos there something that needs to come after it
		- ex `C+x` is prefix for all emacs primary key bidingd
		- ex `C+c` is combination of bindings created by active major and mnior modes or by user
- Open and Save FIles
	- `Ctrl+x Ctrl+f ( find-file )`- brings up prompt in minibuffer to type in file name
	- can also navigate by deleting directory path and using `TAB` to complete parts od dir/file names
	- `Ctrl+x Ctrl+s ( save buffer )` - when open, you can edit and save
	- `Ctrl+x Ctrl+w ( write-file ) `- save buffer to a different file
		- similar to "save-as"
 - Switching Buffers
	 - `Ctrl-x b` - `TAB` will list buffers, `ENTER` on witch we want to switch to
	- `Ctrl-x Ctrl-b` - buffer list with more info, `ENTER` on which we want to open
	- can also quickly switch with `Ctrl+x <left> or <right>`
		- cycle through available buffers
- Killing buffers
	- `Ctrl-x k *buffer* `- kill *buffer* 
- Cutting and Copying Text
	- NOT ctrl-c ctrl-x....
	- kill = cut in emacs, copies then kills
		- mark region with `CTRL + SPACE`
		- `Ctrl+w `- will actually cut the text ( copy and cut at once)
	- paste = yank in emacs
		- `Ctrl+y` - pastes back what we cut ( killed ) from buffer w C-SPC
- Undo and Redo
	- undo changes to a buffer with `Ctrl+_` ( underscore ) to run undo cmd, alternate is `Ctrl-/`
	- To redo something you deleted with undo, press `Ctrl+g Ctrl-_` , note that `Ctrl-_` 's after that keeps redoing things that were undo'ed until end of redo history
		- ctrl-g resets undo state and change undo directions
- Cancelling Operations
	- `Ctrl+g` interupts any active commands 
	- brought back to normal state in emacs
	- ex showing ny prompt ( like the one for find-file cmd), if you want to cancel that prompt, press ctrl-g, possibly repeatedly
	- try this before killing process
- Killing Emacs program
	- `Ctrl+x Ctrl+c` exits 
	- if on server, it closes client connection
- Find other Key Bindings
	- `M-x describe-bindings` - list of all bindings globally and in current buffer
		- lots of movement keys, like `Ctrl-n or Ctrl-p` - next and prev line
		- lots of time savers !
	- `M-x describe-key` prompt for key to look up, tells what command is bound to that key
		- opens documentaion on that command 
- Help System
	- manual is built in, no internet 
	- Help menu ( fn10 -> go to it lmao )
	- emacs tutorial :o


