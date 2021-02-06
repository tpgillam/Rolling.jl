# Rolling.jl
[![Dev](https://img.shields.io/badge/docs-dev-blue.svg)](https://tpgillam.github.io/Rolling.jl/dev)
[![Build Status](https://github.com/tpgillam/Rolling.jl/workflows/CI/badge.svg)](https://github.com/tpgillam/Rolling.jl/actions)
[![Code Style: Blue](https://img.shields.io/badge/code%20style-blue-4495d1.svg)](https://github.com/invenia/BlueStyle)

Accumulate operators over rolling windows.

## Features
- Supports any binary operator that is associative (see Assumptions section for an elaboration).
- Supports a potentially variable window size.
- Supports adding values incrementally, e.g. when streaming data.
- Runs in amortized `O(1)` time, and uses `O(L)` space, where `L` is the typical window length.

## Assumptions and stability
We assume that operators are associative.
Strictly speaking this is not true for operators on floating points, however without this assumption one can do no better than `O(L)` time complexity.
Also, for most reasonable applications this should not be a major limitation.
As detailed below, there is no long term accumulation of errors.

Note that we *do not* assume:
- The existence of an inverse
- Commutativity

### Example
Consider computing a rolling sum over `[a, b, c, d, e]` with window size `3`.
Ignoring the first two incomplete elements, we will emit exactly:
```
[
    a + b + c,
    b + c + d,
    c + d + e
]
```

Note that the most common `O(1)` approach to computing rolling sums uses the following approximation:
```
[
    a + b + c,
    a + b + c + d - a,
    a + b + c + d + e - a - b
]
```
This can be problematic due to overflow / underflow for most numerical types, and accumulated rounding errors in floating point types (for an extreme example, suppose `a` is `Inf`).
The simple approach also does not generalise to operators like the set `union`, since there is no inverse.


## Notes
- API is very preliminary and likely to change a lot.
- No attempts to optimise or otherwise benchmark the algorithms has been done (yet)!

## TODO
- Fixed window for simple types would benefit from statically allocated buffers that could be reused.