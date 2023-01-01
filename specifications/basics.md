# Digital CV Exchange Specification of Basics

## Top Level Structure

The top level JSON should be an key-value pairs object and the fields are:

| Field     | Type     | Format              | Mandatory  | Description                                                 |
| --------- | -------- | ------------------- | ---------- | ----------------------------------------------------------- |
| `version` | `string` | Semantic versioning | `required` | The version of the specification the current JSON followed. |
| `meta`    | `object` | `Meta`              | `optional` | The info about the file, not the CV contents.               |
| `main`    | `object` | `Main`              | `optional` | The info about the CV contents.                             |

## Non-standard Fields

In all objects, the key starts with `x1-`, `x2-`, `x3-` indicates this is not a standard field with the priority weight of 1, 2 and 3. The prefix `x1-` can be shortened as `x-`.

The prefixed and not prefixed version of a property can coexist. In this case, it indicates that these two property should be **shadowed merged**. The fields with a higher priority should overwrite the field with a lower priority and if the higher priority one cannot be recognized, then it should downgrade the priority level gradually until successfully recognize the data structure or finally failed and throw an error.

_The rules of priority and downgrade strategies will be described in another standalone specification._

Custom fields are supported, but should be prefixed to avoid potential conflict with future standards.

## Examples

### Top Level Structure

```JSON
{
    "version": "0.1.0",
    "meta": {
        // structure `Meta`
    },
    "main": {
        // structure `Main`
    }
}
```

### Non-standard Fields

The fields like this:

```JSON
{
    "version": "0.1.0", // priority of 0
    "meta": { // priority of 0
        "language": "zh", // priority of 0
        "update-date": "2023-01-01" // priority of 0
    },
    "main": {}, // priority of 0
    "x-meta": { // priority of 1
        "x1-locale": "zh-CN", // priority of 11
        "x2-locale": "zh-Hans-CN", // priority of 12
        "x-update-date": "2023-01-01T00:00:00Z" // priority of 11
    }
}
```

should first be treated as the following, which is in priority level 2, means it has the max priority of 2:

```JSON
{ // priority level 12 = max(items' priorities)
    "version": "0.1.0", // priority of 0
    "meta": { // priority of 1
        "language": "zh", // priority of 10
        "locale": "zh-Hans-CN", // priority of 12
        "update-date": "2023-01-01T00:00:00Z" // priority of 11
    },
    "main": {}, // priority of 0
}
```

and if the current application cannot recognize the `locale` field, it should reduce 1 priority level to try the max priority of 1, which is:

```JSON
{ // priority level 11 = max(items' priorities)
    "version": "0.1.0",
    "meta": { // priority of 1
        "language": "zh", // priority of 10
        "locale": "zh-CN", // priority of 11
        "update-date": "2023-01-01T00:00:00Z" // priority of 11
    },
    "main": {},
}
```

if this is recognizable then it will be used. Otherwise, if the `locale` field or `update-date` field still cannot be recognized, then it should retreat one more priority to 0, which means the official standard without any extends:

```JSON
{ // priority level 0 = max(items' priorities) = official standard
    "version": "0.1.0",
    "meta": { // priority of 0
        "language": "zh", // priority of 0
        "update-date": "2023-01-01" // priority of 0
    },
    "main": {},
}
```

If the JSON in priority of 0 is still unrecognizable, then an error or warning should be thrown out.
