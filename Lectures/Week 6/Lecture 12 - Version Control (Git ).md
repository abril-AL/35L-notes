Everyone has their own version / competing history
Difference from real history - we can control history
Goal: (?)

## Git 
- one system for version control
- most popular
- Contains two main thing
	1) object database - repository of connected objects, records history of your project, tracks what everyone's doing to everything in the project
	2) index - records the future of your project, not the full or actual future, just your plans for some of the future of the project, keeps track of with your going to do next 
- a **commit** action migrates index into object database   (aside: common style to only commit **working** code)
- Getting Started
	- `git comit` creates empty repository, nothing in it, empty index, no one really does this (start)
		- also has a  justification (commit message)
	- `git clone` starts based off an existing project, new repository that is a clone of an existing repository and its history and an empty index, copy of working files and database
		- creates directory containing project
		- directory `.git` with object database and index
	- `git status` - what's the status of your current repository
	- `git ls` - list files under git control
	- `rm $(git ls-files)` - remove working files
	- `git checkout` - `-f` force it, do (?)
	- `grep main $(git ls)` 
	- `git grep <patter>` = `grep <pattern> $(git ls files)`
		- common cmd, has a shorthand
	- `git log` - history of the git repository
		- `git log -show`
	- safe commands, don't change anything, mostly for viewing
- More on Commits 
	- 1st line of commit, elevator pitch, at most 50 characters, very brief explanation of the commit
	- 2nd line of commit - always empty
	- later lines, can expand, justification/explanation
	- every commit should be on a single topic, dont overdo splitting the commits into smaller topics
- Git log options
	- `git log <commit-big-number>..HEAD`
		- `git log A..B` - log all commits from A (exclusive) to B (inclusive), changes in B not in A
	- (?)
	- Shorthands
		- `HEAD^` or `I^` , previous commit to `I`, `I^^` goes back two
		- Base ten `I~5`
		- `git log HEAD^1` or `HEAD^..HEAD` (?) or `-1` for most recent 
- `git blame` - found a bug and want to find who did it
	- prints line + writer / time and who commited + prefix of gitID
	- can use the output to run other cmds that will explain why bug was added
	- can be wrong, can also do a git blame on an older version `git blame <head> <file>`

## Doing Changes to Program
- ex. want to make change, 
	- edit the file
	- tell git you plan to make a commit involving this file
	- `git add <foo>` modifies index to contain foo
	- atp three different versions of foo
		- in head, most recent
		- in index, created by add cmd
		- working file, what we see in our text editor
	- `git diff --cached` diff between head and staged (?)
	- `git commit -m  <good-message>` - commit message in cmd line terminal, `-a` auto-add to commit everything
	- `edit f -> git add F -> git commit F` if we get a bug edit add commit, OR edit + amend
	- amend replaces recent commit with updated version, rul of thumb is to only do this t your oen rsoisi=itory

??? Rewatch lecture....

## What to include/exclude
- what if we don't want to put `.o` files
- What do we put in our repository ?
	- stuff you maintain/edit by hand
- What to not put in repository
	- stuff generated automatically
	- stuff that's not portable
- `git clean -f` delete everything not added, useful to have to get rid of junk you don't want in your repository 

## Git Ignore
- special configuration file
- tell git what to ignore based on patterns
- bad ignore file, git will complain, or will ignore too many things
- `echo foo0` >abc