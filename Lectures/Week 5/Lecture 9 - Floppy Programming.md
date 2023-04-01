Basic Internet technologies
HTML + HTTP
html is: sgml with web extentions( evolved rapidly, issue w compatability)
1) sgml syntax 
2) web specific stuff
sendinf trees through network connections
compatability - seperate them => XML ( extensible markup language)
- only synax
- html without web
- entities, tags, elements `<x> Hello </x>` , but `x` is up to us
- web figure out the `x` ``
- we can leverage off xml to build our own protocols
	- own tags, attributes, etc
- HTML = XML + web specific stuff => XHTML
- failed , xhtml didnt work, but XML succedded somewhat ( mostly business )
-  xml more strict, need end tags
	- html more forgiving, but might not be right behavior
	- more reliable data transfer

- DONG - document object model
	- used for looking at and t
	- raversing and modifying the trees
	- programming model / API for attributed trees
	- can search, tree manipulation
	- way that many trees are handled 
	- front end 

- CSS cascading style sheets
	- front end
	- disctinction in css between content and form
	- seperate content from form
	- form = appearance on screen
	- form independent from data
	- cascading - specifiy style, cascaades down from parent to childrem
	- specifies priorties , epecifivatoon ket s desribe
	- styles are declarative. goal: dont write code,


Java Script
- simple at the start
- like pytonh
- somwhat  - scripting language, very forgiving
can ??? in html
	`<scrpti> src="pyscript.js"` 
```html
<script src="myscript.js"></script>
download once, 

<script> alert("Hello") </script>
webpage can do fancy stuff, modify DONG, replace script element w something else, up to what browser will let it do

Arent we then giving away src code ? 
- yes... 
- option: obfuscate the code
	- but deofuscaters exist...
	- makes code much slower
- option : dont give it to client, dont ship it to browser
	- server side code ( note the code then doesnt have to be js)
``` 

```javascript
//boring and error prone

const c = create DOM tree node("p");
modify-node(n,subtree("smthn"));

//want to write like this:
<p style="smthn">
	body
</p>

//how did they do it?
const a = <p style='abc'> def </p>
js         shorthand for    js
		   detailed js
			- JSX
//handwavy 
//take jss and turn into tree stuff

//why does it work, 
'<' invalid -> must be html -> translate
similar for '>'


{execute this}
take the value and interpolate it
<html jsx{js}>def</html>
thsi way we can create contents based on things we already calculated
nests recursively
```
##### Order of Execution
- ex. C++ starts with main
- webpage not so simple, can have several scripts, which is run first ?

- Browser Rendering Pipeline
	- browser is running, constantly has work to do, constantly running js
	- wont be able to do everything at once, has to decide what to do now vs later
	- Steps
		- 0 ) Browser downloads HTML web page 
			- could be large
			- could be on bad connection
			- will have part of the web page - part of the tree
			- figure out how to display info about a page it knows some info about
			- browser will start rendering before the download is complete
			- may have to re render already displayed material when new info shows up
			- if it relies on a script that hast arrived - wont work
			- Or, runs script without present data 
			- want robust app, write scripts carefully
		- 1 ) Optimizations
			- whole webpage has displayed part, and offscreen part
			- gives prioriity to elements that are on screen
			- cant assume all scripts will be active
			- js code cant assume (???)
```JS
<XML> ... </XML>
this is one way

another way: JSON
JavaScript Object Notation
	- js easy to specify object w (??) and attributes
	- {"menu":{"a":"file", "value":"File" "menuitem":[...] } }
	- syntax for tree
	- more convenient for ppl used to js
```


## Node.js
htpps://node.js
JavaScript runtime for asyncronouc events
archtecture says that if u use Node, u write ur program as a set of event handlers
```JS
while ( g = getEvent() )
{
	handle that event
}
you define events and event handlers
split program into event handlers
```
- what is an event handler 
	- have to follow certain rules
	- must execute quickly
	- have to return
	- they **cannot** wait - split them up 
	- single-threaded - no parallelism 
	- scale by running mutiple web servers, **safer** 

- multithreaded apps
	- get parallelism
	- threads can wait + respond to other requests
	- downside -> talk to same memory, so a lot of bugs due to race conditions
		- 2 threads read/write at same time
	- Node doesnt use threads, unreliable, at most 1 event handler can operate at a time

- can write any standalone app in Node
	- ex. a web server, writen in js w eent handler model, requests are events'
	- with this approach, can split it into 2 pieace:
		- 1 run in server in web server
		- other run on client side in browser