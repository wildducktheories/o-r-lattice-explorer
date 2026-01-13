# O-R Lattice Project Guidelines

## Subscript Rendering Pattern

### Problem
Unicode only supports a limited set of subscript characters:
- Subscript letters: ₐₑₕₖₗₘₙₒₚₛₜₓ (a,e,h,k,l,m,n,o,p,s,t,x only)
- Subscript numbers: ₀₁₂₃₄₅₆₇₈₉

Most letters (like 'f', 'd', 'b', 'c', etc.) do NOT have Unicode subscript equivalents.

### Solution: Manual Subscript Rendering

When displaying subscripts that Unicode doesn't support, use this two-part approach:

#### 1. Canvas Text (Tooltips)

For canvas-rendered text (like tooltips), manually position subscript text:

```javascript
// Example: rendering "x_fd" as x with subscript fd
if (line.includes('x₍fd₎')) {  // Use marker like x₍fd₎ in the string
    const parts = line.split('x₍fd₎');
    let x = tooltipX + padding;

    // Draw prefix
    if (parts[0]) {
        this.ctx.fillText(parts[0], x, currentY);
        x += this.ctx.measureText(parts[0]).width;
    }

    // Draw base character in normal font
    this.ctx.font = '14px monospace';
    this.ctx.fillText('x', x, currentY);
    x += this.ctx.measureText('x').width;

    // Draw subscript in smaller font, offset down
    this.ctx.font = '10px monospace';
    this.ctx.fillText('fd', x, currentY + 6);  // +6px offset for subscript
    x += this.ctx.measureText('fd').width;

    // Draw rest of line
    this.ctx.font = '14px monospace';
    if (parts[1]) {
        this.ctx.fillText(parts[1], x, currentY);
    }
}
```

**Key parameters:**
- Normal font: 14px monospace
- Subscript font: 10px monospace
- Subscript vertical offset: +6px

#### 2. HTML Text (Info Panels)

For HTML-rendered text, use standard `<sub>` tags:

```javascript
// Build the line with HTML
lines.push(`x<sub>fd</sub> = ${value}`);

// Render with innerHTML (NOT textContent)
document.getElementById('infoContent').innerHTML = lines.join('<br>');
```

### NEVER Do This

**DO NOT:**
- ❌ Use non-existent Unicode subscripts (they don't exist!)
- ❌ Use parentheses workarounds: `x₍fd₎` (only as internal marker)
- ❌ Display literal underscores: `x_fd` (confusing notation)
- ❌ Substitute wrong characters: `xₓ` instead of `x_fd`
- ❌ Use `textContent` when HTML tags are needed (use `innerHTML`)

### Standard Notation

When writing code or referring to subscripts:
- Internal marker in code: `x₍fd₎` (using Unicode subscript parentheses)
- Display on canvas: x with 'fd' rendered as visual subscript
- Display in HTML: `x<sub>fd</sub>`

### Implementation Locations

Current implementations in `/Users/jon/personal/o-r-lattice/index.html`:
- **Tooltip rendering**: Lines 1915-1941 (canvas manual positioning)
- **X₀ info panel**: Lines 2236, 2261 (HTML `<sub>` tags)

### Examples

- **x_fd** - x with subscript "fd" (first descent)
- **x_0** - would use Unicode ₀ (subscript zero exists)
- **x_ab** - would require manual rendering (a exists as ₐ, but not b)

## General Guidelines

- Tooltip and X₀ info panel MUST display identical information in identical order
- Tooltip format is the reference - match that exactly
- Include equations in both sections (e.g., `x = 2^α·3^γ·m - 1 = 53`)
