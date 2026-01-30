# Changes Since c093445

## Summary

Ten major revisions have been made since commit c093445:

1. **Swipe-to-Select Anchor Region** - Interactive selection on the lattice canvas
2. **New Canonical Representation of x** - Fundamental change to how sequence elements are parameterized (5 parameters with γ)
3. **Simplified 4-Parameter Framework** - Final simplification eliminating 3-adic parameters entirely
4. **Modulus Correction** - Updated modulus from 2^β to 2^{β+1}
5. **Selected Block with Visual Highlighting** - Block selection independent of x₀, with lattice highlighting
6. **Remove Heatmap** - Removed (α, β) distribution heatmap
7. **Affine Equation Display** - Numeric slope/intercept and x equation values
8. **BigInt and Exact Arithmetic** - Rational class and full BigInt conversion
9. **Multi-Block Selection with Composite Blocks** - Select ranges of natural blocks, display composite parameters
10. **Trajectory Decomposition Paper** - New paper on block decomposition of Collatz trajectories

---

## 10. Trajectory Decomposition Paper (January 2026)

**Added a new paper: "Block Decomposition of Collatz Trajectories".**

The paper introduces composite blocks with parameters (α, β, ρ, φ, t) formed by composing adjacent natural blocks. It shows that the Collatz conjecture is equivalent to every odd x > 1 having a block decomposition with x→ = 1.

### Key contributions

- Extends the 3-parameter natural block framework from the affine-blocks paper with a 5-parameter composite block form
- Defines block composition formulas for α, β, ρ, φ, t
- Proves equivalence between block decomposition completeness and the Collatz conjecture
- Provides a worked example for x = 7

### Files

- `papers/trajectory-decomposition.tex` — LaTeX source
- `Taskfile.yml` — Added `pdf:trajectory-decomposition` build task

---

## 9. Multi-Block Selection with Composite Blocks (January 2026)

**Swipe across multiple odd terms to select a range of consecutive natural blocks and display composite block parameters.**

### Key Changes

- **Range selection**: Swiping across multiple odd terms selects consecutive natural blocks between leftmost and rightmost odd values
- **Composite block display**: Shows composite (α, β, ρ, φ, t) for the composed range
- **Affine equations**: Displays symbolic and numeric forms for both x and x→ equations
- **t-spinner**: Increments t on the composite block, preserving block structure across sequence changes
- **URL persistence**: Selection state preserved via `succ` URL parameter
- **Block composition**: `composeBlocksInRange()` follows the `decomposeBlock` chain to compose natural blocks correctly

### Display

- Symbolic equation: x = 2^α(ρ + t·2^(β+1)) - φ - 1
- Numeric slope-intercept: x = mt + c
- Equations shown in Selected Block, Composite Block, Lattice Parameters, and tooltip

---

## 8. BigInt and Exact Arithmetic (January 2026)

**Converted the entire Collatz pipeline to BigInt with exact Rational arithmetic.**

### Key Changes

- **Rational class**: Exact rational arithmetic (add, multiply, compare, toString) for composite block parameters where ρ and φ are rational with 3-power denominators
- **BigInt throughout**: All `step.x` values, block parameters, URL parsing, history state, congruence navigation use BigInt
- **No floating-point arithmetic** in the Collatz pipeline — all computations are exact

---

## 7. Affine Equation Display (January 2026)

**Added numeric slope and intercept values to affine equation display.**

### Key Changes

- Show numeric slope and intercept alongside symbolic forms in selected block panel
- Add numeric x equation value display
- Equations appear in selected block, composite block, and lattice parameters sections

---

## 6. Remove Heatmap (January 2026)

**Removed the (α, β) distribution heatmap visualization.**

The heatmap showing the distribution of (α, β) pairs across the sequence has been removed to simplify the interface. The core O-R lattice visualization remains the primary view.

### Removed

- Heatmap canvas and container
- HeatmapRenderer class
- All heatmap rendering logic

---

## 5. Selected Block with Visual Highlighting (January 2026)

**Renamed "Anchor Block" to "Selected Block" and made block selection independent of x₀.**

### Key Changes

