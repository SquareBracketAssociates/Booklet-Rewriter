






###Junk

- Variable. Un schéma peut contenir des variables en utilisant
  le backquote ou accent grave. Ainsi, ``key` représente n'importe
  quelle variable, mais pas une expression.

- Liste. Pour représenter une expression potentiellement
  complexe, on utilise `@` qui caractérise une liste. Ainsi, `@key
  peut représenter aussi bien une variable simple comme temp qu'une
  expression comme `(aDict at: self keyForDict)`. Par exemple, | `@Temps
  | reconnaît une liste de variables temporaires. Le point . reconnaît
  une instruction dans une séquence de code.``@.Statements`
  reconnaît une liste d'instructions. Par exemple, foo `@message:
  ``@args` reconnaît n'importe quel message envoyé à  foo.

- Récursion. Pour que la reconnaissance s'effectue aussi à 
  l'intérieur de l'expression, il faut doubler la quote. La seconde
  quote représente la récursion du schéma cherché. Ainsi,
  ```@object foo` reconnaît foo, à  quelque objet qu'il soit envoyé,
  mais observe également pour chaque reconnaissance si une
  reconnaissance est possible dans la partie représentée par la
  variable ```@object`.

-	Littéraux. `#` représente les littéraux. ainsi, ``#literal`
  reconnaît n'importe quel littéral, par exemple 1, `#()`, "unechaine"
  ou `#unSymbol`.


###Des exemples d'identification de schémas

Si l'on veut identifier les expressions de type `aDict at: aKey ifAbsent: aBlock` dans lesquelles les variables peuvent être des expressions composées, on écrit l'expression
suivante : ```@aDict at: ``@aKey ifAbsent: ``@aBlock.`
Une telle expression identifie par exemple les expressions suivantes :

```
instVarMap at: aClass name ifAbsent: [oldClass instVarNames]
deepCopier references at: argumentTarget ifAbsent: [argumentTarget]
bestGuesses at: anInstVarName ifAbsent: [self typesFor: anInstVarName]
object at: (keyArray at: selectionIndex) ifAbsent: [nil]
```

Comme l'interface en Squeak ne permet pas encore de sélectionner les
classes sur lesquelles on veut travailler, le système analyse les 1
934 classes et quelque 42 869 méthodes qui sont disponibles dans la
distribution de base, ce qui peut sensiblement ralentir le traitement.

Voici quelques exemples d'
expressions qui pourraient être avantageusement transformées :



