# Automated Exoplanet Detection Pipeline (TESS Sector 101)

An automated photometric transit search pipeline deployed on ~13,000 stellar targets from NASA's Transiting Exoplanet Survey Satellite (TESS) Sector 101. 

Supervised by **Pr. Roland Habchi**, Head of the Physics Department, Lebanese University (Faculty of Sciences II).

---

## Scientific Overview & Methodology

This pipeline automates the extraction, detrending, cross-matching, and transit modeling of TESS 2-minute cadence SPOC (Science Processing Operations Center) light curves to identify undiscovered exoplanetary candidates.

### Key Pipeline Features:
1. **Automated Archive Retrieval:** Queries the Mikulski Archive for Space Telescopes (MAST) programmatically via `Lightkurve` to download Pre-search Data Conditioning Simple Aperture Photometry (PDCSAP) flux vectors.
2. **Brightness Filtering:** Restricts searches to $8.0 \le T_{\rm mag} \le 13.0$ to eliminate saturated pixels and photon-noise dominated regimes.
3. **NASA Exoplanet Archive TOI Cross-Matching:** Cross-matches targets at session start against the live TESS Objects of Interest (TOI) catalog via the NASA Exoplanet Archive API, filtering out known planets (KP), confirmed planets (CP), candidates (PC), and eclipsing binaries (EB).
4. **Transit Least Squares (TLS) with Star-Specific Limb Darkening:** Unlike standard Box Least Squares (BLS), the pipeline dynamically queries the TESS Input Catalog (TIC) to retrieve per-star stellar mass ($M_*$), radius ($R_*$), and quadratic limb-darkening coefficients ($u_1, u_2$). It fits the Mandel & Agol (2002) transit profile, yielding ~10% higher recovery rates for small planets ($R_p < 2 R_\oplus$).
5. **Statistical Classification & Automated Logging:** Classifies signals based on Signal Detection Efficiency (SDE):
   - `CANDIDATE`: $\text{SDE} \ge 7.1$ (Hippke & Heller 2019 threshold)
   - `WEAK`: $5.0 \le \text{SDE} < 7.1$
   - `NOISE`: $\text{SDE} < 5.0$
   Full batch run results and metadata are logged with continuous resume capability in CSV and Excel formats.

---

## Validation on Known Transiting System (TOI-1130)

To validate the pipeline end-to-end, it was run on benchmark star **TIC 261136679 (TOI-1130)** using TESS Sector 28 SPOC photometry.

| Parameter | Published Value | Pipeline Recovered Value | Accuracy |
| **Orbital Period ($P$)** | 6.2648 days | **6.2653 days** | **0.005% deviation** |

| **Detection Significance** | — | **SDE = 14.8** | Exceeds 7.1 threshold by $2\times$ |
| **Transit Depth ($\delta$)** | $\sim 250$ ppm | **$0.99975$ (norm. flux)** | Highly consistent across 4 epochs |

### Four-Panel Diagnostic Vetting Plot
[validation_plot.png]
*Panel breakdown: (Top-left) Cleaned, normalized PDCSAP light curve; (Top-right) TLS periodogram showing dominant period peak at 6.265 days; (Bottom-left) Phase-folded transit showing Mandel-Agol limb-darkened model overlay; (Bottom-right) Individual transit depth consistency across epochs.*

---

## Dependencies & Requirements

```bash
pip install lightkurve transitleastsquares astropy numpy pandas matplotlib requests openpyxl
