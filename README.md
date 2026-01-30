# O-R Lattice Collatz Visualizer

Interactive web-based visualization tool for exploring Collatz sequences on the O-R lattice coordinate system.

## Recent Updates

### Multi-Block Selection with Composite Blocks (January 2026)

Swipe across multiple odd terms to select a range of consecutive natural blocks. The composite block parameters (α, β, ρ, φ, t) are computed and displayed with full affine equations.

**Block Selection:**
- **Swipe-to-select**: Drag across one or more odd terms to select natural blocks
- **Composite display**: Shows composite block parameters when multiple blocks are selected
- **Affine equations**: Symbolic and numeric forms for x and x→
- **t-spinner**: Preserves block structure when navigating affine families
- **URL persistence**: Selection state preserved via `succ` parameter

**Exact Arithmetic:**
- Full BigInt pipeline — no floating-point in Collatz computations
- Rational class for exact ρ and φ in composite blocks

### Trajectory Decomposition Paper

New paper `papers/trajectory-decomposition.tex` shows that the Collatz conjecture is equivalent to every odd x > 1 having a complete block decomposition. Extends the affine-blocks framework with composite blocks and composition formulas.

See `CHANGES.md` for detailed technical documentation and `papers/` for mathematical details.

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

**Selected Block:**
- x and x→ (successor) with affine formulas
- Block parameters: α, β, ρ, t, κ

**Lattice Parameters:**
- Lattice position: o, r, e, n=3o-r
- First descent: x_fd with congruence navigation
- Lambda values: λₓ = log₂(x), λₖ = log₂(k)
- Theta-line analysis: θ, cₒ,ᵣ, cₓ, L, ε

### Selected Block Panel

The selected block panel displays the Steiner block for the currently selected odd term:

**Block Selection:**
- **Swipe-to-select**: Drag on the lattice to select the rightmost odd inside the swipe region
- **Visual highlighting**: The selected block's region is highlighted on the lattice
- **x→ navigation**: Click the successor link to select the next block (does not change x₀)

**Interactive Controls:**
- **t spinner**: Navigate along affine families (t ≥ 0)
  - Calculates new x₀ using affine function: x = 2^α·(ρ + t·2^(β+1)) - 1
  - Loads the new sequence immediately

**Display:**
- x with affine formula
- x→ (successor) with clickable link
- Block parameters: α, β, ρ, t, κ = α + β

**URL Parameters:**
- `?x=27` - Loads sequence starting at x₀ = 27
- Browser history preserves x for back/forward navigation

### Congruence Navigation

**First Descent Congruences** in the Lattice Parameters panel:
- Displayed below x_fd value with navigation links
- **k** = 2(o_x - o_fd) - (r_x - r_fd)
- Represents even steps in the path between x and its first descent term
- Click links (← prev | current | next →) to explore congruent values

**Why This Matters:**
- Values in congruence families share structural properties
- Affine translations (via t spinner) explore systematic variations
- Successor navigation (via x→) reveals block chain structure

### Lattice Parameters Panel

Displays analytical properties for the hovered point:
- **Lattice position**: o, r, e, n=3o-r
- **First descent term** (x_fd) with congruence navigation
- **Lambda values**: λₓ = log₂(x), λₖ = log₂(k)
- **Derived parameters**: d = 2^(2o-r) - 3^o, k = 2^e - 3^o·x
- **Theta-line analysis**: θ, cₒ,ᵣ, cₓ, L, ε

### Pop-out Windows

Each major section can be opened in a separate window for flexible screen layout management:

**Available Pop-outs:**
- **Lattice Canvas** (⧉ icon in canvas header) - Opens 1250×900 window
  - Automatically synchronized with main window updates
  - Renders all layer changes and sequence updates in real-time
- **Controls** (⧉ icon in controls header) - Opens 800×600 window
  - All buttons and inputs fully functional in pop-out
  - Bidirectional synchronization with main window
  - Changes in either window update the other
- **Selected Block** (⧉ icon in block section) - Opens 600×800 window
  - Interactive t spinner works in pop-out
  - Clickable successor links navigate to new blocks
  - Updates when selection changes
- **Lattice Parameters** (⧉ icon in parameters section) - Opens 600×800 window
  - Congruence navigation links functional
  - Updates with sequence changes

**Behavior:**
- Clicking a pop-out icon hides that section in the main window
- Other sections flow into freed space (e.g., controls move under lattice)
- Closing a pop-out window automatically restores the section in main window
- Clicking pop-out icon when already open focuses the existing window
- Pop-outs open as actual windows (not tabs) with cascaded positioning

**Use Cases:**
- Multi-monitor setups: spread sections across displays
- Focus mode: show only lattice while keeping controls accessible
- Comparison workflows: keep parameters visible while exploring sequences
- Screen space optimization: hide sections not currently needed

## Mathematical Background

### The O-R Coordinate System

For a value x in a Collatz sequence:
- **o**: Number of odd steps remaining to reach 1
- **e**: Number of even steps remaining to reach 1
- **r**: Defined as r = 2o - e

This coordinate system transforms the chaotic-appearing Collatz sequence into structured trajectories on the (o,r) lattice.

### Block Parameters

Natural blocks use 3 parameters (α, β, ρ) with scaling parameter t:
- **α (alpha)** = v₂(x + 1): 2-adic valuation of x + 1
- **β (beta)** = v₂(3^α·ρ̄ - 1): Even tail length
- **ρ (rho)**: Odd residue parameter
- **t**: Scaling parameter enumerating block instances
- **κ (kappa)** = α + β: Total even steps in the block

Composite blocks add **φ (phi)** ≥ 0 measuring deviation from natural block structure. For composites, ρ and φ are rational with 3-power denominators.

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
3. **Pop out sections**: Click ⧉ icons to open sections in separate windows for flexible layout
4. **Explore points**: Hover over lattice points to see detailed analysis
5. **Navigate sequences**:
   - Double-click any lattice point to load its sequence
   - Click congruence links (← prev | current | next →) to explore structurally similar values
   - Browser history is updated, allowing back/forward navigation between sequences
6. **Animate transformation**: Click "Animate to λₓ" to visualize the geometric relationship
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
