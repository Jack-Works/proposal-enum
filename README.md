# Enum proposal

## Champion group & Authors

- [Ron Buckton](https://github.com/rbuckton/)
- [Rick Waldron](https://github.com/rwaldron/)
- [Jack Works](https://github.com/Jack-Works/)

## Motivation

1. Provide enum ([Enumerated type](https://en.wikipedia.org/wiki/Enumerated_type)) in JavaScript.
    - This pattern is widely used in JavaScript.

2. Provide [Abstract Data Type](https://en.wikipedia.org/wiki/Abstract_data_type) based on enum.
    - It is implemented in many languages in different names like "enum variants" (Rust), "Data Constructor" (Haskell) or "tagged union" (TypeScript).

## Compare to the previous proposals

[Roadmap, design goal and relationship to the prior proposals #2](https://github.com/Jack-Works/proposal-enum/discussions/2)

Please go to [discussion forum](https://github.com/Jack-Works/proposal-enum/discussions) for design details!

https://github.com/rwaldron/proposal-enum-definitions
https://github.com/rbuckton/proposal-enum

The current proposal doesn't have a detailed design to be compared with. Currently I'm focusing on figuring out the design constraints before any semantics or syntax.

## Design

Before the concreate design, we should decide the design constraints first.

### Normal enum

- Enum should have support for symbol, number, string and bigint.

### ADT enum

1. work well with TypeScript.
2. work with [structs proposal](https://github.com/tc39/proposal-structs) (constraint by @rbuckton and @syg), normal objects and [Record & Tuples](https://github.com/tc39/proposal-record-tuple) (for consistency).
3. work well with [pattern matching proposal](https://github.com/tc39/proposal-pattern-matching).

### Decided

- Enum is a frozen, non-extensible normal object
- Enum is a Declaration
- Support number, bigint, string, and symbol
- Support bitflag pattern

### Decided not to

- Allow duplicated keys
- Hoistable declarations

### To be decided

- [More static / More dynamic](https://github.com/Jack-Works/proposal-enum/discussions/4)
- [Extending a current enum](https://github.com/Jack-Works/proposal-enum/discussions/5)

```ts
enum A { Case1 }
enum B extends A { Case2 }

B.Case1
```

- [Mix different types in the enum value (e.g. number + string + symbol)](https://github.com/Jack-Works/proposal-enum/discussions/8)

```ts
enum A { A = 1, B = "a" }
```

- [Reverse mapping](https://github.com/Jack-Works/proposal-enum/discussions/9)

```ts
enum A { B = 1 }
A.B === 1
some API to get the name of 1 === "B"
```

- [Computed key](https://github.com/Jack-Works/proposal-enum/discussions/11)

```ts
enum A { ["a" + "b"]: 1 }
```

- [Computed value](https://github.com/Jack-Works/proposal-enum/discussions/12)

```ts
enum A { B = expr() }
```

- [Autoincrement in bigint and number](https://github.com/Jack-Works/proposal-enum/discussions/13)

```ts
enum A { A = 0, B }; A.B === 1
```

- [Avoid syntax collision with flow.js and TypeScript](https://github.com/Jack-Works/proposal-enum/discussions/14)

```ts
enum A {} // valid TypeScript and flow.js today
enum A of symbol {} // valid flow.js today
```

- [Default member type (number, string, or symbol?)](https://github.com/Jack-Works/proposal-enum/discussions/16)

```ts
enum A { B }; typeof A.B === ?
```

- [Have `[Symbol.iterator]` on the enum object](https://github.com/Jack-Works/proposal-enum/discussions/17)

```ts
enum A { A, B, C }; for (const [key, value] of A);
```

- [Should enum be hoistable declaration?](https://github.com/Jack-Works/proposal-enum/discussions/18)
