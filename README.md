# O-R Lattice Collatz Visualizer

Interactive web-based visualization tool for exploring Collatz sequences on the O-R lattice coordinate system.

## Recent Updates

### Simplified Block Parameterization (January 2026)

The block parameter system has been simplified to focus exclusively on lattice-wide affine relationships:

**Current Parameters:**
- **4 block parameters**: (α, ν, ρ, κ) plus scaling parameter t
- **Fundamental identity**: x + 1 = 2^ν · 2^α · (ρ + t·2^(κ-α))
- **Affine functions**:
  - x(B,t) = 2^ν·(2^α·(ρ + t·2^(κ-α)) - 1)
  - succ_x(B,t) = (3^α·ρ - 1)/2^(κ-α) + 3^α·t

**Why this matters:**
Previous approaches (6 parameters, then 5) attempted to capture both internal block dynamics (3-adic structure) and lattice-wide relationships simultaneously, causing instability. The current 4-parameter approach eliminates 3-adic parameters entirely, focusing only on affine structure. This provides a clean, minimal representation without internal dynamics complications.

**Interactive Anchor Block Panel:**
- **Parameter display**: x, o, r, e, n=3o-r, ν, α, κ, ρ, t, β
- **Interactive spinners**:
  - κ spinner constrained to [α, α+β]
  - t spinner (t ≥ 0) with automatic x recalculation via affine function
- **Clickable successor**: Integer succ_x values link to navigate sequences
- **Natural block detection**: Shows whether κ = α + β

**Lattice Parameters Panel:**
- Unique analytical properties: x_fd, λₓ, λₖ, d, k, θ analysis
- Separated from anchor block to eliminate duplicate information

See `CHANGES.md` for detailed technical documentation and `papers/affine-block-structures.pdf` for mathematical details.

## Overview

This single-page application visualizes Collatz sequences using a novel (o,r) coordinate system where:
- **o** = remaining odd steps to reach 1
- **r** = 2o - e (where e = remaining even steps)

The O-R lattice provides a natural framework for analyzing Collatz dynamics, revealing geometric patterns and relationships that are less apparent in traditional sequence representations.

## Live Demo

