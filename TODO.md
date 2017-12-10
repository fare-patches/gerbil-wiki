TODO Items for Gerbil Scheme
============================

Do you think that Gerbil Scheme is a good system with a lot of promises to it? We do.
However, there is a lot of work to do to make it actually Great.

Here is a wish list of things that you and I could contribute some day,
in no particular order.
Some are small, some are big; some are easy, some are hard;
some require deep knowledge of Gerbil internals, some are accessible to newcomers;
all of them are work...

Think of the following items as "opportunities to build a language right".
The challenge is to solve these opportunities in a priority order that helps best grow the community
by attracting great hackers who would have been blocked if the previous issues hadn't been addressed,
but who will help fix the next issue for the next group of hackers.


Documentation
-------------

* A better tutorial for newcomers.

* Systematic documentation for all stdlib.
   * First, be sure to cover everything.
   * Second, make it GOOD documentation for all stdlib, with test cases/examples.

* Ensure documentation coverage using documentation checker
  hookable in the standard build & test system.

* Implement and use a Literate Programming system for all standard code and documentation?

* Document our underlying Virtual Machine,
  including all the available data types and behaviors inherited from Gambit.

* Automatically lift Gambit documentation.



Interoperability
----------------

* Better compression libraries
   * zlib streaming (for httpd)
   * more modern data compressors from Google

* Systematic interface to Unix APIs, etc.
   1. Automatically lift bindings from other Scheme or Common Lisp implementation,
      or even some safe dynamically typed blub language or statically typed functional language.
   2. Use an appropriate FFI generator if available.
   3. Be thorough and automatic in importing all interfaces from Linux, BSD, macOS.

* Mechanism to select an alternate name for the main function: `gxc --main not-main`

* `sendmail` — pipe into sendmail; long term an SMTP client.

* protobuf library. Port it from Racket? Chicken? Common Lisp? other?

* Streamline use of alternate targets: JS, Android, iOS, Java, etc.


Debuggability
-------------

* [Mostly done] A way to open a module so you can interactively debug it in the context of its bindings.
  You should be able to `(open-module :foo)` and all its imports would be available
  as well as all its imports, and only that (to start with; or with a variant).
  It would be a bit like `(include :foo)`, but it won't bork on `(export ...)` statements,
  and it would do the Right Thing(tm) with respect to namespaces.
  It would also recognize `#lang` declarations, though that might require language specifications
  to specially support opening and closing a module so that definitions may be
  added, modified, shadowed, removed.
  - (vyzo): the gxi interactive environment supports  `enter!` to run a repl in the context of a
    any module; that's close enough for debugging.

* Better discoverability:
    * `apropos` to get information on available bindings, in various contexts (also regexp to match?).
    * Function to list all the methods for a class and/or for an object.
    * `M-.` to get to the source for those bindings and methods.
    * Completion in Emacs.

* Integration with SLIME or SLY, or geiser-mode, etc.

* Tracing facility for functions, generic functions, macros, expressions, etc.

* Better stack traces
    * Link addresses back to the Gerbil source code, not just to the Gambit code generated from it.
    * Get bindings back in terms of Gerbil variables, not just the Gambit variables expanded from it.
    * Improve Gambit debugging capabilities in general.

* Better error messages
    * Add argument checking, exception catching and/or error messages everywhere.
    * When an error occurs inside a Gerbil primitive,
      make sure the error message mentions the Gerbil primitive, not just the underlying Gambit one.

* Omniscient debugger (with time-travel)


Modularity
----------

* Implement and document a good way to layer extension modules:
    * Have `GERBIL_PATH` and `GERBIL_HOME` similar to `PYTHONPATH` and `PYTHONHOME`,
      or something similar.
    * Have a community-managed standard namespace hierarchy for extensions.
        * Have utilities for managing "modules" within that namespace,
          in the style of `raco` and/or `nix`.
        * Support both immutable versioned software and mutable software under external source-control.
    * Define naming conventions.
        * You're free to use any names you want for your modules locally.
          But when you publish software or use software by a third party, follow rules to avoid clashes.
        * `gerbil/` and `std/` are reserved for things included by gerbil itself.
        * Anyone who controls a DNS name should be able to use it as toplevel module name
          (e.g. `tunes.org/`).
        * Names under `ext/` can be open as register-yourself free for all on the Gerbil wiki.
        * Some names, (like `utils/` ?) are reserved for the local end-user.
          You must rename before publishing.
        * Other top-level names are curated by a community project.
          where active maintainers earn commit rights.
        * Rule of thumb: scarce short names for a general category (e.g. `net/`) should not be hoarded.
        * Good community players can reserve a non-clashy top-level name (e.g. `vyzo/`).
        * Use relevant name deep enough in one of the above hierarchies to avoid clashes.
   * A "community standard library", may curate code under many top-level names (`net/` `db/` etc.)
        * Standard modules should aim at top-level quality, fully solve their respective domain.
        * Includes functionality that is not necessary to bootstrap Gerbil itself, but generally useful.
        * Some of the code currently in Gerbil could be moved there (e.g. `db/`)
        * Some code there could be moved to Gerbil itself if useful enough to simplify the bootstrap.

