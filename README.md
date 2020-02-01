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

`say` is a special name. when assigned a value, the value is shown to the user.

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

### contexts

for convenience, it is possible to enter in the context of an object, where its **slots are accessible as variables**.

- to enter in a context, you put the object's name alone on its line.
- to exit from a context, you put the word `end` alone on its line.

you can get in nested contexts.

```eno
say: myDog name

myDog
    say1: name
    say2: breed
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

-- factorial
```

the same function could also be written on 1 line.

```eno
factorial: (n) ife < n 2 1 * n factorial - n 1
```

there can't be variadic functions: a function's arity is always definite.

functions are values.

functions are pure, they can only access their arguments, nothing else from the outside.

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

## tags

values can be "type-tagged" with a `#type` tag. a tag is a word starting with a [number sign](https://en.wikipedia.org/wiki/Number_sign) character. you already know `#log`, which is a normal tag.

there can be **several** tags for 1 value.

tags are optional.

they explicitly indicate **how to interpret** a value.

### adding tags

tags always **precede** the value they're applied to.

```eno
weight: #kg 25
maxSpeed: #unverified #km/h 230
```

in this example, `#kg` indicates the unit of measure of the value `25`.

a tag can't be applied several times to the same value (each value has a [set](https://en.wikipedia.org/wiki/Set_(mathematics)) of tags).

### removing tags

there can be a need to remove tags.

```eno
realMaxSpeed: !unverified maxSpeed
```

`realMaxSpeed` is still tagged as being in `#km/h`, but it's not `#unverified` like `maxSpeed` was.

### tagging multiline values

for multiline assignment, tags come first in the content.

```eno
-- oldPond

#haiku

old pond
frog leaps in
water's sound

-- oldPond
```

this code makes explicit that `oldPong` is an `#haiku`.

### tagging objects

you can't tag objects, but you can tag the values in an object's slots.

```eno
myDog:
name =  #dogName Mike
breed = #dogBreed Alaskan husky
age =   #years 5
```

if `Mike` was very young, `age` could have been expressed in `months` or even `weeks`.

### tagging lists

you can't tag lists, but you can tag the items a list contains.

```eno
myPets:
- #dog myDog
- #hero myCat
- #camel myCamel
```

when accessed from `myPets`, `myCat` is a `#hero`. when accessed directly, `myCat` isn't necessarily tagged. it shows an important property of tags: they depend on the way you access things.

### tags and functions

tags allow **type checking** on function arguments. you can also tag the return value of the function.

```eno
area: (#metre width #metre height) #squareMetre * width height 
```

an exception is raised when an argument doesn't have **all of the required** tags.

## control structures

every modification of the control flow is expressed with [eno sections](https://eno-lang.org/eno/guide/elements/sections/).

sections can be nested without level limit.

### when / unless

`when` blocks are executed if a condition is true.

```eno
# when < x 20

    say: "x less than twenty"

# when < x 80

    say: "x less than eighty"

# unless < x 0

    say: "x positive"

# then

say: "done"
```

if a conditional block isn't executed, flow jumps to the next section of same level.

`then` blocks are always executed.

### else

`else` blocks are executed if the preceding `when` or `unless` of same level hasn't been executed, as you would expect.

`else when` and `else unless` blocks allow usual conditional chaining.

```eno
# when < x 20

    say: "x less than 20"

# else when < x 40

    say: "x between 20 and 40"
```

here, `x between 20 and 40` won't be output if the first `when` block has been executed.

### switch / case

`switch` blocks transfer control flow to the `case` block with the same value.

```eno
# switch + 1 1

    ## case 1

        say: one
        
    ## case 2
    
        say: two
        
    ## case 3
    
        say: three
        
    ## default
    
        say: "something else"
        
# then

say: "done"
```

the `default` block is executed if no `case` matched.

flow doesn't "fall through" non-matching `case` blocks that follow the matching one (`say: three` won't be executed).

### while

`while` blocks are executed repeatedly as long as a condition holds.

```eno
x: 1

# while < x 10

    say: x
    x: + x 1

# then

say: "done"
```

flow loops before the next section of same level.

## procedures

procedures are code blocks allowing sequential execution of calls and assignments.

they are expressed with [eno sections](https://eno-lang.org/eno/guide/elements/sections/) too.

### procedure

a procedure body goes until the next section of same level.

procedures can have parameters.

```eno
-- factorial

    (n)
        ife < n 2
            1
            * n factorial - n 1

-- factorial

# procedure showFactorial number

    -- say

        & "Factorial of "
        & number
        & " is "
          factorial number

    -- say

# main

    do-once: yes

    ## while do-once

        ask: "Choose a number" n
        call: showFactorial n

        ask: "Another one?" do-once
```

procedures can access anything defined in the level they're defined in, and in higher (containing) levels.

you execute a procedure using `call`, as you can see.

### scope

there is no shadowing. if you use a name that's already used in higher (containing) levels, then you're playing with the same toy, not another "local" one.

names assigned in a section don't live outside of the section they're defined in.



