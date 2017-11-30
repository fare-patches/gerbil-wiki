TODO Items for Gerbil Scheme
============================

Do you think that Gerbil Scheme has a lot of promises to it? We do.
However, there is a lot of work to do to make it actually good.
Here is a wish list of things that you and I could contribute some day,
in no particular order.
Some are small, some are big; some are easy, some are hard;
some require deep knowledge of Gerbil internals, some are accessible to newcomers;
all of them are work...


Documentation
-------------

* Systematic documentation for all stdlib.
   * First, be sure to cover everything.
   * Second, make it GOOD documentation for all stdlib, with test cases/examples.

* Ensure documentation coverage using documentation checker
  hookable in the standard build & test system.

* A better tutorial for newcomers.

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
   * Step 1:
       Automatically lift bindings from other Scheme or Common Lisp implementation,
       or even some safe dynamically typed blub language or statically typed functional language.
   * Step 2:
       Use a FFI generator if available.
   * Step 3:
       Be thorough and automatic in importing all interfaces from Linux, BSD, macOS.

* Mechanism to select an alternate name for the main function: `gxc --main not-main`

* `sendmail` -- pipe into sendmail; long term an SMTP client.

* protobuf library. Port it from Racket? Chicken? Common Lisp? other?


Debuggability
-------------

* A way to open a module so you can interactively debug it in the context of its bindings.
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

* Omniscient debugger (with time-travel)


Modularity
----------

* Implement and document a good way to layer extension modules:
    * Have GERBIL_PATH and GERBIL_HOME similar to PYTHONPATH and PYTHONHOME, or something like that.
    * Have a community-managed standard namespace hierarchy for extensions.
        * Have utilities for managing "modules" within that namespace, in the style of raco and/or nix.
        * Support both immutable versioned software and mutable software under external source-control.
    * Define naming conventions.
        * You're free to use any names you want for your modules locally.
          But when you publish software or use software by a third party, follow rules to avoid clashes.
        * gerbil/ and std/ are reserved for things included by gerbil itself.
        * Anyone who controls a DNS name should be able to use it as toplevel module name
          (e.g. tunes.org/).
        * Names under ext/ can be open as register-yourself free for all on the Gerbil wiki.
        * Some names, like utils/ are reserved for the local end-user.
          You must rename before publishing.
        * Other top-level names are curated by a community project
          where active maintainers earn commit rights.
        * Rule of thumb: scarce short names for a general category (e.g. net/) should not be hoarded.
        * Good community players can reserve a non-clashy top-level name in the hierarchy (e.g. vyzo/).
        * Use relevant name deep enough in one of the above hierarchies to avoid clashes.
   * A "community standard library", may curate code under many top-level names (net/ db/ etc.)
        * Standard modules should aim at top-level quality, fully solve their respective domain.
        * Includes functionality that is not necessary to bootstrap Gerbil itself, but generally useful.
        * Some of the code currently in Gerbil could be moved there (e.g. db/)
        * Some code there could be moved to Gerbil itself if useful enough to simplify the bootstrap.

* A better build system
    * Must avoid clashes between incompatible versions of Gerbil for software in the same `$GERBIL_PATH`
    * Must automatically and accurately detect broken dependencies and rebuild accordingly.
      See Bazel, nix.


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