* A better build system
    * Must avoid clashes between incompatible versions of Gerbil for software in the same `GERBIL_PATH`.
    * Must automatically and accurately detect broken dependencies and rebuild accordingly.
      See Bazel, nix.
    * Support multiple simultaneous build targets, e.g. mix of Javascript and C backends.
    * Ideally, see this [ngnghm post on build systems](https://ngnghm.github.io/blog/2016/04/26/chapter-9-build-systems/)

* Auto-detecting packages from file path relative to a `namespace.ss` file or some such.
  Having to explicitly declare a `package:` in each file redundantly with its location from
  the `GERBIL_LOADPATH` is only one chance to get things out of synch, with catastrophic
  infinite build loop if `a` depends on `b/x` which depends on `c/x`,
  but `b/x` incorrectly declares its package as `c` because it was originally copy-pasted.
  [Bazel](https://bazel.build/) does it right here when it detects a file's package by looking
  for a `BUILD` file in the current directory or up its "ancestry".
  I think Bazel does it wrong, however, when it has two levels of namespace hierarchy:
  packages identified by `BUILD` files within a workspace identified by a `WORKSPACE` file
  (plus yet a third level of hierarchy to cope with "external repositories").
  It is better to have to have a single hierarchy, in which parts are "mounted"
  below a given `WORKSPACE` or `BUILD` file (or its equivalent).


Data Structures
---------------

* Better unify struct, class, actors, messages, etc.
    * Make it easy to go from function to object to actor and back, etc.

* Better object and actor system. Aim at CLOS features. Plus optional typing?

* Interfaces in the style [Lisp-Interface-Library](https://github.com/fare/lisp-interface-library)
  combining both parametric and ad hoc polymorphism in a Scheme context.
  Use it as the basis for any implementation of Haskell or similar on top of Gerbil.

* A library of pure functional data structures and algorithms,
  taking advantage of both parametric and ad hoc polymorphism,
  in the style of [Scalaz](https://github.com/scalaz/scalaz).

* Some language to deal with Linear Logic, unify pure and stateful datastructures, etc.
  Then the data structure library also becomes a mutable data structure library.


Control Structures
------------------

* Add delimited control to Gambit then Gerbil.
   * Make it work well with threads, exceptions, etc.
   * See e.g. Racket or Guile.

* Daemon management.
   * Actors that robustly supervise and restart computation in an OS spawned child subprocess.
   * cron-like timed action functionality.
   * Eventally, replace systemd?
   * Implement the Erlang protocols.


Other Language Features
-----------------------

* Add Racket-like Chaperones and Impersonators.

* Add Racket-like contracts.

* Add optional type systems.
    * At a higher level, see Typed Racket, Hackett.
    * At a lower level, see Shen's types, Haskell's typing extension mechanism.

* More generally, improve (partial) compatibility with Racket.
    * How much can we steal from Racket source and extensions, and/or contribute to them?


More Libraries
--------------

* data plotting library.
    * Deploy using httpd+js ?
    * Which JS lib: D3? Google Charts? ploty? processingjs.org?

* Exact decimal numbers. Port Common-Lisp's library wu-decimal ?

* Import all SRFIs and/or provide better alternatives.
  Upstream the better alternatives as SRFIs themselves.


Robustness
----------

* Tests
    * Good regression testing library, covering all of standard code.
    * More introspection / inference for the build and test system,
      so no code is left mistakenly untested.
    * build everything in a directory, run all the tests

* More graceful handling of out-of-memory errors.

* Code coverage, data coverage, test coverage, etc.
    * [First-Class Implementations](https://github.com/fare/climbing).
    * Provide Erlang-style robustness in face of failure, based on FCI.
    * Universal debugger based on FCI.
    * Code instrumentations based on FCI.


Languages
---------

* `#lang` languages will also need conventions for naming, support from the system, etc.


Speed
-----

* While Gambit produces reasonably fast code,
  its compilation process itself is slow because it invokes a C compiler.
  Add a native backend to Gambit and/or Gerbil? A Chez Scheme backend to either?
  A home-grown nanopass compiler using FCI?


Syntax
------

* [Fare] I would like a standard prefix syntax for a wrapping expression, so Java/Python/OCaml/F#/etc.
  annotations can be written without having to both add a form before and a ) after.
