# 1.0 plan and priorities

We don't have time for big changes or big features (dev time + time to marinate). We know vaguely where we're going (see other design PRs).

High level priorities:

- Stability and polish
  - bugs, performance, glitches, etc.
- User experience
  - Goal: KCL is easy to read and comprehend
  - Feedback is useful (though perhaps not pretty)
    - IDE experience
    - Error messages (parser in particular)
- Supporting core new features in engine, as required
- Future compatibility
- Not making tech debt worse
  - And ideally a little bit better

Related:

- [Modelling app roadmap](https://github.com/orgs/KittyCAD/projects/35)
- [1.0 milestone](https://github.com/KittyCAD/modeling-app/milestone/2)
- [CAD primitives roadmap](https://github.com/KittyCAD/modeling-app/issues/729)

Priorities in **bold**, dependencies in *italics*, task size in t-shirt sizes (taking into account breaking change management; m-l means uncertainty about which, not 'somewhere in between').

## As required

### Bug fixing

**P0**:

- high priority bugs as they emerge
- plan to seek out and address lower-priority bugs during feature freeze


### Non-language feature work

Priority follows from other team's priorities.

New or more flexible std lib functions and code mods are expected as side effects of other work.

New features which might require significant work:

- [Multiple profiles in Sketches](https://github.com/KittyCAD/modeling-app/issues/1876)
- Assemblies?


### Pre-paying technical debt

**P1**:

- Don't make things worse by adding hacks, especially around the API of KCL

**P3**:

- Supplying user-side IDs to engine
- code mod API


## Language priorities

**P1** (priority ordered):

- reserve keywords (s)
  - https://github.com/KittyCAD/modeling-app/issues/4486
  - and give meaning to underscore in identifiers
- function args
  - named and optional args (m; *$0*)
  - only one non-named arg (m; *$1*)
    - ideally we make it the first arg, but doesn't have to be
    - could require it be called `self`, but I don't think we need to and that makes back-compat harder
- simple tidying changes
  - automatic conversion of round floats to ints, remove the `int()` function (s)
  - record init: `:` -> `=`  (s)
  - function decl: remove `=` and `=>`, `:` for type annotations (s)
    - return type syntax?
  - convert special strings (e.g., `END`, `XY`) to constants (s)
- std lib
  - organise into modules (for docs, at least) (l)
    - potential issues: namespacing, collisions, receivers, name shadowing for std
  - ensure consistency, remove unnecessary or uncertain functions, check for anything we're badly missing (m)
    - *depends somewhat on $3, we should do this in any case, but how we do it will depend on whether $3 is achieved*
- support optionally-implicit `%` in pipelines (m; *$2; depends on $1*)


**P2** (priority ordered):

- std lib
  - remove use of anon records (l; *depends on $0*)
- immutable model
  - well-defined rules for when engine calls cause a rendered object, add `construction` flag where necessary (m-l)
- tagging
  - support `as` in pipelines (m)
  - remove `$` syntax (m)
  - where std lib functions take tags, ensure they're unambiguous and work with `as` variables (m)


**P3** (priority ordered):

- std lib
  - reduce number of functions by using optional args (m-l; *$3; depends on $0*)
- `var`/`roughly` syntax for unconstrained points/numbers (m-l)
  - changes how we do 'fixed point' constraints
 
## User feedback

TODO I don't really know what needs doing here, how it should be prioritised, or how it should be prioritised relative to the above language work.

- Error messages
  - Parser
- IDE functionality
  - Suggestions
  - ...

## Docs and education material

Planned for feature freeze period (**P3** until then). We should do some planning before-hand (**P0**).

- std docs
- guide
- reference
- videos and other tutorial stuff


## Open questions

- Tags (these might be things we want to change for 1.0), in particular the current approach of collecting all tags into a `tags` field and allowing users of the geometry to refer to internal tags seems sub-optimal:
  - source names are reified at runtime
  - construction (including tag names) of an object should be private by default
  - having a `tags` field violates the principle of tags being just variables
  - a more principled way to do it is `export` of variables, but this 'works' for block syntax sketches, not so much for pipelines, and not for deeply nested tags
- Any other precautions we should take for future compatibility? Including (but not limited to):
  - higher order functions
    - used in `patternTransform` pattern function, `reduce` and `map`
    - are we happy committing to supporting higher order functions?
    - how would they fit into a type system?
  - Units of Measure
    - syntax (is `num id` valid syntax today?)
    - modules with units? (shouldn't be an issue since we only support imports within a project)
  - Are there things we want to 'tighten up'?
    - Indexing records (`foo['fieldName']`)
    - Implicit conversion from array to record (pretty sure this is unintentional and should not work)
    - Make arrays uni-typed?
    - `KclValue` top type? (IMO this is the internals of the implementation leaking into the surface syntax and should be removed)


## Postponed

- [over-eager mutation](https://github.com/KittyCAD/modeling-app/issues/2728)
- Performance or tech-debt work
- point syntax (perhaps just change from `[]` to `()`? Or require `pt`/`vec`?)
- [construction geometry](https://github.com/KittyCAD/modeling-app/issues/1553), implicit or explicit `show`
- extensions to tagging
  - 'dynamic tagging', e.g., for edges of a `polygon`
