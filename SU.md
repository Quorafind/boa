# Boa for Su

This branch (`su/0.22`) is Boa 0.22.0 (`v0.22`) with the fixes Su needs to run Obsidian community
plugins unchanged. Su takes every Boa crate from here through `[patch.crates-io]`, so the crates
always agree with one another.

## What it carries

- `boa_ast`, `scope_analyzer.rs`: a class method's computed name (`class C { [key]() {} }`) is
  visited by the scope analysis, as a field's and an object method's are. Without it a binding the
  name used from an enclosing function was read from the wrong environment and the class threw
  `ReferenceError: access of uninitialized binding` (bundles built with Effect, Obsidian Day
  Planner among them). `ClassMethodDefinition::name` is `pub(crate)` for it.
- `boa_ast`, `scope.rs`: `this` in an arrow function inside another arrow function
  (`method() { return () => { f(x => this.y) } }`) escapes past the arrows to the function that
  owns `this` (`Scope::mark_arrow`, set in `visit_function_like`). Before, the method's `this` was
  not kept and the inner arrow read `undefined` (Admonition's and Day Planner's settings).

- `boa_engine`, `builtins/regexp`: a compiled pattern is kept per thread by its source and flags
  (`COMPILED`, up to 1024 of them), so a regular expression literal evaluated again — once per
  line of a note, in Dataview's parser — no longer compiles its pattern every time.

## Keeping it

Rebase onto the next release tag when Su moves to it, and drop what upstream has fixed.
