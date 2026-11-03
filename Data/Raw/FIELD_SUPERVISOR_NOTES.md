# Field Supervisor Inventory Audit Memo — Gandaki Elevational Transect (2025–2026)

**Project:** Gandaki Watershed REDD+ Forest Carbon MRV & Permanent Sample Plot (PSP) Network  
**Survey Region:** Gandaki Province, Nepal (Nawalpur Terai -> Pokhara/Panchase -> Gorkha/Barpak -> Lamjung/Marsyangdi)  
**Raw Data File:** `data/raw/gandaki_raw_inventory.csv` (Sacred Read-Only Raw Tally Export — **DO NOT EDIT IN EXCEL**)

---

## 1. Sampling Design & Plot Geometry
- **24 Geo-Referenced Permanent Sample Plots (`G01` to `G24`)** stratified across **4 Forest Stands** (`6 plots per stand`) spanning **342 m to 2,395 m a.s.l.**:
  - `ST01_Nawalpur_Sal` (*Community_Forest*, Lowland Terai–Siwalik): 4 plots of `0.10 ha` + 2 plots of `0.20 ha` = **`0.80 ha`**
  - `ST02_Pokhara_Panchase` (*Protected_Watershed*, Lower Mid-Hill): 5 plots of `0.10 ha` + 1 plot of `0.20 ha` = **`0.70 ha`**
  - `ST03_Gorkha_Barpak` (*Restoration_Leasehold*, Middle Mountain): 5 plots of `0.10 ha` + 1 plot of `0.20 ha` = **`0.70 ha`**
  - `ST04_Lamjung_Marsyangdi` (*Conservation_Area*, Upper Mid-Hill): 3 plots of `0.10 ha` + 3 plots of `0.20 ha` = **`0.90 ha`**
- **Total Sampled Area:** **`3.10 ha`** across 24 plots. Never assume a constant plot size when scaling to per-hectare (`ha^-1`) stand metrics!

---

## 2. Official Field Protocol Codes & Known Data-Entry Issues
During post-monsoon data digitization in Pokhara, the field supervisor logged the following quality-control notes:

1. **Paper Tally Sheet Exception — Plot `G14`, Tree `T042` (*Order-of-Operations Alert!*):**
   - In the raw CSV, `Plot_ID == "G14"` and `Tree_ID == "T042"` (*Castanopsis indica*, `Height_m = 21.4 m`) appears with `DBH_cm = 999`.
   - **Waterproof Field Sheet Verification:** This was **NOT** a missing measurement. Mud smudged the decimal point on sheet #14; the verified caliper/tape measurement is **`34.8 cm`**.
   - *Pipeline Requirement:* Correct `G14 / T042` to `34.8` **before** converting protocol `999` codes to `NA`!

2. **Protocol Missing-Value Placeholders (`-99` and `999`):**
   - `-99` = Instrument/measurement missing in field (e.g., dense monsoon canopy blocked laser rangefinder sightline for tree height).
   - `999` = Attribute unrecorded on tally sheet (except `G14 / T042` above).

3. **Millimetre Diameter-Tape Slips (`DBH_cm > 150`):**
   - No tree in these 24 sample plots exceeds `100 cm` DBH. Any valid non-placeholder record with `150 < DBH_cm < 900` was recorded from the **millimetre (`mm`)** side of a dual-scale tape by student interns and must be divided by **`10`** (`mm -> cm`).

4. **Canopy-Occluded Missing Heights (`Height_m` is `NA`, `-99`, or `999` on Valid DBH Stems):**
   - For **18 trees** in the 2025 baseline census, `DBH_cm` was accurately measured, but total height could not be sighted through closed canopy.
   - *Pipeline Requirement:* Do **not** discard these 18 carbon-bearing trees. Fit a Height–Diameter linear regression model (`lm(Height_m ~ DBH_cm)`) on clean, mechanically plausible 2025 calibration trees and **impute** the 18 missing heights (tracking them with `height_imputed_flag = TRUE`).

5. **Mechanical Stability / Slenderness Screening (`Height_m / DBH_cm > 2.0`):**
   - A few pole-sized stems have `Height_m / DBH_cm > 2.0` (e.g., laser rangefinder accidentally locked onto an emergent upper-canopy branch behind a `10 cm` pole).
   - **Do NOT silently delete** these rows—flag them in the supervisory QC audit log (`height_flag = TRUE`) and exclude them when calibrating the `lm()` Height–Diameter regression line.

6. **Clerical Duplicates vs. 2026 Permanent Sample Plot (PSP) Remeasurements:**
   - **Same-day clerical duplicates:** A data-entry clerk accidentally typed a handful of rows twice (identical `Plot_ID`, `Tree_ID`, and `Measurement_Date`). Deduplicate these.
   - **2026 PSP Remeasurement Campaign:** Twenty tagged trees in Plots `G01` and `G07` were remeasured in **October 2026** (`Census_Year == 2026`) to monitor annual diameter increment. Separate and save these 20 longitudinal records to `data/processed/gandaki_2026_remeasurements.csv` so the primary carbon stock baseline strictly reflects **`Census_Year == 2025`**.

7. **Botanical Binomial Normalisation:**
   - Different field crews used shorthand abbreviations (`"S. robusta"`, `"C. indica"`, `"Q. semecarpifolia"`), typos (`"Shorea rubusta"`, `"Schima wallichi"`, `"Pinus roxburgi"`), and trailing spaces. Preserve `Species_raw` and standardise into `Species_standard` (**10 valid scientific species**).
