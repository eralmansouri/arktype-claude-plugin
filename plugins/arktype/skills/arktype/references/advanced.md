# ArkType Advanced Features

## Match (Pattern Matching)

Type-safe pattern matching with optimized branching:

```ts
import { match } from "arktype"

const sizeOf = match({
  "string | Array": v => v.length,
  number: v => v,
  bigint: v => v,
  default: "assert"  // throw if no match
})

sizeOf("abc")     // 3
sizeOf([1,2,3])   // 3
sizeOf(42)        // 42
```

### Default Options
- `"assert"` - Accept unknown, throw if no match
- `"never"` - Only accept inferred input types, throw if no match
- `"reject"` - Accept unknown, return ArkErrors if no match
- `(data) => value` - Custom handler for unmatched

### Fluent API

```ts
const sizeOf = match({
  string: v => v.length,
  number: v => v,
})
  .case({ length: "number" }, o => o.length)
  .default(() => 0)
```

### Discriminated Matching

```ts
type Data = { id: 1; value: number } | { id: 2; value: string }

const handler = match
  .in<Data>()           // narrow input type
  .at("id")             // match at property
  .match({
    1: o => o.value * 2,
    2: o => o.value.length,
    default: "assert"
  })
```

## Configuration

### Global Config

```ts
// config.ts - import BEFORE arktype
import { configure } from "arktype/config"

configure({
  jitless: true,                    // disable JIT compilation
  numberAllowsNaN: true,            // allow NaN in number type
  dateAllowsInvalid: true,          // allow Invalid Date
  onUndeclaredKey: "delete",        // default: "ignore"
  exactOptionalPropertyTypes: false, // allow undefined in optional
  clone: false,                     // mutate input (default: deep clone)
})
```

### Scope Config

```ts
const myScope = scope(
  { User: { age: "number < 100" } },
  {
    max: { actual: () => "too big" }  // customize error messages
  }
)
```

### Type Config

```ts
const Password = type("string >= 8").configure({
  actual: () => ""  // hide actual value in errors
})

// Description (appears in errors)
const Email = type("string.email").describe("a valid email")
```

### Error Customization

| Option | Description |
|--------|-------------|
| `description` | Summary: "must be ___" |
| `expected` | Function returning "must be ___" |
| `actual` | Function returning "(was ___)" |
| `problem` | Full problem string |
| `message` | Complete message with path |

```ts
const Even = type("number % 2").configure({
  expected: ctx => `divisible by ${ctx.rule}`,
  actual: data => `${data} (remainder ${data % 2})`
})
```

### Keyword Configuration

```ts
// config.ts
configure({
  keywords: {
    string: "text",  // shorthand description
    "string.email": {
      actual: () => "not a valid email"
    }
  }
})
```

## Scopes In Depth

### Private Aliases

```ts
const shapes = scope({
  "#BaseProps": { area: "number" },  // private (# prefix)
  Circle: { "...": "BaseProps", radius: "number" },
  Square: { "...": "BaseProps", side: "number" },
})

const types = shapes.export()  // BaseProps not exported
```

### Import vs Export

```ts
const utils = scope({ "box<t>": { box: "t" } })

// export() - aliases become public in consuming scope
// import() - aliases become private in consuming scope

const types = type.module({
  ...utils.import(),  // box available internally, not exported
  User: "box<string>"
})
```

### Submodules

```ts
const sub = type.module({ alias: "number" })

const root = scope({
  a: "string",
  b: "sub.alias",      // reference as prefix.name
  sub: sub
})
```

### Rooted Submodules

```ts
const user = type.module({
  root: { name: "string" },     // "root" makes module usable as type
  Admin: { "...": "root", admin: "true" }
})

const types = type.module({
  User: user,
  Group: "User[]",              // uses root
  AdminGroup: "User.Admin[]"    // uses subtype
})
```

### Thunks (Access Scope in Definition)

```ts
const $ = scope({
  Id: "string.uuid",
  User: () => $.type({ id: "Id" }).pipe(transform)  // access scope via $
})
```

## Type Methods

### Validation Variants

```ts
const T = type("string")

T(data)              // returns data or ArkErrors
T.allows(data)       // returns boolean
T.assert(data)       // returns data or throws
T.from(data)         // same as T(data)
```

### Type Utilities

```ts
T.keyof()            // union of keys
T.get("key")         // type at key
T.get("a", "b")      // nested: T["a"]["b"]
T.pick("a", "b")     // pick keys
T.omit("a", "b")     // omit keys
T.partial()          // all optional
T.required()         // all required
T.merge(other)       // merge objects
T.extract(other)     // intersection
T.exclude(other)     // difference
T.array()            // wrap in array
T.pipe(fn)           // add morph
T.to(other)          // pipe to type
T.narrow(fn)         // add predicate
T.brand("name")      // add type brand
T.describe("desc")   // set description
T.configure(opts)    // set metadata
T.onUndeclaredKey(m) // "reject" | "delete" | "ignore"
T.onDeepUndeclaredKey(m)  // recursive
```

### Type Inspection

```ts
T.infer              // TS type
T.t                  // alias for infer
T.in                 // input type
T.out                // output type
T.expression         // string expression
T.description        // description
T.json               // JSON representation
T.toJsonSchema()     // JSON Schema
T.equals(other)      // type equality
T.extends(other)     // subtype check
T.overlaps(other)    // intersection exists
```

## JSON Schema

```ts
const User = type({
  name: "string",
  email: "string.email",
  "age?": "number >= 18"
})

const schema = User.toJsonSchema()
// {
//   $schema: "https://json-schema.org/draft/2020-12/schema",
//   type: "object",
//   properties: { ... },
//   required: ["name", "email"]
// }
```

### Fallback for Incompatible Types

```ts
T.toJsonSchema({
  fallback: {
    default: ctx => ctx.base,           // ignore incompatible
    date: ctx => ({
      ...ctx.base,
      type: "string",
      format: "date-time"
    })
  }
})
```

## Standard Schema

ArkType implements Standard Schema for library interop:

```ts
// Works with tRPC, react-hook-form, hono, etc.
import { type } from "arktype"

// tRPC >= 11
t.procedure.input(type({ name: "string" }))

// react-hook-form
import { arktypeResolver } from "@hookform/resolvers/arktype"
useForm({ resolver: arktypeResolver(MyType) })
```

## Declare (External Type Matching)

Validate that a Type matches a pre-existing TypeScript type:

```ts
type Expected = { a: string; b?: number }

const T = type.declare<Expected>().type({
  a: "string",
  "b?": "number"
})

// With morphs - validate output side
const T = type.declare<Expected, { side: "out" }>().type({
  a: "string.trim",
  "b?": "number"
})
```
