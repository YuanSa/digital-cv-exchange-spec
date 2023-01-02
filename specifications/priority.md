# Digital CV Exchange Specification of Priority

## Priority

Each entry in the JSON follows Digital CV Exchange Specification has it's own priority in non-negative integer number.

The priority of each entry is  0 by default.

If the name of the entry starts with an `x` followed by a number and dash (like `x1-`, `x2-`, `x10-`), then the priority of this entry will be promoted by the level of the number followed the prefixed `x`.

`x1-` can be shorted as `x-` for convenience.

The parent entries' priority should be considered over the children entries'. That is to say, take the following codes as example, the `x1-language` will be prioritized than the `x2-language` entry - Because the parent entry of `x1-language`, which is `x-meta` with priority of 1, has a higher priority than the parent entry of `x2-language`, which is `meta` with priority of 0.

```JSON
{
    "meta": {
        "x2-language": "en"
    },
    "x-meta": {
        "x1-language": "zh"
    }
}
```

### Representation

To more conveniently and clearly represent the priority of each entry, we use the way like we describe the priority of a rule in CSS to describe them.

Children's priorities should be prefixed with their parent's priority with a dash `-` to join.

For example, an entry with the priority of 1 has two children with priority of 1 and 2 for each. Then we should represent the parent's priority as `1` and the children's priorities should be `1-1` and `1-2`.

```JSON
{
    "parent": { // 0
        "child-a": "something", // 0-0
        "x-child-b": "something" // 0-1
    },
    "x-parent": { // 1
        "x1-child-a": "something", // 1-1 
        "x2-child-b": "something" // 1-2
    }
}
```

## Merge Rule

When we say "the name of entry", the prefix (like `x-`, `x2-`) doesn't count. If multiple entries' names only difference by the prefix, they should be considered as the same entry name and should be merged when apply.

When merging two entries with the same name, the one with higher priority will overwrite the one with lower priority.

Two entries with both the same name and priority coexist in one object is not allowed and this should be regarded as a bug.