# Subsume JSON

A proposal to extend ECMA-262 syntax into a superset of JSON.

## Status
This proposal is at stage 0 of [the TC39 Process](https://tc39.github.io/process-document/).

## Champions
There are no current champions, but Mark Miller has expressed interest.

## Motivation
ECMAScript claims JSON as a subset in [`JSON.parse`](https://tc39.github.io/ecma262/#sec-json.parse), but (as has been well-documented) that is not true because JSON strings can contain unescaped U+2028 LINE SEPARATOR and U+2029 PARAGRAPH SEPARATOR characters while ECMAScript strings cannot.

These exceptions add unnecessary complexity to the specification and increase the cognitive burden on both implementers and users, allowing for the introduction of subtle bugs.
Also, as a lesser but concrete corrolary problem, certain source concatenation and construction tasks currently require additional steps to process valid JSON into valid ECMAScript before embedding it.

## Proposed Solution
JSON syntax is permanently fixed by [RFC 7159](https://tools.ietf.org/html/rfc7159), but the <i>DoubleStringCharacter</i> and <i>SingleStringCharacter</i> productions of ECMA-262 can be extended to allow unescaped U+2028 LINE SEPARATOR and U+2029 PARAGRAPH SEPARATOR characters.

## Examples
```js
const LS = " ";
const PS = eval("'\u2029'");
```

## Discussion
This change is backwards-compatible with all versions of ECMAScript.
User-visible effects will be limited to the elimination of SyntaxError completions when parsing strings that include unescaped LINE SEPARATOR or PARAGRAPH SEPARATOR characters, which in practice are extremely uncommon.

## Specification
The specification is available in [ecmarkup](spec.emu) or [rendered HTML](https://gibson042.github.io/ecma262-proposal-json-superset/).
