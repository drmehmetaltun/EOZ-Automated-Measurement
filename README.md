# EOZ Analyzer

A browser-based tool for measuring the **Effective Optical Zone (EOZ)** and **decentration** on Pentacam tangential curvature difference maps (TCDM) after corneal refractive surgery (SMILE / KLEx / FS-LASIK).

**[Live Demo →](https://drmehmetaltun.github.io/eoz-analyzer/)**

## What It Does

Upload a Pentacam TCDM screenshot and the tool automatically:

1. **Detects the corneal vertex** (map origin) and **calibrates the scale** from the ruler bar
2. **Segments the EOZ boundary** — either via the color scale (0.00 D iso-contour) or via hue thresholding
3. **Fits an ellipse** to the boundary and reports area, equivalent diameter, major/minor axes, meridians, eccentricity, and axis ratio
4. **Computes decentration** relative to the corneal vertex (dec_CV) and an optional target center (dec_HM)
5. **Exports results** as CSV (single or batch) with full parameter set

All processing runs **entirely in the browser** — no server, no uploads, no data leaves your machine.

## Features

- **Zero-install**: single HTML file, works in any modern browser
- **Dual boundary detection**: scale-based (0.00 D iso-contour via color→dioptre field) or hue-based (color threshold)
- **Pre→Post and Post→Pre maps**: automatic hue threshold adjustment for both subtraction directions
- **Band-middle mode**: fits inner and outer contours of the green 0 D band, averages them for the true boundary
- **Batch processing**: drag up to 500 images, get a CSV + HTML report with summary statistics
- **Interactive tools**: point probe (coordinate readout) and ruler (two-point distance measurement)
- **Optional pupil marking**: when marked, reports decentration from a surgeon-specified target center (pupil + Px/Py offset); when omitted, reports vertex-referenced decentration only
- **Retention ratio**: EOZ/POZ ratio quantifying optical zone shrinkage

## Quick Start

1. Download `index.html` (or clone this repo)
2. Open it in Chrome, Firefox, Safari, or Edge
3. Drag-and-drop a Pentacam TCDM screenshot onto the upload area
4. The tool auto-detects the vertex, calibrates the scale, and is ready to analyze
5. Set the **POZ diameter** (programmed optical zone) and click **Analiz et**

Or use the live GitHub Pages link above.

## Boundary Detection Methods

### Scale-based (recommended)

The tool reads the color scale bar on the right side of the Pentacam map, builds a pixel→dioptre lookup, and extracts the 0.00 D iso-contour using radial interpolation. This is the most accurate method and is used by default when the scale bar is detected.

### Hue-based (fallback)

When the scale bar cannot be read (e.g. cropped images), the tool falls back to HSV color thresholding. Two thresholds define the inner and outer edges of the green 0 D band:

| Map direction | Inner threshold | Outer threshold | Mask logic |
|---|---|---|---|
| Pre − Post | hue < 125° | hue < 149° | warm pixels = EOZ |
| Post − Pre | hue > 149° | hue > 125° | cool pixels = EOZ |

These values are the midpoints between adjacent Pentacam palette swatches (+0.25 D ↔ 0.00 D ↔ −0.25 D) and ensure the boundary tracks the center of the green band.

## Output Parameters

| Parameter | Description |
|---|---|
| EOZ area (mm²) | Contour area (shoelace) and ellipse area (π·a·b) |
| Equivalent diameter (mm) | √(4·area/π) |
| Major / minor axis (mm) | From algebraic ellipse fit (Halır–Flusser) |
| Major meridian (°) | TABO convention |
| Eccentricity, axis ratio | Shape descriptors |
| dec_HM (mm) | Decentration from target center (requires pupil) |
| dec_CV (mm) | Decentration from corneal vertex |
| Retention ratio (%) | EOZ / POZ × 100 |

## Batch Mode

Select multiple images → the tool processes each one independently (auto-calibration, auto-vertex, auto-boundary) and produces:

- **CSV file** with one row per image and all parameters
- **HTML report** with a summary statistics table (mean, SD, median, IQR, min, max)

Batch mode uses vertex-referenced decentration only (pupil cannot be marked in batch).

## Validation

The computational core has been validated against a Python reference implementation — 17/17 parameters matched to machine precision (≈1e−15) on the same test image. The geometric kernel (ellipse fit, meridian conversion, area) was additionally verified against analytic test data.

A clinical validation study (116 eyes) showed strong agreement with ImageJ-based manual measurement:
- EOZ area ICC = 0.999
- Equivalent diameter ICC = 0.999
- Decentration ICC = 0.95–0.98
- Dice coefficient = 0.986

## Technical Notes

- All coordinates use a **corneal vertex origin** with +x nasal, +y superior (right-eye convention; left-eye Px/Py can be flipped via UI toggles)
- The ellipse fit uses the **Halır–Flusser algebraic method** (direct least-squares), not a moment-based fit
- Scale calibration reads **ruler tick marks** on the Pentacam export (median inter-tick spacing)
- For standard 1300×720 Pentacam exports, a **fixed vertex coordinate** (1051.5, 564.5) is used as template

## Requirements

- A modern web browser (Chrome 90+, Firefox 88+, Safari 15+, Edge 90+)
- Pentacam TCDM screenshots (PNG or JPEG recommended; higher resolution = better accuracy)

## License

[MIT](LICENSE)

## Citation

If you use this tool in a publication, please cite:

> *Manuscript in preparation — citation details will be added upon publication.*

## Contributing

Issues and pull requests are welcome. The entire application is a single HTML file with no build step — open `index.html` in a text editor to get started.
