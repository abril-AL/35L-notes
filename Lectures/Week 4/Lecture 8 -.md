classes
```
class c(a,b)
	code ( define method)
	def_method(..)
		code
def __init__()
	code for initialising the object

```
- 1st arg is the actual object , self
- class hierarchy , like a graph, no loops

namespace control
- what py uses to keep track of identifiers
- class is an an object, 
- ???

names starting w `__` are special, for creating stuff
- dont typically change them
- usually only change methods
- can dynamically change (runtime) class stuff
	- flexibility, common in js or py, not compiled langs
	- modify its dictionary

```python
# modulename.py

python code
class c(a,b)
def f(a)


$python3 modulename.py

# same (ish) as saying:
#python
>>> import modulename

1 creates new namespace 'n'
2 reads and executes the code in the context of the new namespace 'n'
	to avoid collissions, so if two classes are called c, it knows diff
3 bind modulename. to n in ????
>>>modulename.f(27) 

other way:
>>>from modulename import x
can say just f(27)
not usually done, not very reliable

difference:
$python3 foo.py
when it reads the code it temp binds __name__ to "__main__"

# in foo:
ordinary def c(a,b)
if __name__ == "__main__":
	stuff
	often test code to test module
	skipped in import
	
	
# common to write test cases first - test first dev / test driven dev / wrtie 1st
## inverted for a reason
## we dont know API, but we can write test first to test API that doesnt exist, 
## easier to write than fixing code later

example:

class aincostan(angle)
	code...

(x,y,z)=sincostan(a)
print...
#we can fix this part first, save time later
#slow to implement first
# try for project

```
Virtual Environments
- Background
	- virtualization - have existing model for organizing software
		- ex. we can see it on SEASnet `$cd / $ls`
		- those commands / envr are same for everyone
		- downside: 
	- build a tailored copy of python environment
		- my have diff packages or diff modules
		- as we tailor, it gets more expensive, but better
- `python3 -m vemv mydir` 
	- creates virtual software environment
	- *python* virtualization is software only, no root access or hardware access 
		- for python only ( in this case ), other things dont care, ex emacs
	- puts in everything we need to make virtual environment
		- ex bin dir w commands, include dir w stuff w python3 stuff
		- config file - tells about our virtual environment
- can run `. bin/activate`
	- ls shows us activate files are not executable
		- shell code that packages up what we need to run in this virtual environ
	- run that command to run that code, now running in our virtual environment
		- ex. `pip install numpy` installing into our directory
	- not same as `./bin/activate` 
```python
# can now do:
$python3
>>>import numpy
```
- can export for use elsewhere 
- deactivate
	- runs shell file function deactie
	- back to originall envr
- python makes it easy to make 'playpens'
- 




```Python
Python Packages
- scaled up modules
- how we collect small bits of modules
- Instead of a big module or large amt of `.py` files that dont scale well
	- Want organized way to scale up
	- Will use directories to group related files
	- like a tree
	- `__init__.py` - ran when package opened(?) to handle(?)
- Why do we do this / Motivation
	- similarly have organizing classes and for packages
	- can we just use one / why use 2 ?
	- packages are abt software developer behavior/for conveniance , whereas classes are for prganizing object behavior as the code runs
- importing is a statement ( not a declaration )

# could do :
if time > 1200
	import thing
# can dynamically import


```


Client Servers
- hook everything together on one machine
- ex, lnxsrv12.seas.ucla.edu
- multp processes connected, maybe piping, but still one machine
- scaling issue
	- depends on technology 
	- not good for building big stuff
	- not enough insulation bw pieces
		- slow the server, brittle
- want it so that if one piece breaks, we can recover
- Idea: Client Server
	- pick one node
	- have clients, indiv crashes dont effect server
	- if server goes down, less reliable
	- but, more complicated
- Alternatives to C.S.
	- peer-to-peer
		- split between nodes, all equals, all clients and all servers,
		- no central server
		- if one peer goes down, res could possibly keep going
		- downside: more overhead, more communication
	- primary secondary approach
		- primary tracks state of application, coordinates application
		- like an inversion of C.S.
		- primary in charge or requests
	- shell w mult processes
-


- Performance Issues with Client Servers
	- 1throughput
		- how much data we get through
		- how big are our pipes, bits per second
	- 2 latency
		- delay in seconds
		- how long are we waiting for response after request
	- Standard ways to solve
		- throughput - out of order exection, not in order they were given, so it  can do more in same amt of time
		- latency - caching recent answers
			- problem: stale caching

- Brief intro to the internet 
	- before, to hook together computers was circuit searching, 
		- if im in LA talking to someone in NY, there is a physical wire to a central off that connects to other ones with wires, until it eventually gets to NY
		- reserved wires from a to b
		- downside: nobody else can use those reserved wires, and have to overprovision enough wires
	- internet introduced new way to make connection, packet switching
		- instead of reserving, just take message and divide into packets (same sizes) that are shipped off, dont care about how they get there (path), router ships them off, diff packets may have diff paths 
		- more roubust, if a router goes bad, it just sends a different path/way
		- best effort, not guaranteed, can lose packets 
		- what can go wrong with packet switching
			- 1 packets can be lost
				- typically happens when a router is overloaded, and throws packets out and keeps going
			- 2 packets can be recieved out of order
			- 3 packets can be duplicated
				- can get more than one copy
			- 4 packet can be intercepted/snooped
			- 5 packets ca be corrupted 
		- how do we deal with these issues
			- layers of the internet
				- physical layer  ( lowest layer )
				- link layer (physical) - gets packets between adjacent nodes(?)
				- internet layer - packets between non adjacent nodes
				- transport layer - **streams** of data between non adjacent node
				- **app**/application layer (top level) - particular protocal for getting aparticuar progrmam to run , how client and server talk to eachother, app dependent
			