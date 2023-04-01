12/01

- Lecture 1 review
	- Hardlink
		- multiple names for same file, in diff directories, or even same directory
		- we decide the mapping of file name components to file
		- minimum to number of links ?
			- `$ cat /etc/[assword`  > file
			- `$ ( rm file, cat )` < file
				- link count is zero after rm executed
					- no one can find it by name
				- file still there... cat using it to output whats in file
		- if we delete OG file, links can still acess it ( assuming no soft links in b/w )
	- rm modifies directory 
		- doesnt rly erase anything
	- `$ shred file` opens files, puts random bits on it, removes
	- Symbolic link ( soft link )
		- "points" to a name of a file
		- special file - treated speacial , acts normal
		- contents treated like file name when we reference it
		- begins with / - absolute name
			- throw away how we got there
			- means the same thing everywhere
			- start from root
		- doesnt begin with / 
			- relative to directory we start at
		- actully exist, interpreted dynamically
		- if we delete original file, the link is useless
- ![[linksDiff]]


- Topics
	- filesystems
		- what linux filesystem looks like
		- model of pointers to files
	- Scripting
		- solve problems quickly
	- Building and Distribution
		- how to build + ship out, compatabilty for other users w diff machines
	- Version Control
	- Low level debugging
	- Client-server ( project 

- Emacs Basics
	- `Ctrl-h t` - emacs learning by doing tutoria;;
	- `Ctrl-h k` - tells what a keystroke will do without exec
		- `Crtl-h k Ctrl-p` - tells us about `Ctrl-p`
	- `Ctrl-h m` - tell about the mode
		- see what keystrokes are available
		- emacs is a modeful user interface ( depends on context, window or buffer)
			- `Ctrlx d` dired mode
	- `Ctrl-h b` - list all key bindings, list mode in detailed mode 
	- want to generate this list then search through it
		- `Ctrl-s`  
	- `TAB` completion character
	- `Ctrl-x o` - switch to other buffer ( if more than one open )
	- `Ctrl-x 1 ` - see just this buffer
	- `Ctrl-x 2`  - split the window
	- `Ctrl-x 3 ` - split window horizontally
	- `Ctrl-x 8 RET` - prompts for what unicode char u want and types it 
		- `Ctrl-x = ` what char it is
	- 

- More Commands
	- `M-x - view-lossage RET`
		- view old commands
		- key strokes and what they map to
	- `Ctrl-g`  - want to get out of trouble
	- `Ctrl-x Ctrl-c`  -  exit emacs
	- `Ctrl-x k`  -  kill  buffer
		- if not assoc. with file, unrecoverable
		- if assoc. with file, warns if u havent saved
	- `M-x shell RET`  -  starts a subshell, interactive
	- `M-! <command> RET`  -  single shell, output int shell TODO??
		- empty standard input
	- `M-| shell <comand> RET` ( vertical bar ) - stdinput comes from region of buffer in emacs
		- region ? 
			- point and mark
			- point is the position of the cursor
			- region is area bw point and mark
			- `Ctrl-@ or C-SPACE` - set mark = point
				- twice will deactivate the mark
			- set  mark first, move cursor (point)
			- can also set point by searching
		- `Ctrl-x Ctrl-x`  swap point and mark ( go to mark , prev pos saved as new mark )
	- `Ctrl-u ` prefix
		- if we add it to our `M-1 and M-|` does it ti current buffer

## The Shell
- shell command language
	- we can say `ls -l   or  head -2 /etc/passwd`
	- we can do I/) rediractions: `<file >file 1  2>err`where to go/come from rather than the terminal
- piplines
	- `cat foo bar | tr a b | sort ` - run three commands parallel
		- linked together bc standard input is same
		- no temp file
	- broken pipe?
	- 