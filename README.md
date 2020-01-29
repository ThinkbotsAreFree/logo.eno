# logo.eno

the logo.eno programming platform.

## import

i start from 2 languages i love:

- **logo** is an educational programming language designed in 1967 by Wally Feurzeig, Seymour Papert, and Cynthia Solomon. it is a multi-paradigm adaptation and dialect of lisp. https://people.eecs.berkeley.edu/~bh/logo.html
- **eno** is a modern plaintext data format for file-based content, designed and developed with great enthusiasm and dedication by Simon Repp. eno exists since early 2018. https://eno-lang.org/

## syntax

### assignment

assignment is written using [eno fields](https://eno-lang.org/eno/guide/elements/fields/).

```
x: 5
y: 6
z: + x y
```

algorithms are written in [prefix notation](https://en.wikipedia.org/wiki/Polish_notation), operators/functions first, followed by their operands/arguments.

in the example above, `z` equals `11`.

## objects

objects are expressed using [eno fieldset](https://eno-lang.org/eno/guide/elements/fieldsets/).

```
myDog:
name =  Mike
breed = Alaskan husky
isa =   Dog
```

`name`, `breed` and `isa` are **slots** of `myDog`. you access objects slots as you would give arguments to a function:

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

eno provides a syntax for multiline content, which we use for assignment.

```
-- oldPond

old pond
frog leaps in
water's sound

-- oldPond
```
this code assigns an haiku to the variable `oldPond`.
