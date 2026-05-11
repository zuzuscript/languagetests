# ZuzuScript Language Tests

This repository contains shared language conformance tests for ZuzuScript.
The tests are consumed by `zuzu-perl`, `zuzu-rust`, `zuzu-js`, and the
implementation matrix.

Use Oxford English in documentation and test descriptions: mostly standard
British English, with `-ize` word endings.

## Relationship To Other Projects

These tests define expected language behaviour across runtimes. They should
be implementation-neutral unless a test is explicitly capability-specific,
such as `perl.zzs` or `javascript.zzs`.

When a runtime fails a valid test, fix the runtime. Do not weaken shared
tests to match one implementation unless the test contradicts the language
reference or accepted project direction.

## Test Format

Tests are ZuzuScript files that emit TAP. A passing test should:

- print a valid TAP plan line such as `1..N`;
- print no lines matching `not ok`;
- exit with status zero.

Use `test/more` style helpers where available through runtime test-module
paths. Keep assertions clear and focused; one file should usually cover one
language area.

## Test Scope

- `basic.zzs`, `collection/`, `types/`, and `lang/` cover general language
  semantics.
- `concurrency/` covers async and task behaviour.
- `lang/operators/paths.zzs` covers built-in path query operators.
- Capability-specific tests should skip cleanly when the host capability is
  unavailable.

Do not put stdlib module-specific coverage here unless it is inseparable
from language semantics. Prefer `stdlib/tests` for module APIs.

## Validation

Run changed tests through every relevant runtime when practical:

```bash
# from zuzu-perl
prove -lr t

# from zuzu-js
node run-ztests.js languagetests

# from zuzu-rust
cargo run --bin zuzu-rust-run-tests -- languagetests
```

The matrix project can run cross-runtime subsets with:

```bash
./run-tests.pl --only 'path-or-regex'
```

## ZuzuScript Style

Use tabs for indentation, spaces for alignment, One True Brace Style,
uncuddled `else`, whitespace around binary operators, and semicolons as
terminators. Keep code lines under 80 columns where practical.
