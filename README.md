# logo.eno

*a post-modern programming platform for the XXI century...* nah, just kiddin'

## import

starting from two lovely languages.

- **logo** is a programming language designed in 1967 by Wally Feurzeig, Seymour Papert, and Cynthia Solomon. it is a multi-paradigm adaptation and dialect of lisp. https://people.eecs.berkeley.edu/~bh/logo.html
- **eno** is a modern plaintext data format for file-based content, designed and developed with great enthusiasm and dedication by Simon Repp. eno exists since early 2018. https://eno-lang.org/

## basics

### assignment

assignment is written using [eno fields](https://eno-lang.org/eno/guide/elements/fields/).

```eno
x: 5
y: 6
z: + x y
```

algorithms are expressed in [prefix notation](https://en.wikipedia.org/wiki/Polish_notation), operators/functions first, followed by their operands/arguments.

in the example above, `z` equals `11`.

### multiline assignment

eno provides a syntax for [multiline content](https://eno-lang.org/eno/guide/elements/multiline-fields/), which you can use for assignment. it **works just like regular assignment**.

```eno
-- oldPond

old pond
frog leaps in
water's sound

-- oldPond
```
this code would assign an haiku to the variable `oldPond`.

primary use case of multiline assignment is method definition, see below.

### lists

lists are obviously [eno lists](https://eno-lang.org/eno/guide/elements/lists/).

```eno
myPets:
- myDog
- myCat
- myCamel
```

you access list items by giving the list an index, as you would give a function an argument. **index of first item is 1**.

### say

`say` is a special name: when assigned, its value is shown to the user.

```eno
say: myPets + 1 1
```

this would output the 2nd item of the `myPets` list, which is `myCat`.

### objects

objects are written with [eno fieldsets](https://eno-lang.org/eno/guide/elements/fieldsets/).

```eno
myDog:
name =  Mike
breed = Alaskan husky
```

`name` and `breed` are **slots** of `myDog`. you access objects slots as you would give arguments to a function.

```eno
say: myDog name
```

this would output `Mike`.

### push/pop

the word `push` can be used everywhere a variable would be written to.

the word `pop` can be used everywhere a variable would be read from.

```eno
push: foo
say: pop
```

`push` and `pop` give access to a global stack. it is meant to be used locally and immediately.

this would output `foo`.

### contexts

for convenience, it is possible to enter in the context of an object, where its **slots are accessible as variables**.

- to enter in a context, you put the object's name alone on its line.
- to exit from a context, you put the word `end` alone on its line.

you can get in nested contexts.

```eno
say: myDog name

myDog
    say: name
    say: breed
end
```

this example above would output `Mike` twice, and `Alaskan husky`.

indentation doesn't matter.

primary use case of contexts is method definition, see below.

### functions

a function starts with parentheses `( )` that contain a list of space-separated function parameters, followed by the function body expression.

```eno
-- factorial

(n)
    ife < n 2
        1
        * n factorial - n 1

-- factorial n
```

the same function could also be written on 1 line.

```eno
factorial: (n) ife < n 2 1 * n factorial - n 1
```

there can't be variadic functions: a function's arity is always definite.

### methods

a method is a **function defined in an object's slot**.

```eno
Dog:
convertAge years = (n) * 7 years
```

you call it by accessing the slot and giving the method its arguments.

```eno
say: Dog convertAge 5
```

this would output `35`.

multiline methods can be expressed by entering an object's context and using multiline assignment.

```eno
myDog
-- poeticDiction

(poem)
    &
        woof
        poem

-- poeticDiction
end
```

### prototypes and components

objects can be given an `isa` slot, which contains a list of space-separated **prototypes**.

objects can be given an `has` slot, which contains a list of space-separated **components**.

```eno
myDog:
isa = Dog
has = family

Dog:
legs = 4

say: myDog legs
```

this would output 4.

**first**, if a matching slot isn't found, the lookup continues depth first recursively in the object's *prototypes*. **then**, if there's still no matching slot, the lookup continues depth first recursively in the object's *components*.

prototypes work by [delegation](https://en.wikipedia.org/wiki/Delegation_(object-oriented_programming)).

components work by [forwarding](https://en.wikipedia.org/wiki/Forwarding_(object-oriented_programming)).

### self

in a prototype's method, the `self` word references the **sending object**.

in a component's method, the `self` word references the **receiving object**.

```eno
Dog:
bark = () & woof self name

say: myDog bark
```

this would output `woof Mike` since `self name` resolves to `myDog name`, which is `Mike`.

## typetags

values can be "type-tagged" with a `#type` tag. a typetag is a word starting with a [number sign](https://en.wikipedia.org/wiki/Number_sign) character.

there can be **several** typetags for 1 value.

typetags are optional.

they explicitly indicate **how to interpret** a value.

### adding typetags

typetags always **precede** the value they're applied to.

```eno
weight: #kg 25
maxSpeed: #unverified #km/h 230
```

in this example, `#kg` indicates the unit of measure of the value `25`.

a typetag can't be applied several times to the same value (each value has a [set](https://en.wikipedia.org/wiki/Set_(mathematics)) of typetags).

### removing typetags

there can be a need to remove typetags.

```eno
realMaxSpeed: ~unverified maxSpeed
```

`realMaxSpeed` is still typetagged as being in `#km/h`, but it's not `unverified` as `maxSpeed` was.

### typetagging multiline values

for multiline assignment, typetags come first in the content.

```eno
-- oldPond

#haiku

old pond
frog leaps in
water's sound

-- oldPond
```

this code makes explicit that `oldPong` is an `#haiku`.

### typetagging objects

you can't typetag objects, but you can typetag the values in an object's slots.

```eno
myDog:
name =  #dogName Mike
breed = #dogBreed Alaskan husky
age =   #years 5
```

if `Mike` was very young, `age` could have been expressed in `months` or even `weeks`.

### typetagging lists

you can't typetag lists, but you can typetag the items a list contains.

```eno
myPets:
- #dog myDog
- #hero myCat
- #camel myCamel
```

when accessed from `myPets`, `myCat` is a `#hero`. when accessed directly, `myCat` isn't necessarily typetagged. it shows an important property of typetags: they depend on the way you access things.

### typetags and functions

typetags allow **type checking** on function arguments. you can also typetag the return value of the function.

```eno
area: (#metre width #metre height) #squareMetre * width height 
```

an exception is raised when an argument doesn't have **all of the required** typetags.





