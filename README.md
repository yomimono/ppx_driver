Ppx\_driver - driver for AST transformers
=========================================

A driver is an executable created from a set of OCaml AST transformers
linked together with a command line frontend.

The aim is to provide a tool that can be used to:

- easily view the pre-processed version of a file, no need to
  construct a complex command line: `ppx file.ml` will do
- use a single executable to run several transformations: no need to
  fork many times just for pre-processing
- improved errors for misspelled/misplaced attributes and extension
  points

## Building a custom driver

To build a custom driver, simply link all the AST transformers
together with the `ppx_driver.runner` library at the end:

    ocamlfind ocamlopt -o ppx -linkpkg -linkall \
      -package sexplib.ppx -package ... \
      -package ppx_driver.runner

Note that the `-linkall` is important here, as otherwise the various
rewriters won't get linked into the final executable.

## The driver as a command line tool

```
$ ppx -help
ppx.exe [extra_args] [files]
  -as-ppx                Run as a -ppx rewriter (must be the first argument)
  -o FILENAME            Output file (use '-' for stdout)
  -loc-filename STRING   File name to use in locations
  -dump-ast              Dump the marshaled ast to the output file instead of pretty-printing it
  -dparsetree            Print the parsetree (same as ocamlc -dparsetree)
  -impl FILE             Treat the input as a .ml file
  -intf FILE             Treat the input as a .mli file
  -help                  Display this list of options
  --help                 Display this list of options
```

When passed a file as argument, a ppx driver will pretty-print the
code transformed by all its built-in AST transformers. This gives a
convenient way of seeing the code generated for a given
attribute/extension.

A driver can simply be used as the argument of the `-pp` option of the
OCaml compiler, or as the argument of the `-ppx` option by passing
`-as-ppx` as first argument:

```
$ ocamlc -c -pp ppx file.ml
$ ocamlc -c -ppx "ppx -as-ppx" file.ml
```

When used with `-pp`, the driver will also interpret `#`-directives
using [ppx_optcomp](http://github.com/janestreet/ppx_optcomp).
