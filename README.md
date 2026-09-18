# Group-33-assignment
# Group 33 — Comparisons

## Task Requirements
Re-implement a set of dataframe-style comparison operations in C++, inspired by
functions available in libraries like Polars/Pandas. The operations covered are:

- `between()` — check if values fall within an inclusive range
- `within()` — check if values are within a tolerance of a center value
- `approximately_equal()` — compare two numbers for near-equality
- `clamp()` — restrict a single value to a given range
- `clip()` — apply clamping across an entire dataset

## Implementation Strategy
- A "column" of data is represented as a `vector<double>`, matching how dataframe
  libraries typically store column data internally.
- Each operation is a standalone function that takes the data (and any needed
  parameters) and returns a new result — no input data is modified in place.
- `clip()` is built on top of `clamp()`, reusing the same range-restriction logic
  instead of duplicating it.
- Boolean-returning functions (`between()`, `within()`) return a `vector<bool>`
  with one result per input value, printed using `boolalpha` for readability.
- For `approximately_equal()`, output precision is temporarily raised with
  `fixed`/`setprecision(7)` before printing, then reset — otherwise `cout`'s
  default 6-significant-digit precision rounds `10.0000001` down to `10`,
  hiding the actual difference being compared.

## Key Decisions
- Inclusive bounds are used for `between()` and `clamp()`/`clip()` (a value
  exactly equal to a bound counts as within range).
- `approximately_equal()` uses a default tolerance of `0.000001` unless the
  caller specifies otherwise, since exact `==` comparisons are unreliable with
  floating-point numbers.
- Output is grouped by operation number (1–5) in `main()` for a clear, readable
  demonstration of each function.

## Testing
Each function was tested using a sample dataset in `main()`:

```cpp
vector<double> data = {5, 10, 15, 20, 25};
```

Testing approach:
- Verified `between(10, 20)` correctly flags 10, 15, 20 as true and 5, 25 as false.
- Verified `within(20, 5)` correctly flags values within 5 units of 20.
- Verified `approximately_equal()` returns true for two numbers differing only
  by a tiny floating-point amount (10.0 vs 10.0000001), and confirmed the
  printed values actually show that difference after fixing output precision.
- Verified `clamp()` correctly caps 5 → 10, 25 → 20, and leaves 15 unchanged.
- Verified `clip()` applies the same clamping across the full dataset.

## Working Example

**Input:**
```cpp
vector<double> data = {5, 10, 15, 20, 25};
```

**Output:**
```
GROUP 33 - COMPARISONS
========================

1. BETWEEN
Values between 10 and 20:
false true true true false

2. WITHIN
Values within +/-5 of 20:
false false true true true

3. APPROXIMATELY EQUAL
10.0000000 and 10.0000001: true

4. CLAMP
Clamp 5  between 10 and 20: 10
Clamp 15 between 10 and 20: 15
Clamp 25 between 10 and 20: 20

5. CLIP
Original values:
5 10 15 20 25

Clipped values (10 to 20):
10 10 15 20 20
```
