# MOF Methane Adsorption — Data Collection Pipeline

Workflow for collecting, filtering, matching, and cleaning MOF crystal structures and their associated methane adsorption isotherms from the NIST ISODB and Cambridge Structural Database (CSD).

---

## Notebooks (run in order)

| # | Notebook | Kernel | Purpose |
|---|----------|--------|---------|
| 01 | `01_filter_nist_isotherms` | Standard Python | Load NIST ISODB, apply quality filters (experimental only, CH₄, temperature range, unit normalisation), remove non-MOFs, handle duplicates, apply coherence corrections, export cleaned dataset |
| 02 | `02_scrape_csd_metadata` | **CSD Python** | Extract MOF metadata from CSD MOF subset (~135k entries), curate synonyms, deduplicate by paper |
| 03 | `03_match_nist_to_csd` | **CSD Python** | Match filtered NIST MOFs to CSD identifiers via DOI matching (MOF subset + full CSD fallback with pickle cache) |
| 04 | `04_llm_formula_match` | Standard Python | LLM-assisted matching of remaining unmatched MOFs: fetch paper abstracts via OpenAlex, infer empirical formula (Haiku), score against CSD candidates by stoichiometry |
| 05 | `05_clean_cifs_samosa` | **CSD Python** | Convert CIFs to P1 symmetry, run SAMOSA solvent removal pipeline |
| 06 | `06_gather_cifs_and_isotherms` | **CSD Python** | Merge isotherms with matched MOF metadata, retrieve CIF files from MOSAEC / CoRE-MOF / CCDC, copy isotherm JSONs, normalise units on exported copies |

---

## Pipeline Coverage

| Stage | MOFs | Notes |
|-------|------|-------|
| NIST ISODB after filtering (NB01) | 418 | Starting point |
| DOI-matched to CSD (NB03) | 247 | Matched via publication DOI |
| LLM-matched (NB04) | 128 | Formula inference + candidate scoring |
| Unmatched — not collected (NB06) | 94 | No CSD entry found; excluded from final dataset |

The 94 unmatched MOFs are not removed from `filtered_isotherms.xlsx` — they simply receive no CIF file in NB06 and are excluded from the final dataset naturally.

---

## Setup

### 1. Install Python dependencies

```bash
pip install -r requirements.txt
```

### 2. CSD Python API

Required for notebooks 02, 03, 05, and 06. Install separately via the CCDC installer — not available on PyPI. See [CCDC documentation](https://downloads.ccdc.cam.ac.uk/documentation/API/).

### 3. SAMOSA (solvent removal)

Cloned as a git submodule at `external/SAMOSA`. Initialise with:

```bash
git submodule update --init --recursive
```

### 4. Anthropic API key

Required for notebook 04. Create `data/.env` with:

```
ANTHROPIC_API_KEY=sk-ant-...
```

### 5. Set output paths

Notebooks 05 and 06 contain a **user config cell at the top** where you set paths to your local databases (MOSAEC, CoRE-MOF) and your output directory. Edit these before running — no paths are hardcoded elsewhere.

---

## Manual Steps

Several stages require importing a manually edited file (marked with ⚠ in notebooks). These occur when:
- Visual or numerical inspection of isotherms leads to exclusions (audit removal lists)
- The automated NIST-to-CSD merge requires human review of ambiguous matches
- Duplicate isotherms need manual curation of which to keep

The curated input files are preserved in `data/` and referenced by the notebooks.

---

## Key Data Files

**Curated inputs (do not regenerate):**

| File | Description |
|------|-------------|
| `data/duplicate_keep_filenames.txt` | Manually curated duplicate resolution |
| `data/audit_removal_filenames.txt` | Isotherms flagged for removal during visual audit |
| `data/MOFs_from_CCDC_sorted_KNOWN.xlsx` | Known MOF-to-CSD matches |
| `data/MOFs_from_CCDC_sorted_UNKNOWN.xlsx` | Unknown / ambiguous matches |
| `data/filtered_isotherms_merged_with_CCDC_manual.xlsx` | Manually reviewed merge |

**Generated outputs (tracked):**

| File | Rows | Description |
|------|------|-------------|
| `data/filtered_isotherms.xlsx` | 418 | Filtered NIST isotherm table (output of NB01) |
| `data/matched_mofs.csv` | 247 | DOI-based CSD matches (output of NB03) |
| `data/unmatched_mofs.csv` | — | MOFs passed to NB04 (output of NB03) |
| `data/llm_matched_mofs.csv` | 128 | LLM-based CSD matches (output of NB04) |
| `data/llm_unmatched.csv` | 94 | MOFs NB04 could not resolve (output of NB04) |
| `data/step8_unit_normalisation_audit.csv` | — | Unit conversion audit trail |
| `data/samosa_pipeline/reports/` | — | P1 conversion and SAMOSA reports |

**Not tracked (regenerate or obtain separately):**

| File / Folder | Reason |
|---------------|--------|
| `data/nistdb.pickle` | 121 MB — auto-regenerated from `isodb-library/` |
| `data/full_csd_doi_index.pickle` | Large — auto-regenerated on first run of NB03 |
| `data/step-00/01/02.csv` | CSD MOF Subset — subject to CCDC licence |
| `data/MOSAEC_CIFs_found/` | Third-party CIF files |
| `data/CoRE_MOF_CIFs_found/` | Third-party CIF files |
| `data/CCDC_CIFs_found/` | Requires CCDC access |
| `data/samosa_pipeline/` (CIF subdirs) | Regenerate with NB05 |
