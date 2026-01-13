# O-R Lattice Collatz Visualizer

Interactive web-based visualization tool for exploring Collatz sequences on the O-R lattice coordinate system.

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
- **OE Blocks Layer**: Highlights ((OE)+E+) block structures
- **λₘ Layer**: Plots log₃(m) on right y-axis (red circles, size = γ + 3)
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

Hover over any lattice point to see detailed information:

**Lattice Coordinates:**
- o, r, e values

**Element Properties:**
- x value with factorization: x = 2^α·3^γ·m - 1
- λₓ = log₂(x)

**Derived Parameters:**
- α = v₂(x+1): 2-adic valuation determining upward growth
- γ = v₃(x+1): 3-adic valuation
- m = (x+1)/(2^α·3^γ): reduced multiplier
- λₘ = log₃(m)
- β = v₂(x) or v₂(3x+1): determines downward decay
- d = 2^(2o-r) - 3^o
- k = 2^e - 3^o·x
- λₖ = log₂(k)

**Theta-Line Analysis:**
- θ = 2 - log₂(3)
- cₒ,ᵣ = θ·o - r: intercept of x-estimator (through lattice point)
- cₓ = log₂(x): intercept of (o,r)-estimator
- L = cₒ,ᵣ/√(1+θ²): normal distance from origin line
- ε = (cₒ,ᵣ - cₓ)/√(1+θ²): error between estimators

### Congruence Navigation

The visualizer supports navigating along **congruence families** - sets of values that share identical Collatz sequence structure up to the first k even terms.

#### What are Congruences?

For a given value x and parameter k, the congruent values are:
- **x - 2^k** (previous congruent value)
- **x** (current value)
- **x + 2^k** (next congruent value)

These values have identical sequence patterns for the first k even steps, making them structurally related in the Collatz dynamics.

#### Two Types of Congruences

**1. OE Block Congruences**

Displayed in the OE Blocks panel for each maximal ((OE)+E+) block:
- **k** = number of E's (even terms) in the block pattern
- Example: For pattern "OEOEE", k=3, so congruent values are x±8

Format:
```
x=27: OEOEE
  k=3: ← 19 | 27 | 35 →
```

**2. First Descent (x_fd) Congruences**

Displayed in the X₀ info panel below the x_fd value:
- **k** = 2(o_x - o_fd) - (r_x - r_fd)
- Represents the number of even steps in the path between x and its first descent term
- First descent term: point with x < x_search, L < L_search, o < o_search (maximizing o)

#### Anchor Navigation

When you click a congruence link (e.g., k=3: ← 19), the URL includes `?x=19&anchor_k=3` and the new page displays:

```
anchor: OEOEE
  k=3: ← 11 | 19 | 27 →

x=19: OEOEEE
  k=4: ← 3 | 19 | 35 →
```

The **anchor** section shows:
- The OE prefix of the current sequence up to k E's
- The congruence navigation path you followed to arrive here
- Allows you to continue navigating along the same k-congruence thread

The maximal OE blocks follow below with their own congruence values.

#### How to Use

1. **Navigate blocks**: Click any congruence link to jump to a structurally similar value
2. **Follow threads**: The anchor_k parameter persists, letting you explore an entire congruence family
3. **Browser history**: Back/forward buttons work correctly, preserving anchor_k context
4. **Reset anchor**: Click "Plot Sequence" or an example button to start fresh exploration

#### Why This Matters

Congruence navigation reveals:
- **Structural invariants**: Patterns that persist across value transformations
- **Periodic behavior**: How sequences evolve as you navigate x → x±2^k
- **Family relationships**: Values that share common Collatz sub-trajectories

### Starting Value Info Panel

Displays comprehensive properties for the sequence's starting value x₀:
- All lattice coordinates, valuations, and derived parameters
- Lambda values (λₓ, λₘ, λₖ)
- Complete theta-line analysis (θ, cₒ,ᵣ, cₓ, L, ε)
- Appears immediately below the O-R lattice plot

## Mathematical Background

### The O-R Coordinate System

For a value x in a Collatz sequence:
- **o**: Number of odd steps remaining to reach 1
- **e**: Number of even steps remaining to reach 1
- **r**: Defined as r = 2o - e

This coordinate system transforms the chaotic-appearing Collatz sequence into structured trajectories on the (o,r) lattice.

### Greek Letter Variables

To avoid notation conflicts, we use:
- **α (alpha)** = v₂(x+1): Controls upward growth
- **γ (gamma)** = v₃(x+1): Neutral to growth/decay
- **β (beta)** = v₂(x) or v₂(3x+1): Controls downward decay

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
