# logo.eno

*a post-modern programming platform for the XXI century...* nah, just kiddin'

## import

starting from two lovely languages.

- **logo** is a programming language designed in 1967 by Wally Feurzeig, Seymour Papert, and Cynthia Solomon. it is a multi-paradigm adaptation and dialect of lisp. [https://people.eecs.berkeley.edu/~bh/logo.html](https://people.eecs.berkeley.edu/~bh/logo.html)
- **eno** is a modern plaintext data format for file-based content, designed and developed with great enthusiasm and dedication by Simon Repp. eno exists since early 2018. [https://eno-lang.org/](https://eno-lang.org/)

## basics

### declaration

you declare the names you'll use like this:

```
name: foo bar baz
```

the program won't run if you're using names without declaring them first. declaring the same name several times is forbidden too.

### assignment

assignment is written using [eno fields](https://eno-lang.org/eno/guide/elements/fields/).

```eno
name: x y z

x: 5
y: 6
z: + x y
```

algorithms are expressed in [prefix notation](https://en.wikipedia.org/wiki/Polish_notation), operators/functions first, followed by their operands/arguments.

in the example above, `z` equals `11`.

### lists

lists are obviously [eno lists](https://eno-lang.org/eno/guide/elements/lists/).

```eno
name: my-pets

my-pets:
- my-dog
- my-cat
- my-camel
```

you access list items by giving the list an index, as you would give a function an argument. **index of first item is 1**.

### say

`say` is a special name. when assigned a value, the value is shown to the user.

```eno
say: my-pets + 1 1
```

this would output the 2nd item of the `my-pets` list, which is `my-cat`.

### double quotes and square brackets

#### double quotes

by default, the value of a name is itself. so, you don't need to quote unassigned names.

but a double quote can be used as first character of a name, to prevent its evaluation.

```eno
name: eleven

say: eleven
eleven: 11
say: eleven
say: "eleven
```

this would ouput `eleven` and `11` and `eleven`.

#### square brackets

square brackets denote a string.

```eno
say: + 1 1
say: [ + 1 1 ]
```

this would output `2` and `+ 1 1`.

curly braces allow templating.

```
say: [ + 1 1 would output { + 1 1 } ]
```

this code would ouput `+ 1 1 would output 2`.

### multiline assignment

eno provides a syntax for [multiline content](https://eno-lang.org/eno/guide/elements/multiline-fields/), which you can use for assignment. it **works just like regular assignment**.

```eno
name: old-pond

-- old-pond
[
    old pond
    frog leaps in
    water's sound
]
-- old-pond
```
this code would assign an haiku to the variable `old-pond`.

primary use case of multiline assignment is method definition, see below.

### objects

objects are written with [eno fieldsets](https://eno-lang.org/eno/guide/elements/fieldsets/).

```eno
name: my-dog

my-dog:
name = [Mike]
breed = [Alaskan husky]
```

`name` and `breed` are **slots** of `my-dog`. you access objects slots as you would give arguments to a function.

```eno
say: my-dog name
```

this would output `Mike`.

### contexts

for convenience, it is possible to enter in the context of an object, where its **slots are accessible as variables**.

- to enter in a context, you put the object's name alone on its line.
- to exit from a context, you put the word `end` alone on its line.

you can get in nested contexts.

```eno
say: my-dog name

my-dog
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
name: factorial

-- factorial

(n)
    ife < n 2
        1
        * n do - n 1

-- factorial
```

the same function could also be written on 1 line.

```eno
factorial: (n) ife < n 2 1 * n do - n 1
```

there can't be variadic functions: a function's arity is always definite.

functions are values.

functions are pure, they can only access their arguments, nothing else from the outside.

`do` always refer to the executing function, so you can write anonymous recursive functions.

### methods

a method is a **function defined in an object's slot**.

```eno
name: Dog

Dog:
convert-age = (years) * 7 years
```

you call it by accessing the slot and giving the method its arguments.

```eno
say: Dog convert-age 5
```

this would output `35`.

multiline methods can be expressed by entering an object's context and using multiline assignment.

```eno
my-dog
-- poetic-diction

(poem)
    &
        [woof]
        poem

-- poetic-diction
end
```

### prototypes and components

objects can be given an `isa` slot, which contains a list of space-separated **prototypes**.

objects can be given an `has` slot, which contains a list of space-separated **components**.

```eno
my-dog:
isa = Dog
has = family

Dog:
legs = 4

say: my-dog legs
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
bark = () & [woof] self name

say: my-dog bark
```

this would output `woof Mike` since `self name` resolves to `my-dog name`, which is `Mike`.

## tags

values can be "type-tagged" with a `#type` tag. a tag is a word starting with a [number sign](https://en.wikipedia.org/wiki/Number_sign) character.
there can be **several** tags for 1 value.

tags are optional.

they can explicitly indicate **how to interpret** a value.

### adding tags

tags always **precede** the value they're applied to.

```eno
name: weight max-speed

weight: #kg 25
max-speed: #unverified #km/h 230
```

in this example, `#kg` indicates the unit of measure of the value `25`.

a tag can't be applied several times to the same value (each value has a [set](https://en.wikipedia.org/wiki/Set_(mathematics)) of tags).

### removing tags

there can be a need to remove tags.

```eno
name: real-max-speed

real-max-speed: !unverified max-speed
```

`real-max-speed` is still tagged as being in `#km/h`, but it's not `#unverified` like `max-speed` was.

### tagging multiline values

for multiline assignment, tags come first in the content.

```eno
-- old-pond

#haiku
[
    old pond
    frog leaps in
    water's sound
]
-- old-pond
```

this code makes explicit that `old-pong` is an `#haiku`.

### tagging objects

you can't tag objects, but you can tag the values in an object's slots.

```eno
my-dog:
name =  #dogName Mike
breed = #dogBreed [Alaskan husky]
age =   #years 5
```

if `Mike` was very young, `age` could have been expressed in `months` or `weeks`.

### tagging lists

you can't tag lists, but you can tag the items a list contains.

```eno
my-pets:
- #dog my-dog
- #hero my-cat
- #camel my-camel
```

when accessed from `my-pets`, `my-cat` is a `#hero`. when accessed directly, `my-cat` isn't necessarily tagged. it shows an important property of tags: they depend on the way you access things.

### tags and functions

tags allow **type checking** on function arguments. you can also tag the return value of the function.

```eno
name: area

area: (#metre width #metre height) #squareMetre * width height 
```

an exception is raised when an argument doesn't have **all of the required** tags.

## control structures

every modification of the control flow is expressed with [eno sections](https://eno-lang.org/eno/guide/elements/sections/).

sections can be nested without level limit.

### when / unless

`when` blocks are executed if a condition is true.

```eno
name: x

# when < x 20

    say: [x less than twenty]

# when < x 80

    say: [x less than eighty]

# unless < x 0

    say: [x positive]

# then

say: [done]
```

if a conditional block isn't executed, flow jumps to the next section of same level.

`then` blocks are always executed.

### else

`else` blocks are executed if the preceding `when` or `unless` of same level hasn't been executed, as you would expect.

`else when` and `else unless` blocks allow usual conditional chaining.

```eno
# when < x 20

    say: [x less than 20]

# else when < x 40

    say: [x between 20 and 40]
```

here, `x between 20 and 40` won't be output if the first `when` block has been executed.

### switch / case

`switch` blocks transfer control flow to the `case` block with the same value.

```eno
name: one two three

# switch + 1 1

    ## case 1

        say: one
        
    ## case 2
    
        say: two
        
    ## case 3
    
        say: three
        
    ## default
    
        say: [something else]
        
# then

say: [done]
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

say: [done]
```

flow loops before the next section of same level.

## procedures

procedures are code blocks allowing sequential execution of calls and assignments.

they are expressed with [eno sections](https://eno-lang.org/eno/guide/elements/sections/) too.

### to

a procedure body goes until the next section of same level.

procedures can have parameters.

```eno
-- name

    factorial
    show-factorial
    start
    
-- name



-- factorial

    (n)
        ife < n 2
            1
            * n do - n 1

-- factorial



# to show-factorial number

    say: [Factorial of {number} is {factorial number}]



# to start

    name: do-once

    do-once: yes

    ## while do-once

        ask: [Choose a number] n
        call: show-factorial n

        ask: [Another one?] do-once
```

procedures can access anything defined in the level they're defined in, and in higher (containing) levels.

you can execute a procedure with `call`.

### scope

there is no shadowing. if you use a name that's already used in higher (containing) levels, then you're playing with the same toy, not another "local" one.

shadowing only occurs through functions/procedures parameters.

names declared in a section don't live outside of the section they're declared in.

### call / jump / spawn

`call` executes another procedure. the caller is suspended until the callee terminates.

`jump` jumps to another procedure. the caller is terminated and the callee "replaces" it.

`spawn` starts another procedure without suspending the caller. the caller and the callee run in parallel.

## documents

documents are sections interpreted entirely as structured values.

they start with a section labelled `document docname` where `docname` is a variable name, and goes until the next section of same level.

```
name: john

# document john

    ## Profile

        Name: John Doe
        Hair color: brown/gray
        Age:
        Hobbies:
        - Fishing
        - Horse-riding

        -- Quote
            I read the eno guide and everything made sense,
            except this non-sensical example text here.
        -- Quote

    ### Friends

        False friends:

        Close friends:
        - Jane Doe
        - Jean Doe

        Casual acquaintances:
        - John Dear

# to start

    say: john Profile 1 Name 1
```

this would output `John Doe`.

the `1` you can see in the `start` procedure are needed to access the correct `Profile` section and `Name` field, because there could be several sections/fields with the same key.
