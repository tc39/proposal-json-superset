# Subsume JSON (a.k.a. JSON ⊂ ECMAScript)

A proposal to extend ECMA-262 syntax into a superset of JSON.

## Status
This proposal is at stage 4 of [the TC39 Process](https://tc39.github.io/process-document/) and is scheduled to be included in ES2019.

## Champions
* Mark Miller
* Mathias Bynens

## Motivation
ECMAScript claims JSON as a subset in [`JSON.parse`](https://tc39.github.io/ecma262/#sec-json.parse), but (as has been well-documented) that is not true because JSON strings can contain unescaped U+2028 LINE SEPARATOR and U+2029 PARAGRAPH SEPARATOR characters while ECMAScript strings cannot.

These exceptions add unnecessary complexity to the specification and increase the cognitive burden on both implementers and users, allowing for the introduction of subtle bugs.
Also, as a lesser but concrete corrolary problem, certain source concatenation and construction tasks currently require additional steps to process valid JSON into valid ECMAScript before embedding it.

## Proposed Solution
JSON syntax is defined by [ECMA-404](http://www.ecma-international.org/publications/standards/Ecma-404.htm) and permanently fixed by [RFC 7159](https://tools.ietf.org/html/rfc7159), but the <i>DoubleStringCharacter</i> and <i>SingleStringCharacter</i> productions of ECMA-262 can be extended to allow unescaped U+2028 LINE SEPARATOR and U+2029 PARAGRAPH SEPARATOR characters.

## Examples
```js
const LS = " ";
const PS = eval("'\u2029'");
```

## Discussion
### Backwards Compatibility
This change is backwards-compatible.
User-visible effects will be limited to the elimination of SyntaxError completions when parsing strings that include unescaped LINE SEPARATOR or PARAGRAPH SEPARATOR characters, which in practice are extremely uncommon (we also hope to [collect data](https://bugs.chromium.org/p/v8/issues/detail?id=6827) for the related question of how often those characters are used as line terminators _outside_ of strings).

### Regular Expression Literals
Unescaped LINE SEPARATOR and PARAGRAPH SEPARATOR characters are not currently allowed in regular expression literals either, but that restriction has been left in place because regular expression literals are not part of JSON.

### Template Literals
Unescaped LINE SEPARATOR and PARAGRAPH SEPARATOR characters are already allowed in template literals.

### Validity
Encompassing JSON syntax does not imply the _semantic_ validity of all JSON text.
For example, `({ "__proto__": 1, "__proto__": 2 })` triggers an early SyntaxError under Annex B, and will continue to do so.
However, it will become possible to generate a parse tree from `({ "LineTerminators": "\n\r  " })`.

### Objections
Allen Wirfs-Brock [argues](https://esdiscuss.org/topic/json-text-is-not-a-subset-of-primaryexpression#content-3) that ECMAScript and JSON are distinct and don't need an easily-described relationship, and is concerned that acceptance of this proposal would be used as leverage by others attempting to "fix JSON".

The latter is addressed by this proposal explicitly acknowledging JSON syntax as a fixed point.
As for the former, it is clear from the definition of `JSON.parse` that ECMAScript benefits from the similarity (e.g., step 4 includes "parsing and evaluating <i>scriptText</i> as if it was the source text of an ECMAScript <i>Script</i>").
This proposal argues that eliminating the need for an alternate <i>DoubleStringCharacter</i> production and the associated cognitive burden in reasoning about the two languages is sufficiently beneficial to justify such a change.

## Conformance tests

Test262 tests are here: <https://github.com/tc39/test262/pull/1544>

## TC39 meeting notes

- [September 2017](https://tc39.github.io/tc39-notes/2017-09_sept-27.html#12ie-make-ecmascript-a-syntactic-superset-of-json-for-stage-1)
- [November 2017](https://tc39.github.io/tc39-notes/2017-11_nov-28.html#9iic-make-ecmascript-a-syntactic-superset-of-json-for-stage-2)
- [January 2018](https://tc39.github.io/tc39-notes/2018-01_jan-23.html#13iib-make-ecmascript-a-syntactic-superset-of-json-for-stage-3)
- [May 2018](https://tc39.github.io/tc39-notes/2018-05_may-22.html#11ie-ecmascript-as-a-superset-of-json)

## Implementations

- [V8](https://bugs.chromium.org/p/v8/issues/detail?id=7418), shipping in Chrome 66
- JavaScriptCore, shipping in [Safari Technology Preview 49+](https://developer.apple.com/safari/technology-preview/release-notes/#release-49)
- [Babel](https://github.com/babel/babel/tree/master/packages/babel-plugin-proposal-json-strings)

## Specification
The specification is available in [ecmarkup](spec.emu) or [rendered HTML](https://tc39.github.io/proposal-json-superset/).