**Block Selection Behavior:**
- Swipe-to-select now sets `selectedX` (the displayed block) WITHOUT changing x₀
- The selected block is highlighted on the lattice with a blue band
- Margins are ±⅓ grid square from the block boundaries
- Block extends from selected odd (high o) to lower o by α steps

**Successor Navigation:**
- Clicking x→ changes the selected block, NOT x₀
- This allows exploring the block chain without losing the current sequence

**Simplified Parameters:**
- Removed κ spinner (now only natural blocks where κ = α + β)
- t spinner still changes x₀ via affine function

**UI Changes:**
- Renamed all "anchor" references to "selected block"
- Removed "Viewing block for x = ..." message
- Block pattern (OE string) shown at top of panel

### Files Changed

- `index.html` - Renamed UI elements, added block highlighting, fixed selection logic

---

## 4. Modulus Correction (January 2026)

**Commit c00b350**: Corrected the modulus in affine block formulas from 2^(κ-α) to 2^(κ-α+1).

### Changes

**Fundamental identity** (corrected):
```
x = 2^ν · (2^α · (ρ + t·2^(κ-α+1)) - 1)
```

**Parameter computation** (corrected):
- ρ = m_raw mod 2^(κ-α+1)
- t = ⌊(m_raw - ρ)/2^(κ-α+1)⌋

**Affine function slopes** (corrected):
- x-function slope: 2^(ν+κ+1) (was 2^(ν+κ))
- Successor slope: 2·3^α (was 3^α)

### Why This Matters

The modulus 2^(κ-α+1) = 2^(β+1) ensures that:
1. All block instances (indexed by t) have the same β value
2. The affine family correctly enumerates values with identical parity patterns
3. The slope formulas properly account for the doubling in the modulus

### Files Changed

- `papers/affine-block-structures.tex` - Updated all formulas
- `index.html` - Updated computations and display

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
x = 2^ν · (2^α · (ρ + t·2^(κ-α+1)) - 1)
```

**Parameter computation**:
1. ν = v₂(x)
2. α = v₂(x/2^ν + 1)
3. m_raw = (x/2^ν + 1)/2^α
4. β = v₂(3^α·m_raw - 1)
5. κ = α + β (natural block) or κ ∈ [α, α+β] (basic block)
6. ρ = m_raw mod 2^(κ-α+1) (must be odd)
7. t = ⌊(m_raw - ρ)/2^(κ-α+1)⌋

**Affine functions**:
```
x(B,t) = 2^ν·(2^α·(ρ + t·2^(κ-α+1)) - 1)
         slope: m_x = 2^(ν+κ+1)
         intercept: c_x = 2^ν·(2^α·ρ - 1)

succ_x(B,t) = (3^α·ρ - 1)/2^(κ-α) + 2·3^α·t
              slope: m_succ = 2·3^α
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
- Modulus: 2^(κ-α+1)

A **natural block** is the specific block containing x where:
- κ = α + β
- Modulus: 2^(β+1) (since β+1 = κ - α + 1)

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
| a7c921c | Add .ipynb_checkpoints to .gitignore |
| 9527c1e | Add LaTeX build products to .gitignore |
| 2e32b6e | Add trajectory decomposition paper |
| fe0a2bb | Add multi-block selection with composite block composition |
| 6afaf11 | Add block decomposition UI and convert Collatz pipeline to BigInt |
| 9785001 | Add BigInt Rational class and convert arithmetic to exact precision |
| ef298a8 | Add numeric value to x equation display |
| 1371d99 | Show numeric slope and intercept in affine equations |
| c00b350 | Update modulus from 2^β to 2^{β+1} in UI and paper |
| a4ad829 | Refactor anchor block panel with interactive controls |
| ebc554a | Document basic blocks vs natural blocks distinction |
| 9f56103 | Update CHANGES.md with revised parameter formulas |
| f621fa7 | Revise m, β, γ, ρ, t parameter formulas |
| 41c1bcd | Add swipe-to-select for anchor region on lattice |
| 713942f | Add ρ, t, ν parameters and remove M Values layer |

---

## Files Changed

- `index.html` - 372 insertions, 100 deletions
