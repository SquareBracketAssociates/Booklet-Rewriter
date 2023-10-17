

### Using Flamel Search Tool


### Dealing extras messages
When you look at the results of the previous pattern and you compare with the system you will see that we are missing some of the cases.
For example we are missing the following method. 

What you see here is in the `RBRenameClassChange>>changeClass` is that the pattern did not match because `anything matches only one element and here we get `newName asSymbol` which is not a single element.

```
RBRenameClassChange>>changeClass
	^ Smalltalk globals at: oldName asSymbol ifAbsent: [ Smalltalk globals at: newName asSymbol ]
```

Similar for `configurationClass` we get a complex expression `workingCopy package name asSymbol` instead of a simple element.

```
ConfigurationGenerator>>configurationClass
	^ Smalltalk globals at: workingCopy package name asSymbol 
```

Finally another variation of the problem is the following one:

```
RBAddClass>>definitionClass
	^ Smalltalk globals at: (self superclassName ifNil: [ ^ ProtoObject ])
```

OK how do we address this problem, we use the `@` operator in addition to `\``. `@` means that the element is optional and can be repeated.
The optional is not illustrated by the example here but we will see later that you need it to express that there could be zero or more elements
or expressing that you can at least one unary messages. Let us come back to our case we add `@` to the pattern as follows:


```
`anobject globals at: `@anything
```

Rexecute the query to see that you now get all the cases.

```
FlamelRule new
   matchingExpression: '`anObject globals at: `@anything';
   matchExpression;
   run;
   result
```

### Dealing with Literals
todo 

```
	`anObject globals at: #foo
```


### Recursive matches
What's happen if a variable matches a complex expression that also contains the expression we are looking for.
For example 

Finally when trying to match an expression, there is another operator ```` which supports recurvise match.


### Matching Expressions



### Defining a meta-variable: Matching any node

``node` This is the most basic meta-variable. It matches any node.

Examples

``receiver foo` matches the following expressions:

```
self foo
x foo
OrderedCollection foo
```


`self `msg: `arg` matches any one-argument message sent to self:

```
self as: Array
self instVarAt: 2
```

### Matching a list of zero or more nodes

- ``@nodes` To match a list of zero or more nodes (like message args or temps). 

Examples:

``@receiver foo` matches

```
self foo
self size foo
(self at: 1) foo
```

`self `@msg: `@args` matches any message sent to self:

```
self class
self at: 4
self perform: #at: with: 4
```



### Matching Literals

``#literal` To match literals. 

``#literal size` matches any message sent to a literal whose selector is size 

```
3 size
'foo' size
#(a b c) size
```



### Matching Methods

@MatchingMethods

While matching expressions does not care of the whole context (such temporaries, assignment and method signatures), matching a complete method requires more advanced matching. For example if an expression is inside a block if we care about the fact that the methods
has temporaries.


- ``.stmts` To match statements. The pattern ``.Sta1.` matches

```
x := 1.
```

Combined with `@`, you can match e list of statements. 
The pattern ``@.stmts` matches any list of statements.


- ``{ :node | ... }` To match the nodes that satisfy the enclosed Smalltalk code. The pattern ``{ :node | node isInstance }` matches instance variables.








###Examples


```
| methodNode searcher  |
methodNode := OpalCompiler new parse: 'test 
| x |
x := 1. 
y := 2.
z := (self at: #x) size.
x + y'.

searcher := RBParseTreeSearcher new.
searcher
	matches: 'self at: `#literal'
      do: [ :aNode :answer | answer add: aNode ; yourself ].
searcher executeTree: methodNode initialAnswer: Set new.
searcher answer
```


```
| rbMethodNode |
rbMethodNode := OpalCompiler new parse: 'test 
| x |
x := 1. 
y := 2.
z := (self at: #x) size.
x + y'.

(RBParseTreeSearcher new
        matches: '`{:node | node isVariable }'
        do: [ :aNode :answer | answer add: aNode; yourself ] )
               executeTree: rbMethodNode initialAnswer: Set new.

```



```
self matcher
matches: '``@lotOfStuffBefore globals at: ``@lotOfStuffAfter'
do: [:theMatch :theOwner | theMatch inspect].

rule := SearchGlobalsAtUsage new.
environment := RBClassEnvironment class: Result.
RBSmalllintChecker runRule: rule onEnvironment: (environment)
```



```
| rbMethodNode |
rbMethodNode := OpalCompiler new parse: 'test 
| x |
x := 1. 
y := 2.
x + y'.

(RBParseTreeSearcher new
        matches: '`{:node | node isVariable }'
        do: [ :aNode :answer | answer add: aNode; yourself ] )
               executeTree: rbMethodNode initialAnswer: Set new.

```


```
| className realClass replacer category |
className := #MyClass.
realClass := Smalltalk at: className.
category := #accessing.
```

If you really just want the string 'tabs', the string 'tabs' with the
quotes is the search expression.

If you want to find it as part of a substring use something along:
```
   `#string `{ :node | node value isString and: [ node value
includesSubString: 'tabs' ] }
```

The ``#string` is a literal pattern (booleans, characters, arrays,
strings, numbers, ...) and `{ ... adds a constraint on the preceeding
match.


```| className realClass replacer category |

className := #MyClass.
realClass := Smalltalk at: className.
category := #accessing.

replacer := RBParseTreeRewriter new
				replace: '`receiver oldMessage' with: '`receiver newMessage';
				yourself.
(realClass organization listAtCategoryNamed: category)
	collect: [:sel |
		| parseTree |
		parseTree := ( realClass >> sel) parseTree.
		(replacer executeTree: parseTree)
			ifTrue: [ realClass compile: replacer tree newSource " [1] " ] ]
```




