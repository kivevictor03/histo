# Atlas Fizjologii i Histologii

A Polish-language interactive web app with a homepage menu that branches into multiple sections (interactive simulators, histology gallery, other study materials).

## Architecture

- Single-file static HTML app (`index.html`)
- Uses D3.js v7 (CDN) for SVG rendering and animation
- Served via Python's built-in HTTP server on port 5000
- **View router** (top-level): four `.view` blocks (`#view-home`, `#view-sims`, `#view-histo`, `#view-other`); only one carries `.active` at a time. `showView(name)` toggles, syncs URL hash, scrolls to top, and hides global footer on home.
- Inside `#view-sims`: tabbed UI hosting the simulator modules (`#tab-renal`, `#tab-ph`)

## Homepage (`#view-home`)

- Hero: gradient title "Atlas Fizjologii i Histologii" + subtitle
- 3-card grid (`.home-grid` / `.home-card`):
  1. **Symulatory interaktywne** (tag `Dostępne`, target=`sims`) — opens GFR + pH simulators
  2. **Histologia — preparaty mikroskopowe** (tag `Wkrótce`, target=`histo`) — placeholder for microscope photo gallery
  3. **Inne materiały** (tag `Wkrótce`, target=`other`) — placeholder for additional study materials
- Author credit moved here: ornament `⸺ ⟡ ⸺` + `Autor projektu: @kive_victor` linking to Instagram (`.home-credit`)
- Global `<footer id="page-footer">` is hidden on the home view (the home credit replaces it) and shown on all sub-views

## Sub-section views

- Each non-home view starts with `.back-bar` containing a `← Menu główne` button (`data-target="home"`) and a breadcrumb label
- `#view-histo` and `#view-other` use `.placeholder` cards with icon + headline + description + `W przygotowaniu` / `Wkrótce` badge

## Simulators (inside `#view-sims`)

### 1. Ciałko Nerkowe (GFR) — `#tab-renal`
- Layered glomerulus diagram (podocyte petals, mezangium, JG cell, afferent/efferent arterioles)
- Animated blood + golden filtrate particles
- Sliders: MAP, afferent arteriole, efferent / Ang. II, mezangium
- GFR clinical scale bar (G1–G5)
- Interactive RAAS pathway side panel (lights up nodes on low MAP / high Ang. II)
- Reset button restores defaults: MAP=100, aff=100, eff=100, mes=0

### 2. Komórki Wstawkowe (pH) — `#tab-ph`
- Three-band diagram: KREW (OSOCZE) / KOMÓRKA WSTAWKOWA / ŚWIATŁO CEWKI (MOCZ); clean background (no static "frozen-animation" ions, no redundant text)
- **Inputs**: paired slider + number input for `[HCO₃⁻]` (5–45 mmol/L, default 24) and `pCO₂` (15–80 mmHg, default 40); animation speed/density sliders; ions toggle; Reset button
- **pH is computed** from Henderson–Hasselbalch: `pH = 6.1 + log₁₀([HCO₃⁻] / (0.03 × pCO₂))`
- Live equation panel renders the symbolic formula AND the substituted numeric expansion (intermediate denominator, ratio, log, final pH) — color-coded variables and a legend explaining pKa, [HCO₃⁻], pCO₂, and the 0.03 solubility constant
- Bidirectional sync: typing in number input updates slider, moving slider updates number — both fire `drawPh()`
- Status logic: <7.20 ciężka kwasica · 7.20–7.34 kwasica · 7.35–7.45 norma · 7.46–7.60 zasadowica · >7.60 ciężka zasadowica
- Type A active in acidosis (H⁺ secretion ↓ to urine, HCO₃⁻ reabsorption ↑ to blood); Type B in alkalosis (reversed direction; cell label changes)
- Animated text-label ions ("HCO₃⁻", "H⁺") only appear when toggle is ON AND pH is abnormal — no static populations
- Clinical scenarios set both `data-hco3` and `data-pco2` (norma 24/40 · DKA 12/28 · POChP 30/60 · zas. met. 36/44 · hiperwentylacja 22/25)
- Side panel: Type A card, Type B card (highlight when active), buffer systems info

## JS organization

- Renal sim code first, scoped: `document.querySelectorAll("#tab-renal input")`
- Tab switching: `.tab` buttons toggle `.active` on `.tab` and `.tab-pane`
- pH sim code (`drawPh`) below tab switch logic, scoped: `document.querySelectorAll("#tab-ph input")`

## Workflow

- **Start application**: `python3 -m http.server 5000` (port 5000, webview)

## Author

Footer credit: Instagram [@kive_victor](https://www.instagram.com/kive_victor)
