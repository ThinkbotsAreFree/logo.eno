# logo.eno

a post-modern programming platform. nah, just kiddin'

## import

starting from two lovely languages.

- **logo** is a programming language designed in 1967 by Wally Feurzeig, Seymour Papert, and Cynthia Solomon. it is a multi-paradigm adaptation and dialect of lisp. https://people.eecs.berkeley.edu/~bh/logo.html
- **eno** is a modern plaintext data format for file-based content, designed and developed with great enthusiasm and dedication by Simon Repp. eno exists since early 2018. https://eno-lang.org/

## syntax

### assignment

assignment is written using [eno fields](https://eno-lang.org/eno/guide/elements/fields/).

```
x: 5
y: 6
z: + x y
```

algorithms are expressed in [prefix notation](https://en.wikipedia.org/wiki/Polish_notation), operators/functions first, followed by their operands/arguments.

in the example above, `z` equals `11`.

## objects

objects are written with [eno fieldsets](https://eno-lang.org/eno/guide/elements/fieldsets/).

```
myDog:
name =  Mike
breed = Alaskan husky
```

`name` and `breed` are **slots** of `myDog`. you access objects slots as you would give arguments to a function.

```
output myDog name
```

this would output `Mike`.

## lists

lists are obviously [eno lists](https://eno-lang.org/eno/guide/elements/lists/).

```
myPets:
- myDog
- myCat
- myCamel
```

you access list items by giving the list an index, as you would give a function an argument. **index of first item is 1**.

```
output myPets + 1 1
```

this would output the 2nd item, `myCat`.

## multiline assignment

eno provides a syntax for [multiline content](https://eno-lang.org/eno/guide/elements/multiline-fields/), which you can use for assignment. it **works just like regular assignment**.

```
-- oldPond

old pond
frog leaps in
water's sound

-- oldPond
```
this code would assign an haiku to the variable `oldPond`.

primary use case of multiline assignment is method definition, see below.

## contexts

for convenience, it is possible to enter in the context of an object, where its **slots are accessible as variables**.

- to enter in a context, you put the object's name alone on its line.
- to exit from a context, you put the word `end` alone on its line.

```
output myDog name

myDog
    output name
    output breed
end
```

this example above would output `Mike` twice, and `Alaskan husky`.

indentation doesn't matter.

primary use case of multiline assignment is method definition, see below.

## functions

a function starts with the word `fn`, followed by the function body expression.

the identifier is the name of the function followed by the names of its parameters.

```
-- factorial n

fn
    ife < n 1
        1
        * n factorial - n 1

-- factorial n
```

the same function could also be written on 1 line.

```
factorial n : fn ife < n 1 1 * n factorial - n 1
```

actually, the effect of `fn` is simply to quote its argument, so it's not evaluated.

there can't be variadic functions, a function's arity is always definite (because there's no parentheses). also, a function can't be applied if it's anonymous, because the parameters are part of the function identifier.

## methods

a method is a function defined in an object's slot.

```
Dog:
convertAge years = fn * 7 years
```

you call it by accessing the slot and giving the method its arguments.

```
output Dog convertAge 5
```

this would output `35`.

multiline methods can be expressed by entering an object's context and using multiline assignment.

```
myDog
-- poeticDiction poem

fn
    output &
        woof
        poem

-- poeticDiction poem
end
```

## self

in a method, the `self` word **references the receiving object**.

```
Dog:
bark = fn output & Woof self name

myDog bark
```

this would output `Woof Mike` since `self name` resolves to `myDog name`, which is `Mike`.

## prototypes

objects can be given an `isa` slot, which contains a list of **space-separated prototypes**.

```
myDog:
isa = FamilyMember Dog

Dog:
legs = 4

output myDog legs

```

this would output 4.

if a matching slot isn't found, the lookup continues **depth first** recursively in the object's protos.
