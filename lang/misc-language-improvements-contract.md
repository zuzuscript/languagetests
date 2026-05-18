# Miscellaneous Language Improvements Contract

## Status

This document defines shared expected behaviour for future conformance tests
and runtime implementation of the miscellaneous language improvements. It
covers call argument spread, collection copying, the `default` operator, and
declaration unpacking.

The contract is implementation-neutral. Runtime-specific parser, evaluator,
and library changes should match the semantics here unless a later accepted
language decision updates this document.

## Call Argument Spread

The `...expr` spread form is valid only in function, method, constructor, and
dynamic member call argument lists. Existing `...` range and literal syntax is
unchanged outside call arguments.

Call arguments are evaluated from left to right. A spread argument expands at
its source position in the argument list after its operand has been evaluated.

Array spread operands expand into positional arguments. Dict and PairList
spread operands expand into named arguments. Dict pair iteration uses the
existing Dict order. PairList spread preserves pair order and duplicate keys.

Duplicate named call arguments preserve argument order. Existing callee
argument handling decides how duplicates are interpreted.

Spreading any operand other than Array, Dict, or PairList throws.

## Collection Copy

Array, Bag, Set, Dict, and PairList gain a `copy()` method.

`copy()` returns a new outer collection of the same type as the receiver. The
copy is shallow: contained values are reused rather than deeply copied.
Mutating the copied outer collection must not mutate the original outer
collection.

PairList copies preserve pair order and duplicate keys.

## `default` Operator

`default` is a binary word operator. It is left associative. Its precedence is
lower than member, index, and call postfix expressions.

In call arguments, `... opts default defaults` parses as
`...(opts default defaults)`.

The left operand must be a Dict, PairList, or null. A null left operand is
treated as an empty PairList, and the result type is PairList. The right
operand must be a Dict or PairList. `x default null` throws. Any other invalid
operand combination throws.

The result type matches the left operand type except when the left operand is
null, where the result type is PairList. The operation starts with a shallow
copy of the left operand. It then iterates right operand pairs in the existing
right operand order and adds a right pair only when the original left operand
lacks that key. Presence is tested with existing `has(key)` behaviour.

For a Dict result, at most one right pair is added for each missing key. For a
PairList result, all duplicate right pairs are preserved when their key is
missing from the original left operand.

## Declaration Unpacking

Declaration unpacking is supported only in `let` and `const` declarations.
Ordinary assignment destructuring is not part of this contract.

The source expression is evaluated once. The source value must be a Dict or
PairList; any other source value throws.

Defaults are evaluated lazily only when the requested key is absent. Present
keys bind their stored value, including null. Missing keys without defaults
bind null.

PairList extraction uses existing `PairList.get(key)` behaviour. Duplicate
local names in one unpacking declaration are invalid.

Declaration unpacking has no new rollback or transactionality guarantee beyond
existing declaration behaviour.

Supported binding forms include shorthand bindings, defaults, typed bindings,
aliases, typed aliases, computed keys, and `but weak`. Typed bindings use the
existing typed-identifier order. Aliases use `key: binding` form. `but weak`
follows existing weak lexical storage semantics.
