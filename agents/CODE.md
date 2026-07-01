# Coding Practices

### Contract-first changes

* Update contracts, schemas, interfaces, type definitions, and all in-repo callers in the same change.
* Do not introduce compatibility layers, deprecation paths, aliases, adapters, facades, wrappers, bridges, or shims unless explicitly requested.
* Prefer a clean final design over transitional architecture when all affected code can be updated together.

### Minimal surface area

* Implement only the minimum code required to satisfy the request.
* Extend existing functions, modules, types, and structures before introducing new files, classes, abstractions, frameworks, or configuration.
* If the current structure prevents a clean implementation, refactor the relevant area first and implement the feature in the same change.
* Every new abstraction must solve a demonstrated present-day requirement.

### Remove dead code

* Delete code that is clearly obsolete, unreachable, unused, or superseded by the change.
* If reachability or usage cannot be determined confidently, retain it and add a targeted TODO explaining what must be verified before removal.
* Favor deletion over deprecation for internal code that is no longer needed.

### Maintainability over transition paths

* Optimize for the long-term codebase state, not temporary migration convenience.
* Avoid preserving outdated APIs solely to reduce the amount of work required in the current change.
* Minimize future maintenance burden whenever implementation costs are comparable.

## Refactoring Guardrails

* Do not perform speculative refactors.
* Do not introduce abstractions based on hypothetical future requirements.
* Do not create plugin systems, extension points, dependency injection layers, generic utility frameworks, configuration frameworks, factories, adapters, or wrappers unless required by the current task.
* Do not extract helpers solely because a block of code feels large.
* Refactor only when it directly improves correctness, clarity, maintainability, or enables the requested change.
* Every abstraction should remove existing duplication or solve a demonstrated problem.

### Match existing conventions

* Inspect surrounding code before modifying it.
* Follow established conventions for:

  * Naming
  * Formatting
  * Import organization
  * Error handling
  * Logging
  * Testing
  * File structure
  * Dependency management
* Conform even when a different approach would be preferred in a new project.

### Minimal diffs

* Change only what is required.
* Avoid unrelated cleanup, renames, formatting-only changes, dependency upgrades, file moves, or style corrections unless necessary for the task.
* Keep blast radius as small as possible.

### Verify, don't assume

* Validate changes using available verification mechanisms.
* Prefer:

  * Tests
  * Type checking
  * Linters
  * Formatters
  * Build systems
  * Runtime validation
  * Static analysis
* Report actual results.
* Never claim functionality works without verification when verification is available.
* Never use phrases such as:

  * "should work"
  * "appears fixed"
  * "likely resolved"
    when verification is possible.

### Handle ambiguity appropriately

* If a wrong assumption could cause:

  * Data loss
  * Security issues
  * API breakage
  * Contract violations
  * Significant rework
    ask one targeted question.
* If ambiguity is low-cost, state the assumption briefly and proceed.