- notes and stuff on how i did it

## Laboratory: Lisp scripting


### Exercise 2.1: Navigating through Emacs source code

The basic idea here is to get a mental model of how Emacs works by looking at a bit of its keybindings and source code.

Start up a fresh Emacs with a *scratch* buffer.

To warm up, compute (2^607 − 1) × (2^607 − 1 ) (i.e., 2**(607 - 1) * (2**607 - 1)) in the *scratch* buffer, by using the expt and other functions. This is the 14th [perfect number](https://en.wikipedia.org/wiki/Perfect_number), discovered in 1952.

```perl
;; want:  2**(607 - 1) * (2**607 - 1)
pt1: ( expt 2 (- 607 1 ) )
pt2: ( - ( expt 2 607) 1 )
pt3: ( * pt1 pt2 )

  ( *  ( expt 2 (- 607 1 ) ) ( - ( expt 2 607) 1 ) )

evaluates to:
141053......more......128

141053783706712069063207958086063189881486743514715667838838675999954867742652380114104193329037690251561950568709829327164087724366370087116731268159313652487450652439805877296207297446723295166658228846926807786652870188920867879451478364569313922060370695064736073572378695176473055266826253284886383715072974324463835300053138429460296575143368065570759537328128
```


Use Emacs to determine how many bits it would take to represent this number in base-2 notation (not counting any sign bit), by writing a Lisp expression that yields the number of bits as an integer.

```perl
( + (logb ( *  ( expt 2 (- 607 1 ) ) ( - ( expt 2 607) 1 ) ) ) 1 )
```


Type M-: and use it to compute (2607 − 1) × (2607 − 1).

```perl
this cmd takes elisp format, so:
( * (- 2607 1) ( - 2607 1 )  )
result = 6791236
```

Get a list of keybindings by typing C-h b.

Look for two keybindings: C-h k and M-SPC. C-h k stands for “Type Control-h, then ‘k’.” M-SPC is “Meta Space”; on good keyboards you can get this by holding down Alt while hitting the space bar, but you may need to type “Esc” and then follow by hitting the space bar. We will examine these two keybindings in more detail.

> C-h h is describe-key
> M-SPC is just-one-space

Type C-h k C-h k and describe what happens and why. (This should relate to the C-h b output mentioned previously.)

>This 

Type C-h k M-SPC and describe what happens and why. (This should also relate.)
>using the describe-key command we see the documentation of the just-one-space command
>This is because we saw that C-h k will describe the keybindings we input after

Try out M-SPC on some sample text with a lot of white space, to see how it works.
> i saw that it put a space before the cursor if there was a character before it, i tried it where there were spaces before and it doesnt add one
> In spaces where there were multiple spaces, it instead reduces the consecutive spaces around the cursor to one space

Visit the source code for the function that implements M-SPC, by going to its help and clicking (or typing RET) on its source file name.

Notice how M-SPC is implemented in terms of a more-general function, which does not have a keybinding. Use M-: to execute this more-general function on a buffer, such that the function changes the buffer’s contents.
```perl
documentation shows: 
(defun just-one-space (&optional n)
  "Delete all spaces and tabs around point, leaving one space (or N spaces).
If N is negative, delete newlines as well, leaving -N spaces.
See also `cycle-spacing'."
  (interactive "*p")
  (cycle-spacing n nil 'single-shot))

try:
M-: (cycle-spacing n nil 'single-shot)
didnt work

try 
 (interactive "*p")
  (cycle-spacing n nil 'single-shot))
didnt work


try 
(cycle-spacing)
Works!!
returns nil
```

Similarly, use M-x to execute the more-general function on a buffer.
```perl
M-x cycle-spacing
works as well^
```

### Exercise 2.2: Scripting Emacs

Use the Emacs command M-x what-line and see what it does.

M-x what-line simply tells you what line you are on, not how many lines are in the buffer. Design and implement a command M-x gps-line that acts like M-x what-line except that it says “Line 27/106” in contexts where M-x what-line would merely say “Line 27”; here, it’s assumed the buffer has 106 lines. Do this by using C-h f to get help about what-line, navigating through that help to find its source code, putting a copy of the source code into a new file gps-line.el, editing that file, loading it into Emacs, and then executing your new command.
```perl
when we look at what what-line does, we see:

(defun what-line ()
  "Print the current buffer line number and narrowed line number of point."
  (interactive)
  (let ((start (point-min))
        (n (line-number-at-pos)))
    (if (= start 1)
        (message "Line %d" n)
      (save-excursion
        (save-restriction
          (widen)
          (message "line %d (narrowed line %d)"
                   (+ n (line-number-at-pos start) -1) n))))))
what i notice: 
	Start is the min point - O?
	n is the lineat the postition
	it calculates difference between line at pos and start 


```


When counting all the lines in a buffer, simply count the number of newline characters that it contains. This means that if a buffer ends in a non-newline, you should not count the characters after the last newline to be part of another line. Also, an empty buffer has zero lines.

Test your function on buffers that do not end in newline. Your function should be able to say things like “Line 1/0” and “Line 3/2”.






## Homework: Python scripting

-   The Python Foundation, [The Python Tutorial](https://docs.python.org/3/tutorial/) (2022)

Consider the old-fashioned Python 2 script [`randline.py`](https://web.cs.ucla.edu/classes/winter23/cs35L/assign/randline.py).

What happens when this script is invoked on an empty file like `/dev/null`, and why?

What happens when this script is invoked with Python 3 rather than Python 2, and why? (You can run Python 3 on the SEASnet hosts by using the command `python3` instead of `python2`.)

Use Emacs to write a new script `shuf.py` in the style of `randline.py` but using Python 3 instead. Your script should implement the GNU `[shuf](https://www.gnu.org/software/coreutils/manual/html_node/shuf-invocation.html)` command that is part of GNU Coreutils. GNU `shuf` is written in C, whereas you want a Python implementation so that you can more easily add new features to it. Your program should run on `/usr/local/cs/bin/python3` as installed on SEASnet.

Your program should support the following `shuf` options, with the same behavior as GNU `shuf`: `--echo` (`-e`), `--input-range` (`-i`), `--head-count` (`-n`), `--repeat` (`-r`), and `--help`. As with GNU `shuf`, if `--repeat` (`-r`) is used without `--head-count` (`-n`), your program should run forever. Your program should also support zero non-option arguments or a single non-option argument “`-`” (either of which means read from standard input), or a single non-option argument other than “`-`” (which specifies the input file name). Your program need not support the other options of GNU `shuf`. As with GNU `shuf`, your program should report an error if given invalid arguments.

Your solution should use the `argparse` module instead of the obsolescent `optparse`. It should not import any modules other than `argparse`, `string` and the non-`optparse` modules that `randline.py` already imports. Don’t forget to change its usage message to accurately describe the modified behavior.

```
Few important notes
	args-> arguments
	every argument then has some properties

filename->name,mode,encoding







```



What happens when your shuf.py script is invoked with Python 2 rather than Python 3, and why?

The [Python 3.11 release notes](https://www.python.org/downloads/release/python-3111/) say that Python 3.11 is significantly faster than older releases. Can you measure the performance difference? Use Bash’s `time` command to compare the performance of your implementation when run via SEASnet’s `/usr/bin/python3` (which should predate Python 3.11), versus running it via `/usr/local/cs/bin/python3` (which should be Python 3.11 or later), versus running Coreutils `/usr/local/cs/bin/shuf`. Use Bash commands like the following to time these three benchmarks (this example is for Coreutils, and assumes `/usr/local/cs/bin` is at the start of your `PATH`):

  time shuf < /usr/share/unicode/ucd/BidiTest.txt > /dev/null

For each of these three benchmarks, run the benchmark at least three times on the text file shown above, and report the median of the sum of the user and system times. Do your benchmarks on the same SEASnet host, and document the CPU and operating system version of the SEASnet host you used by consulting the `lscpu` command and the `/etc/os-release` file. If your Python implementation runs on `/usr/local/cs/bin/python3` but not `/usr/bin/python3`, do not benchmark it on the latter; instead, briefly explain which features of the newer Python your program relies on, and why.

## Submit

Submit the following files within a compressed tarball named assign2.tgz.

-   gps-line.el
-   shuf.py
-   notes.txt, a text file answering questions (e.g., why structural pattern matching made no sense for your Python program), and containing any other notes or comments that you’d like us to see.

All files other than the .drib files should use GNU/Linux style, i.e., [UTF-8](https://en.wikipedia.org/wiki/UTF-8) encoding with [LF-terminated lines](https://en.wikipedia.org/wiki/CRLF#Representation).

The shell command:

tar -tvf assign2.tgz

should output a list of file names that contains gps-line.el etc., with sizes and other metainformation about the files.

---

© 2005, 2007–2022 [Paul Eggert](https://web.cs.ucla.edu/classes/winter23/cs35L/mail-eggert.html), Steve VanDeBogart, and Lei Zhang. See [copying rules](https://web.cs.ucla.edu/classes/winter23/cs35L/copyright.html).  
$Id: assign2.html,v 1.12 2023/01/20 07:34:28 eggert Exp $