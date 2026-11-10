# Raman Plotter

A single-file web page for plotting Raman spectra exported by Horiba LabSpec (`.txt`) and producing publication-style figures: individual, stacked or overlaid spectra, automatic peak labels with a peak table, and export to PNG (600 dpi), SVG and CSV.

There is nothing to install. Open `Raman_Plotter.html` in any modern browser (Chrome, Edge, Firefox, Safari). The page makes no network requests: your files are read locally and never uploaded anywhere.

> **Status:** version 1.0 (initial release). Optional processing tools (Savitzky–Golay smoothing, polynomial baseline) are under development and are not part of this release. See [Roadmap](#roadmap).

---

## Quick start

1. Download `Raman_Plotter.html` and double-click it (or open it from your browser with *File → Open*).
2. Drag one or more spectrum files onto the box **Drag and drop .txt Raman file** (or click the box to browse).
3. Choose the **Mode** (Individual, Stacked or Overlay), adjust the x range and options.
4. Click **Download PNG (600 dpi)**, **SVG** or **Peaks CSV**.

## Input files

- Accepted extensions: `.txt`, `.csv`, `.dat`.
- Two columns: Raman shift (cm⁻¹) and intensity. Separators can be tab, space, semicolon or comma; a decimal comma is accepted.
- Lines that are empty or start with `#` are ignored. Files with fewer than 10 valid points are skipped.
- Loading a file with the same name as one already loaded replaces it.

### File names (optional, but useful)

If the file name follows the LabSpec pattern below, the page reads the acquisition parameters and fills the legend automatically:

```
<sample>_<time>s_<accumulations>_<laser>nm_<filter>_<grating> (<range>)_x<objective>_<hole>_<pinhole>_<power>%.txt
```

Example: `NbC_Li_10s_3_532nm_Edge_600 (500)_x50_LWD_100_10%.txt`

The parameters appear under the legend label (for example `532 nm · 10 s × 3 · 600 gr/mm|Edge/LWD · pinhole 100 · 10%`). If the name does not match, the spectrum still loads; the sample name is simply the file name, and you can type any label or parameter text yourself (see below). In Stacked and Overlay modes, samples are ordered by laser wavelength read from the file name.

### Reference spectra

Files whose names start with `ref_` (for example `ref_NbC_literature.txt`) are treated as **references**: they get their own colors, are drawn after the samples, and are not peak-picked. The `ref_` prefix is removed from the legend and underscores become spaces. In Overlay mode they share the baseline of the samples.

---

## Controls

### Spectra (one block per loaded spectrum)

| Control | What it does |
|---|---|
| Color | Line color. Samples start black; references get colors from a palette. |
| Line type | Solid, dashed or dotted. |
| Line width (number box) | Width for this spectrum only. Empty = use the global width (shown in gray as a hint). |
| × | Remove this spectrum. |
| Legend / Description | Show or hide the legend label / the acquisition parameters of this spectrum. |
| Label field | Custom legend text (empty = automatic). |
| Parameters field | Custom text for the parameters under the label. Use `|` to start a new line. |

### Figure

| Control | What it does |
|---|---|
| Mode | **Individual**: one sample at a time (pick it in *Spectrum*). **Stacked**: all samples one above the other. **Overlay**: all samples on the same axes. |
| x min / max (cm⁻¹) | Plot range. `x min` empty = automatic. Processing and normalization use only this range. |
| Normalize (0–1) | Subtracts each spectrum's minimum and divides by its maximum, inside the x range. When off, the minimum is still subtracted and the y axis shows intensity in the original units. |
| Smooth (moving avg.) | Moving-average smoothing; the number box is the window in points (odd, ≥ 3). Off by default, so data are plotted as exported. |
| Show y axis | Draws y-axis ticks and numbers. Hidden by default (usual for normalized spectra). |
| Plain y numbers | With Normalize off, writes `20000` instead of `2e+4`. Values are rounded to 2 significant digits. |
| Title, X axis title, Y axis title | Free text. Use `^{ }` for superscript and `_{ }` for subscript, e.g. `Raman shift (cm^{-1})`, `CO_{2}`. |
| Show grid | Light grid lines. |
| Show acquisition parameters | Show or hide the parameter lines under each legend label. |
| Legend | *On each trace*, a corner (top/bottom, left/right), or *None*. In Overlay mode "On each trace" becomes top right. |
| Reset legend positions | Puts legends back where they were. **You can drag any legend in the figure to move it.** |
| Stack offset | Vertical spacing between spectra in Stacked mode, in units of the tallest spectrum (1.4 = 140 %). |
| Width × height (px) | Figure size. The PNG is exported at 600 dpi for this size. |
| Font / line width (all) | Font size and the global line width, applied to everything not overridden per spectrum. |

### Peaks

| Control | What it does |
|---|---|
| Show labels | Writes the position of each peak above it. |
| Auto detection | Finds local maxima automatically. |
| Min. prominence | Smallest accepted peak, as a fraction of the spectrum's intensity range (0.05 = 5 %). Increase to get fewer peaks. |
| Min. distance (cm⁻¹) | Peaks closer than this are merged; the taller one is kept. |
| Add | Positions to force, separated by commas, e.g. `877, 125`. The nearest maximum within ±10 cm⁻¹ is used. |
| Remove | Positions to delete, e.g. `1450` (tolerance ±8 cm⁻¹). |
| Click on the plot | Adds a peak at that position (on the spectrum under the click). |
| Click on a label | Removes that peak. |

Below the figure, a table lists **position**, **relative intensity** and **FWHM** for every peak. Notes:

- FWHM is measured on the plotted curve (after smoothing, if enabled) at half of the peak's prominence, using linear interpolation. It is not a fitted line width.
- The intensity column is relative only when Normalize is on.

### Export

| Button | File |
|---|---|
| Download PNG (600 dpi) | `raman_<name>.png` |
| SVG | `raman_<name>.svg` (vector, editable in Inkscape or Illustrator) |
| Peaks CSV | `raman_<name>_picos.csv` with columns `spectrum, position_cm-1, norm_intensity, FWHM_cm-1` |

For stacked or overlaid figures, `<name>` is `stacked`.

---

## Tips

- For a clean comparison figure: Stacked mode, Normalize on, Show y axis off, Legend *On each trace*, and drag the labels where they fit.
- To highlight your own measurement over references in Overlay mode, give it a larger individual line width.
- If your data were already smoothed or baseline-corrected elsewhere (for example in MATLAB), leave Smooth off.
- Tick labels on the x axis use fixed steps (10, 20, 25, 50, 100 … cm⁻¹) chosen from the range; narrowing x min / max refines them.

## Known limitations

- Only the Smooth (moving average) option is available in this version; there is no baseline correction yet.
- The page was written to be self-contained, so it works from a local file with no internet connection.
- Peak detection is intended for exploration. Check labeled positions against your data before reporting them.

## Roadmap

- Savitzky–Golay smoothing (window and polynomial order)
- Polynomial baseline correction (iterative, with peak clipping)
- Buttons to apply the processing and to return to raw data
- Option to display the fitted baseline over the spectrum

## Files in this repository

| File | Description |
|---|---|
| `Raman_Plotter.html` | The application (open it in a browser). |
| `README.md` | This document. |
| `LICENSE` | License terms. |

## License

Released under the [MIT License](LICENSE): you are free to use, copy, modify and redistribute this software, provided the copyright and license notice are kept.

## Acknowledgments

This software was developed with the assistance of [Claude](https://www.anthropic.com/claude), an AI model by Anthropic.

## Author

Fernando Albuquerque  
Nuclear and Energy Research Institute (IPEN) / University of São Paulo  
fernandoafalbuquerque\@gmail.com
