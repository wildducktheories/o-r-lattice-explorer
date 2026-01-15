# Changes Since c093445

## Summary

Two significant features have been added since commit c093445:

1. **Swipe-to-Select Anchor Region** - Interactive selection on the lattice canvas
2. **New Canonical Representation of x** - Fundamental change to how sequence elements are parameterized

---

## 1. Swipe-to-Select Anchor Region

A new interactive selection feature allows users to draw a rectangular region on the lattice canvas by clicking and dragging.

### How It Works

- **Click and drag** anywhere on the lattice to draw a selection box
- On release, the system:
  - Identifies the **rightmost odd term** in the selection → becomes the new x₀
  - Counts **even steps between first and last odd** → sets anchor_k
  - Immediately plots the new sequence with these values
  - Displays the anchor region with visual boundaries

### Selection Logic

The selection identifies complete (OE)+E+ blocks:
- The rightmost point defines the start of the anchor block
- The leftmost point determines the first complete block boundary
- Anchor boundaries are offset by ⅓ grid square for visual clarity

### Use Case

This enables rapid exploration of the Collatz sequence by visually selecting regions of interest and immediately navigating to related sequences.

---

## 2. New Canonical Representation of x

**This is a fundamental change to the mathematical representation.**

### Previous Representation

```
x = 2^α · 3^γ · m - 1
```

Where m was an opaque intermediate value with λₘ = log₃(m).

### New Representation

For **odd x**:
```
x = 2^α · (3^γ·ρ + t·2^β) - 1
```

For **even x**:
```
x = 2^ν · (2^α · (3^γ·ρ + t·2^β) - 1)
```

### Parameter Definitions (in computation order)

| Parameter | Definition | Description |
|-----------|------------|-------------|
| **α** | v₂(x+1) | Power of 2 dividing (x+1) |
| **m** | (x+1) / 2^α | Intermediate value after extracting 2-power |
| **β** | v₂(3^α · m - 1) | Computed from α and m |
| **γ** | v₃(m mod 2^β) | 3-adic valuation of remainder |
| **ν** | v₂(x) | Power of 2 dividing x (0 for odd x) |
| **ρ** | (m mod 2^β) / 3^γ | Remainder with 3-power factored out |
| **t** | ⌊m / 2^β⌋ | Quotient in the decomposition |

### Identity

```
m = 3^γ·ρ + t·2^β
```

### Why This Matters

The decomposition `m = 3^γ·ρ + t·2^β` makes explicit the structure that was hidden in m:

- **ρ** captures the "offset" within a congruence class (after factoring out 3^γ)
- **t** indexes translations within the equivalence class
- **ν** properly handles even values by factoring out powers of 2 first
- **γ** emerges from β, not directly from x+1

For even x, parameters are computed from the odd part x/2^ν, ensuring consistent parameterization across the entire sequence.

### Basic Blocks vs Natural Blocks

A **basic block** is a parity sequence with exactly κ evens. It is characterized by:
- Parameters: (α, γ, ρ, t, κ) where κ is a free parameter
- Modulus: 2^(κ-α)

A **natural block** is the specific block containing x where:
- κ = α + β
- Modulus: 2^β (since β = κ - α)

The natural block is the block that x naturally belongs to based on its own parameters. Basic blocks generalize this by allowing κ to vary independently.

### Significance for Block Structure

**Block translations on the O-R lattice are expressible as affine transforms derived from these parameters.**

Each increment of the free parameter **t** represents a translation of x to another x on the O-R lattice that shares the same initial parity sequence. In other words, values related by t have identical (OE)+ block prefixes.

This means:
- **t parameterizes equivalence classes** of lattice points with shared parity structure
- **Linear combinations of blocks** can be derived from linear combinations of their affine structures
- The (α, γ, ρ, t, κ) tuple provides the natural coordinates for analyzing block dynamics

### Removed

- The **M Values layer** (λₘ-layer) has been removed entirely
- The λₘ = log₃(m) calculation is no longer computed or displayed

---

## Commits

| Hash | Description |
|------|-------------|
| 41c1bcd | Add swipe-to-select for anchor region on lattice |
| 713942f | Add ρ, t, ν parameters and remove M Values layer |
| f621fa7 | Revise m, β, γ, ρ, t parameter formulas |

---

## Files Changed

- `index.html` - 372 insertions, 100 deletions
