# Changes Since c093445

## Summary

Three major revisions have been made since commit c093445:

1. **Swipe-to-Select Anchor Region** - Interactive selection on the lattice canvas
2. **New Canonical Representation of x** - Fundamental change to how sequence elements are parameterized (5 parameters with γ)
3. **Simplified 4-Parameter Framework** - Final simplification eliminating 3-adic parameters entirely

---

## 3. Simplified 4-Parameter Framework (January 2026)

**This is a fundamental simplification that eliminates internal 3-adic structure tracking.**

### Previous Approach (WITHDRAWN)

The 5-parameter system (α, ν, γ, ρ, κ) attempted to capture both:
- Lattice-wide affine relationships between blocks
- Internal 3-adic dynamics within blocks (via γ = v₃(m mod 2^β))

This dual purpose caused conceptual complexity and instability.

### Current Approach: 4 Parameters + Scaling

**Block definition**: B = (α, ν, ρ, κ), t ≥ 0

**Fundamental identity**:
```
x + 1 = 2^ν · 2^α · (ρ + t·2^(κ-α))
```

**Parameter computation**:
1. ν = v₂(x)
2. α = v₂(x/2^ν + 1)
3. m_raw = (x/2^ν + 1)/2^α
4. β = v₂(3^α·m_raw - 1)
5. κ = α + β (natural block) or κ ∈ [α, α+β] (basic block)
6. ρ = m_raw mod 2^(κ-α) (must be odd)
7. t = ⌊(m_raw - ρ)/2^(κ-α)⌋

**Affine functions**:
```
x(B,t) = 2^ν·(2^α·(ρ + t·2^(κ-α)) - 1)
         slope: m_x = 2^(ν+κ)
         intercept: c_x = 2^ν·(2^α·ρ - 1)

succ_x(B,t) = (3^α·ρ - 1)/2^(κ-α) + 3^α·t
              slope: m_succ = 3^α
              intercept: c_succ = (3^α·ρ - 1)/2^(κ-α)
```

### Why This Matters

**Separation of concerns**:
- The 4-parameter framework focuses exclusively on **lattice-wide affine relationships**
- Internal 3-adic dynamics (if needed) should be treated in separate analysis
- No conflation of block-to-block relationships with internal block evolution

**Benefits**:
- Clean minimal parameterization
- Stable parameters across block instances
- Direct computational efficiency
- Clear mathematical interpretation

### UI Changes

**Interactive Anchor Block Panel**:
- Removed: δ, γ, m parameters
- Added: n = 3o - r parameter
- Added: Interactive κ spinner (constrained to [α, α+β])
- Added: Interactive t spinner (t ≥ 0) with affine function calculation
- Added: Clickable succ_x links for integer values
- Organization: 5 logical groups (position, parameters, classification, functions, navigation)

**Lattice Parameters Panel** (renamed from "Starting Value"):
- Removed: Duplicate parameters (o, r, e, x, ν, α, β, κ, ρ, t)
- Kept: Unique values (x_fd, λₓ, λₖ, d, k, θ analysis)
- Purpose: Analytical properties not in anchor block

**Tooltip**:
- Two-section structure: "Anchor Block" + "Lattice Parameters"
- Static versions of both panels
- Block pattern prefix at top (if applicable)

**Removed**:
- ((OE)+E+) Blocks section entirely

### Documentation Updates

- `papers/affine-block-structures.pdf`: Documents 4-parameter framework mathematically
- `reddit.txt`: Plain text summary for sharing
- Renamed: succ_B → succ_x throughout all files (represents "successor of x according to B")

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
| 972e681 | Refactor anchor block panel with interactive controls |
| dc41248 | WIP: Add basic block parameter display to anchor section |
| ebc554a | Document basic blocks vs natural blocks distinction |
| 9f56103 | Update CHANGES.md with revised parameter formulas |
| f621fa7 | Revise m, β, γ, ρ, t parameter formulas |
| 41c1bcd | Add swipe-to-select for anchor region on lattice |
| 713942f | Add ρ, t, ν parameters and remove M Values layer |

---

## Files Changed

- `index.html` - 372 insertions, 100 deletions