Try the visualizer: [https://wildducktheories.github.io/o-r-lattice-explorer/?x=27](https://wildducktheories.github.io/o-r-lattice-explorer/?x=27)

## Features

### Multi-Layer Rendering System

The visualizer uses a flexible layer-based architecture where each layer can be independently toggled:

- **Grid Layer**: Background lattice grid
- **Axes Layer**: O-axis (horizontal) and R-axis (vertical) with labels
- **Path Layer**: Sequence trajectory with semi-transparent gold points
- **λₓ Layer** (enabled by default): Plots -log₂(x) on left axis (green circles at (o, -log₂(x)), size = β + 3)
  - Shows vertical line from lattice point (o,r) to (o, -log₂(x))
  - Shows horizontal line from theta slope intersection to x point
  - Works for both odd and even x when hovering
- **ε Layer**: Plots error term ε = (cₒ,ᵣ - cₓ)/√(1+θ²) on right y-axis (magenta circles at (o, ε), radius = 4)
  - Visualizes the perpendicular distance from the (o,r)-estimator to the actual lattice point
  - Only displays for odd x values

### Theta-Line Reference System

Three parallel reference lines with slope θ = 2 - log₂(3) ≈ 0.4150:

1. **Orange dashed line**: Passes through current lattice point (o,r) - x-estimator
2. **Red-orange dashed line**: Passes through (0, -log₂(x)) - (o,r)-estimator
3. **Gray dashed line**: Passes through origin - baseline reference

These lines provide bidirectional estimation:
- Line 2 (o,r)-estimator: Given x, estimate where lattice points should fall
- Line 1 x-estimator: Given (o,r), estimate x from where the line intersects the r-axis
- The deviation between estimators is quantified by the error term ε

### Interactive Tooltips

Hover over any lattice point to see detailed information organized into two sections:

**Anchor Block (interactive on hover):**
- Lattice position: x, o, r, e, n=3o-r
- Block parameters: ν, α, κ, ρ, t
- Block classification: β, natural (κ = α + β)
- Affine functions: x(B,t), succ_x(B,t) with slopes and intercepts

**Lattice Parameters:**
- First descent: x_fd with congruence navigation
- Lambda values: λₓ = log₂(x), λₖ = log₂(k)
- Derived parameters: d = 2^(2o-r) - 3^o, k = 2^e - 3^o·x
- Theta-line analysis:
  - θ = 2 - log₂(3)
  - cₒ,ᵣ = θ·o - r: intercept of x-estimator
  - cₓ = log₂(x): intercept of (o,r)-estimator
  - L = cₒ,ᵣ/√(1+θ²): normal distance from origin
  - ε = (cₒ,ᵣ - cₓ)/√(1+θ²): error between estimators

### Anchor Block Panel

The anchor block panel provides interactive exploration of block parameter space:

**Interactive Controls:**
- **κ spinner**: Adjust block length within range [α, α+β]
  - Changes the block's modular structure
  - Updates all derived parameters in real-time
- **t spinner**: Navigate along affine families (t ≥ 0)
  - Automatically calculates new x using affine function: x(B,t) = 2^ν·(2^α·(ρ + t·2^(κ-α)) - 1)
  - Loads the new sequence immediately
- **succ_x links**: Click integer successor values to navigate to next block
  - Jumps to the successor's natural block (κ = α + β)

**Display Sections:**
1. **Lattice Position**: x, o, r, e, n=3o-r
2. **Block Parameters**: ν, α, κ, ρ, t
3. **Block Classification**: β, natural (true/false)
4. **Affine Functions**: x(B,t), succ_x(B,t) with slopes

**URL Parameters:**
- `?x=27` - Loads sequence with natural block (κ = α + β)
- `?x=27&anchor_k=3` - Loads sequence with specific κ value
- Browser history preserves both parameters for back/forward navigation

### Congruence Navigation

**First Descent Congruences** in the Lattice Parameters panel:
- Displayed below x_fd value with navigation links
- **k** = 2(o_x - o_fd) - (r_x - r_fd)
- Represents even steps in the path between x and its first descent term
- Click links (← prev | current | next →) to explore congruent values

**Why This Matters:**
- Values in congruence families share structural properties
- Affine translations (via t spinner) explore systematic variations
- Block length adjustments (via κ spinner) reveal how parity patterns affect dynamics

### Lattice Parameters Panel

Displays unique analytical properties not shown in the anchor block panel:
- **First descent term** (x_fd) with congruence navigation
- **Lambda values**: λₓ = log₂(x), λₖ = log₂(k)
- **Derived parameters**: d = 2^(2o-r) - 3^o, k = 2^e - 3^o·x
- **Theta-line analysis**: θ, cₒ,ᵣ, cₓ, L, ε
- Appears immediately below the O-R lattice plot

## Mathematical Background

### The O-R Coordinate System

For a value x in a Collatz sequence:
- **o**: Number of odd steps remaining to reach 1
- **e**: Number of even steps remaining to reach 1
- **r**: Defined as r = 2o - e

This coordinate system transforms the chaotic-appearing Collatz sequence into structured trajectories on the (o,r) lattice.

### Block Parameters

The affine block structure uses:
- **ν (nu)** = v₂(x): Trailing powers of 2
- **α (alpha)** = v₂(x/2^ν + 1): 2-adic valuation of odd core plus one
- **ρ (rho)**: Odd residue parameter, ρ = (x/2^ν+1)/2^α mod 2^(κ-α)
- **κ (kappa)**: Block length (number of even steps in block)
- **t**: Scaling parameter enumerating block instances
- **β (beta)** = v₂(3^α·(x+1)/2^α - 1): Natural block indicator (κ_natural = α + β)

### Theta-Line Relationship

The slope θ = 2 - log₂(3) ≈ 0.4150 emerges from the fundamental Collatz operations:
- Odd step: x → 3x+1 (multiply by 3, add 1)
- Even steps: x → x/2^β (divide by power of 2)

The lines θ·o - r = c provide:
- **cₒ,ᵣ**: Intercept of x-estimator (where the actual lattice point lies)
- **cₓ**: Intercept of (o,r)-estimator (where x estimates the point should lie)
- **L**: Signed distance from origin
- **ε**: Error due to k ≠ 0

Since k = 2^e - 3^o·x is non-zero (except at convergence), there's always an error term causing the actual trajectory to deviate from the ideal θ-slope estimation.

### Pythagorean Identity for ε

The λₓ layer visualizes a right triangle with vertices:
- **(0, -log₂(x))**: Where (o,r)-estimator crosses the r-axis
- **(o, -log₂(x))**: Right angle vertex (green circle location)
- **(o, r)**: Actual lattice point

The triangle has legs:
- **Horizontal**: h = o
- **Vertical**: v = r + log₂(x)

This triangle satisfies a Pythagorean-like identity relating the error term ε:

```
(o + θ·v)² + (cₒ,ᵣ - cₓ)² = (1 + θ²)(o² + v²)
```

Equivalently, in normalized form:

```
o² + v² = [parallel projection]² + ε²
```

where:
- **Parallel projection** = (o + θ·v)/√(1+θ²) along the θ-slope direction
- **ε** = (cₒ,ᵣ - cₓ)/√(1+θ²) perpendicular to the θ-slope
- **cₒ,ᵣ - cₓ** = θ·o - (r + log₂(x)) = unnormalized perpendicular distance

**Geometric interpretation**: The hypotenuse from the (o,r)-estimator intercept to the actual lattice point can be decomposed into a component parallel to the θ-slope and a perpendicular error component ε. When ε = 0, the hypotenuse lies exactly along the θ-slope, meaning perfect alignment between estimators.

## Usage

1. **Load a sequence**: Enter a starting value x₀ and click "Plot Sequence"
2. **Toggle layers**: Use checkboxes to show/hide different visualizations
3. **Explore points**: Hover over lattice points to see detailed analysis
4. **Navigate sequences**:
   - Double-click any lattice point to load its sequence
   - Click congruence links (← prev | current | next →) to explore structurally similar values
   - Browser history is updated, allowing back/forward navigation between sequences
5. **Animate transformation**: Click "Animate to λₓ" to visualize the geometric relationship
   - Original gold points remain at (o, r) positions
   - Animated copy morphs from gold to green as it moves to (o, -log₂(x))
   - Shows both representations simultaneously during animation
   - Button only visible when λₓ layer is enabled
   - Click again to animate back (green fades to gold, points return to (o, r))
   - 1.5 second smooth animation with easing
6. **View examples**: Click example buttons to load interesting sequences (27, 31, 63, 127, 70055, 77031)

### Keyboard Controls

- **Show even terms**: Toggle to display all even x values (default: only shows odd values and critical even points)

## Technical Details

### Single-File Application

The entire application is contained in `index.html` with no external dependencies:
- Vanilla JavaScript (no frameworks)
- HTML5 Canvas for rendering
- No build process required

### Layer Architecture

Each layer extends a base `Layer` class with:
- `render(ctx, sequences, latticeTransform, rightAxisTransform)`: Rendering logic
- `enabled`: Toggle visibility
- `usesRightAxis`: Whether layer uses right y-axis scaling

Right-axis layers automatically coordinate their bounds and share the same axis.

### Coordinate Transformations

The renderer maintains transforms between:
- **Lattice coordinates** (o, r): Mathematical space
- **Screen coordinates** (x, y): Canvas pixels
- **Right-axis values** (λₘ, λₓ): Additional data dimensions

## Browser Compatibility

Works in all modern browsers with HTML5 Canvas support:
- Chrome/Edge (recommended)
- Firefox
- Safari

## Development

See `CLAUDE.md` for detailed development notes, coordinate system conventions, and implementation lessons learned.

## References

Based on research exploring Collatz-type sequences through lattice-based coordinate systems.

## Screenshot

<!-- Screenshot will be added here -->

## License

[To be determined]
