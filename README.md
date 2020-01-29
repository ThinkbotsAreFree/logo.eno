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

objects are expressed using [eno fieldset](https://eno-lang.org/eno/guide/elements/fieldsets/)

```
myDog:
name =  Mike
breed = Alaskan husky
isa =   Dog
```

`name`, `breed` and `isa` are **slots** of `myDog`. `isa` is a special slot containing proto chains, we'll get to that later. you access objects slots as you would give arguments to a function:

```
output myDog name
```

## lists

