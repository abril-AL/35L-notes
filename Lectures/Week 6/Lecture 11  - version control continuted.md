- `git fetch` - when u clone one repo from another (  upstream (what you cloned from ) to downstream ( your .git))
	- can create a tree of the repo
	- collab with other people who have their own downstream repos with unique changes, and pushes them
	- `git fetch` grabs the latest git upstream version
	- possibly weve made our own changes
	- simoultaneous versions can exist on diff repos, both visible down stream , what were working on + latest
	- later `merge`
- `git pull` - does the fetch plus copy it into your version
	- easy thing, but trouble if there's a collision 
	- safter to do a fetch and check that there will be no trouble
- `git bisect` - standard technique for debugging programs
- `git bisect start` - tell git ur ready to do a bisect, tell it which commits are good/bad with `git bisect good <branch/tag>`,`git bisect bad <branch/tag>`
	- `git bisect start HEAD v12`
		- HEAD bad v12 good
	- `git bisect run` - put whatever cmd and test the, 
		- `git bisect run make ( -j check )`
## Branches
- cuts down on time
- feature branch 
- def: lightweight movable pointer to a commit, like a pointer 
	- commit moves the branch pointer up
	- `git clone` gives us the main branch
- can switch to a different branch 
- `git clone` -> `git branch z100`
	- point to wherever we are currently
- `git ...` `git checkout <branch>`, `git add -> git commit`

- `git branch -m a b` Take branch a and move to b, like `mv`
	- if we have a complicated repository, move is just renaming, cheap operation

## Merge
- thing getting complicating branching out, how we simplify, `merge`
- have two branches, want changes to be reflect what in both
- multiple parents, want changes from both
- have to figure out how to apply these changes, `diff`, summary of diff between the two, `diff A B >file-diff.diff`
- `patch <diff-file.diff` will look at edit file and apply it to A to turn it into B 
- `diff3` compares 3 files, summary of all three files and differences between them, diff A B then diff B C and merges two results, A is a common ancestor of B and C
Git Merge
- When git wants to merge branches, does diff of two branches last commit and a common ancestor
- competing versions, diff doesnt know whats right, git also doesnt know, colliding changes to same area
- 2 kinds of merges
	- conflicts - overlapping
		- trouble
	- no conflicts - smooth
		- might still have trouble, ex deleting a function in branch A, but adding a call to it in branch B
		- make sure to run test cases
- git only insists on no cycles, can be own parent 

## Rebasing
- with big graph, hard to track where we are
- how to simplify our graph ?
	- `git checkout b` , `git rebase main`, goes through changes and applies to main 
	- simpler history, less honest to how development happened
	- git bisect works with rebasing
- if push fails, try
	- ???