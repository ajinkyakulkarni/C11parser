C11parser
=========

##### A correct C11 parser written using Menhir and OCaml

The operation and design of this parser are described in detail in the
following journal paper (in process of review for publication):

> A simple, possibly correct LR parser for C11<br/>
> Jacques-Henri Jourdan and François Pottier<br/>
> 2017<br/>

How to use the parser?
----------------------

You need to have installed OCaml, the Menhir parser generator and
ocamlbuild. In order to build it, you can just type `make`.

The executable that is produced takes a preprocessed C file in its
standard input and raises an exception in the case of a parse
error.

The following command-line options are available:
  - `-std {c89|c90|c99|c11}`

    Sets which grammar to use.

    `c89` and `c90` tells the parser to use the old grammar, where
    declaration were not required to have a type specifier, in which
    case "int" was used (it still recognizes C99 and C11 constructs).

    `c99` and `c11` use the new, simpler grammar: declarations are
    required to have a type specifier, and the scoping rules are
    different.

  - `-c99-scoping`

    Use the C99/C11 scoping rules even though the old C89/C90 grammar
    is used. This is always set when using the new grammar.

  - `-atomic-permissive-syntax`

    The C11 standard forbids the use of an opening parenthesis
    immediately following an atomic type qualifier. This is intended
    to avoid a possible ambiguity with _Atomic used in a type
    specifier. This parser disambiguates this apparent conflict so
    that this restriction can be lifted safely.

If you want to use this parser in a C front-end, you should fill the
semantic actions of .mly files with your own code for building your
AST:
  - The file `parser.mly` contains a C99/C11 compliant parser. It
    mostly follows the grammar of the C11 standard.
  - The file `parser_ansi_compatible.mly` is compliant with C89, C99 and
    C11 (depending on the options given in options.ml). It is
    significantly more complex than `parser.mly`.

The test suite
--------------

We provide, in the tests directory, a series of tests that are
particularly difficult to handle in a correct C parser. They are all
valid C11 fragments, except for:
  - The files whose name finishes with .fail.c
  - `atomic_parenthesis.c`, which represent an unnecessary restriction in
    the syntax presented in the C11 standard.
