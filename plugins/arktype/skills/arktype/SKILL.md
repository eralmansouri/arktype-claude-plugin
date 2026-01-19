---
name: arktype
description: Runtime TypeScript validation library with 1:1 type syntax. Use when creating validators, parsing data, or working with ArkType schema definitions using type().
---

# ArkType

Runtime validation with TypeScript-like syntax. 100x faster than Zod.

## Installation

```bash
npm install arktype
```

Requires TypeScript >=5.1, `"type": "module"` in package.json, and `strict` or `strictNullChecks` in tsconfig.

## Core Syntax

```ts
import { type } from "arktype";

const User = type({
  name: "string",
  email: "string.email",
  "age?": "number >= 0", // optional with constraint
  role: "'admin' | 'user'", // literal union
  tags: "string[]", // array
});

type User = typeof User.infer; // extract TS type

// Validation
const result = User(inputData);
if (result instanceof type.errors) {
  console.error(result.summary); // human-readable errors
} else {
  console.log(result.name); // narrowed to User
}
```

## String Keywords

**Primitives:** `string`, `number`, `boolean`, `bigint`, `symbol`, `null`, `undefined`, `object`, `unknown`, `never`

**String subtypes:** `string.email`, `string.uuid`, `string.uuid.v4`, `string.url`, `string.json`, `string.json.parse` (morph), `string.numeric`, `string.numeric.parse` (morph), `string.integer`, `string.date`, `string.date.parse` (morph), `string.semver`, `string.ip`, `string.base64`, `string.trim` (morph), `string.lower` (morph), `string.upper` (morph)

**Number subtypes:** `number.integer`, `number.positive`, `number.negative`, `number.safe`, `number.epoch`

**Instanceof:** `Date`, `Error`, `RegExp`, `Map`, `Set`, `Promise`, `Array`, `FormData`, `File`, `Blob`

## Constraints

```ts
// Numbers
"number > 0"; // exclusive min
"number >= 0"; // inclusive min
"0 < number <= 100"; // range
"number % 2"; // divisible by

// Strings (length)
"string > 0"; // non-empty
"1 <= string <= 50"; // length range

// Arrays (length)
"string[] > 0"; // non-empty array

// Dates
"Date > d'2000-01-01'"; // after date literal

// Regex
"/^[a-z]+$/"; // pattern
```

## Objects

```ts
type({
  required: "string",
  "optional?": "number", // optional
  withDefault: "string = 'hi'", // default value
  "[string]": "unknown", // index signature
  "+": "reject", // error on undeclared keys (or "delete" to strip)
});

// Merge objects
const Extended = Base.merge({ extra: "string" });
// or: type({ "...": Base, extra: "string" })
```

## Arrays & Tuples

```ts
"string[]"[("string", "number")][("string", "number?")][ // array // tuple // optional element
  ("string", "...", "number[]")
][("string", "boolean = false")]; // variadic // defaultable
```

## Unions & Intersections

```ts
"string | number"; // union
"string & /^a/"; // intersection
type.string.or(type.number); // fluent
```

## Morphs (Transforms)

```ts
// Built-in
"string.json.parse"; // string → object
"string.numeric.parse"; // "123" → 123

// Custom
const trimmed = type("string").pipe((s) => s.trim());

// Chain with output validation
type("string.json.parse").to({ name: "string" });
```

## Narrow (Custom Validation)

```ts
const even = type("number").narrow(
  (n, ctx) => n % 2 === 0 || ctx.mustBe("even"),
);
```

## Generics

```ts
// Built-in
"Record<string, number>";
"Pick<User, 'name'>";
"Partial<User>";

// Custom
const boxOf = type("<t>", { box: "t" });
const stringBox = boxOf("string");
```

## Scopes (Reusable Aliases)

```ts
import { scope } from "arktype";

const types = scope({
  Id: "string.uuid",
  User: { id: "Id", friends: "User[]" }, // cyclic OK
}).export();

// Shorthand
const types = type.module({
  User: { name: "string" },
  Admin: { "...": "User", isAdmin: "true" },
});
```

## Error Handling

```ts
if (result instanceof type.errors) {
  result.summary; // "name must be a string (was number)"
  result.throw(); // throw as exception

  for (const error of result) {
    error.path; // ["users", 0, "name"]
    error.expected; // "a string"
    error.actual; // "a number"
  }
}
```

## Common Patterns

```ts
// API Response discriminated union
const ApiResponse = type({
  success: "true",
  data: "unknown",
}).or({
  success: "false",
  error: "string",
});

// Form with defaults
const Form = type({
  email: "string.email",
  password: "string >= 8",
  "remember?": "boolean = false",
});
```

## References

For complete keyword tables: `references/keywords.md`
For advanced features (match, configuration, introspection): `references/advanced.md`
