# ArkType Keywords Reference

## String Keywords

| Keyword | Description |
|---------|-------------|
| `string` | Any string |
| `string.alpha` | Only letters |
| `string.alphanumeric` | Letters and digits |
| `string.base64` | Base64 encoded |
| `string.base64.url` | URL-safe base64 |
| `string.capitalize` | **Morph**: capitalize first letter |
| `string.creditCard` | Credit card number |
| `string.date` | Parseable date string |
| `string.date.parse` | **Morph**: parse to Date |
| `string.date.iso` | ISO 8601 date |
| `string.date.iso.parse` | **Morph**: parse ISO to Date |
| `string.date.epoch` | **Morph**: parse to Unix epoch |
| `string.date.epoch.parse` | **Morph**: epoch string to number |
| `string.digits` | Only digits 0-9 |
| `string.email` | Email address |
| `string.integer` | Integer string |
| `string.integer.parse` | **Morph**: to integer |
| `string.ip` | IPv4 or IPv6 |
| `string.ip.v4` | IPv4 only |
| `string.ip.v6` | IPv6 only |
| `string.json` | Valid JSON string |
| `string.json.parse` | **Morph**: parse JSON |
| `string.lower` | **Morph**: lowercase |
| `string.normalize` | **Morph**: Unicode NFC |
| `string.normalize.NFC/NFD/NFKC/NFKD` | Specific Unicode form |
| `string.numeric` | Numeric string |
| `string.numeric.parse` | **Morph**: to number |
| `string.semver` | Semantic version |
| `string.trim` | **Morph**: trim whitespace |
| `string.upper` | **Morph**: uppercase |
| `string.url` | Valid URL |
| `string.url.parse` | **Morph**: to URL object |
| `string.uuid` | Any UUID |
| `string.uuid.v4/v6/v7` | Specific UUID version |

## Number Keywords

| Keyword | Description |
|---------|-------------|
| `number` | Any number (excludes NaN by default) |
| `number.integer` | Whole number |
| `number.positive` | Greater than 0 |
| `number.negative` | Less than 0 |
| `number.nonNegative` | >= 0 |
| `number.nonPositive` | <= 0 |
| `number.safe` | Within JS safe integer range |
| `number.epoch` | Unix timestamp (ms since 1970) |
| `number.NaN` | Only NaN |

## Instanceof Keywords

| Keyword | Description |
|---------|-------------|
| `Date` | Date instance |
| `Date.parse` | **Morph**: coerce to Date |
| `Error` | Error instance |
| `RegExp` | RegExp instance |
| `Array` | Array instance |
| `Map` | Map instance |
| `Set` | Set instance |
| `WeakMap` | WeakMap instance |
| `WeakSet` | WeakSet instance |
| `Promise` | Promise instance |
| `ArrayBuffer` | ArrayBuffer instance |
| `Blob` | Blob instance |
| `File` | File instance |
| `FormData` | FormData instance |
| `Headers` | Headers instance |
| `Request` | Request instance |
| `Response` | Response instance |
| `URL` | URL instance |
| `TypedArray` | Any typed array |
| `Int8Array` | Int8Array instance |
| `Uint8Array` | Uint8Array instance |
| `Int16Array` | Int16Array instance |
| `Uint16Array` | Uint16Array instance |
| `Int32Array` | Int32Array instance |
| `Uint32Array` | Uint32Array instance |
| `Float32Array` | Float32Array instance |
| `Float64Array` | Float64Array instance |
| `BigInt64Array` | BigInt64Array instance |
| `BigUint64Array` | BigUint64Array instance |

## Built-in Generics

| Generic | Description |
|---------|-------------|
| `Array<t>` | Array of t |
| `Record<k, v>` | Object with keys k and values v |
| `Pick<o, k>` | Pick keys k from object o |
| `Omit<o, k>` | Omit keys k from object o |
| `Partial<o>` | Make all keys optional |
| `Required<o>` | Make all keys required |
| `Extract<t, u>` | Extract types from t assignable to u |
| `Exclude<t, u>` | Exclude types from t assignable to u |
| `Merge<base, merged>` | Merge objects (merged overwrites) |

## Fluent Methods

### Constraint Methods

```ts
// String length
type.string.atLeastLength(n)
type.string.atMostLength(n)
type.string.moreThanLength(n)
type.string.lessThanLength(n)

// Number bounds
type.number.atLeast(n)
type.number.atMost(n)
type.number.moreThan(n)
type.number.lessThan(n)
type.number.divisibleBy(n)

// Array length
type.string.array().atLeastLength(n)
type.string.array().atMostLength(n)

// Date bounds
type.Date.atOrAfter(d)
type.Date.atOrBefore(d)
type.Date.laterThan(d)
type.Date.earlierThan(d)
```

### Composition Methods

```ts
.or(other)              // union
.and(other)             // intersection
.array()                // wrap in array
.optional()             // make optional (in objects/tuples)
.default(value)         // add default (in objects/tuples)
.pipe(fn)               // transform output
.pipe.try(fn)           // transform with error catching
.to(type)               // pipe to another type
.narrow(fn)             // custom validation
.merge(object)          // merge objects
.keyof()                // extract keys as union
.get("key")             // extract property type
.pick("a", "b")         // pick properties
.omit("a", "b")         // omit properties
.partial()              // all keys optional
.required()             // all keys required
```

### Introspection

```ts
.infer                  // TypeScript type (use with typeof)
.t                      // Same as .infer
.in                     // Input type (before morphs)
.out                    // Output type (after morphs)
.json                   // JSON representation
.expression             // String expression
.description            // Human description
.toJsonSchema()         // Generate JSON Schema
```
