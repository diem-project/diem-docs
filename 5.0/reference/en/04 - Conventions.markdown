Conventions are useful. Not only they allow the code to be clean and consistent, but they also allow a lot of automation.

Diem follows all symfony conventions, and adds some.

## Syntaxes

When naming something in the code ( a model, a module, a variable, a method... ), there are three ways to do it:

### Underscored

Lowercase, words separated by an underscore.
[code php]
this_is_an_underscored_word
[/code]

When use the underscore syntax ?

- database table names
- database field names
- php functions ( typically helper functions )
- array keys ( like dependency injection service parameters )

### Camelized

Uppercased first letter, words separated by an uppercased letter.
[code php]
ThisIsAnUppercasedWord
[/code]

When use the camelized syntax ?

- models class name

### Modulized

Lowercased first letter, words separated by an uppercased letter.
[code php]
thisIsAModulizedWord
[/code]

When use the modulized syntax ?

- methods
- variables
- modules
- templates
- non model class names

## Conversion
Diem provides methods to transform a string from a syntax to another one.

[code php]
dmString::underscore($string) // transform $string to underscored syntax
dmString::camelize($string)   // transform $string to camelized syntax
dmString::modulize($string)   // transform $string to modulized syntax
[/code]

## Coding standarts
All the symfony coding stadarts are applied and recommended, without any addition.

Learn about [symfony coding standarts](http://trac.symfony-project.org/wiki/HowToContributeToSymfony#CodingStandards)