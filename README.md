# MOF Methane Adsorption — Data Collection Pipeline

Streamlined workflow for collecting, filtering, matching, and cleaning MOF crystal structures and their associated methane adsorption isotherms from the NIST ISODB and Cambridge Structural Database (CSD).

**Final output:** 391 MOFs with confirmed CSD identifiers, cleaned CIF files, and normalised methane isotherms — ready for ML training.

---

## Notebooks (run in order)

| # | Notebook | Kernel | Purpose |
|---|----------|--------|---------|
| 01 | `01_filter_nist_isotherms` | Standard Python | Load NIST ISODB, apply quality filters (experimental only, CH₄, temperature range, unit normalisation), remove non-MOFs, handle duplicates, apply coherence corrections, export cleaned dataset |
| 02 | `02_scrape_csd_metadata` | **CSD Python** | Extract MOF metadata from CSD MOF subset (~135k entries), curate synonyms, deduplicate by paper |
| 03 | `03_match_nist_to_csd` | **CSD Python** | Match filtered NIST MOFs to CSD identifiers via DOI matching (MOF subset + full CSD fallback with pickle cache) |
| 04 | `04_llm_formula_match_2LLM_new` | Standard Python | LLM-assisted matching of remaining unmatched MOFs via formula inference (Anthropic Haiku) + CSD candidate scoring |
| 04b | `04_clean_cifs_samosa` | **CSD Python** | Convert CIFs to P1 symmetry, run SAMOSA solvent removal pipeline |
| 05 | `05_gather_cifs_and_isotherms` | **CSD Python** | Merge isotherms with known MOF metadata, retrieve CIF files from MOSAEC / CoRE-MOF / CCDC, copy isotherm JSONs, normalise units on exported copies |

---

## Pipeline Coverage

| Stage | MOFs | Coverage |
|-------|------|----------|
| NIST ISODB after filtering | 418 | 100% |
| Stage 1 — DOI matching (notebook 03) | 223 | 53.3% |
| Stage 2 — LLM pass-1 (Haiku API + element filter) | 120 | 28.7% |
| Stage 2 — LLM pass-2 (synonym / knowledge / formula search) | 48 | 11.5% |
| **Total matched** | **391** | **93.5%** |
| Removed (no CSD match found) | 27 | 6.5% |

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

**Generated outputs (tracked in this repo):**
| File | Rows | Description |
|------|------|-------------|
| `data/filtered_isotherms.xlsx` | 391 | Final filtered isotherm table (post-removal of unmatched) |
| `data/matched_mofs.csv` | 247 | DOI-based matches from notebook 03 |
| `data/llm_matched_mofs.csv` | 179 | LLM-based matches (pass-1 + pass-2) |
| `data/llm_pass2_audit.csv` | 94 | Full pass-2 audit trail with confidence scores |
| `data/llm_unmatched.csv` | 16 | Legacy unresolved entries (outside filtered list) |
| `data/truly_unmatched_mofs.csv` | 27 | MOFs removed from pipeline (no CSD match) |
| `data/step8_unit_normalisation_audit.csv` | — | Unit conversion audit trail |
| `data/samosa_pipeline/reports/` | — | P1 conversion and SAMOSA reports |

**Not tracked (regenerate or obtain separately):**
| File | Reason |
|------|--------|
| `data/nistdb.pickle` | 121 MB cache — auto-regenerated from `isodb-library/` |
| `data/full_csd_doi_index.pickle` | Large cache — auto-regenerated on first run of notebook 03 |
| `data/step-00/01/02.csv` | CSD MOF Subset data — subject to CCDC license |
| `data/MOSAEC_CIFs_found/` | Third-party CIF files — subject to respective licenses |
| `data/CoRE_MOF_CIFs_found/` | Third-party CIF files |
| `data/CCDC_CIFs_found/` | Third-party CIF files — requires CCDC access |
| `data/samosa_pipeline/` (CIF subdirs) | Processed CIF files — regenerate with notebook 04b |

---

## Dependencies

```
pip install -r requirements.txt
```

- **Python 3.9+** — notebooks 01, 04
- **CSD Python API (v3.6.1+)** — required for notebooks 02, 03, 04b, 05; install separately via CCDC
- **SAMOSA** — cloned as a git submodule at `external/SAMOSA`; initialise with:
  ```bash
  git submodule update --init --recursive
  ```
- **Anthropic API key** — required for notebook 04 LLM matching; set in `data/.env`:
  ```
  ANTHROPIC_API_KEY=sk-ant-...
  ```

---

## Archive

Original (pre-streamlined) notebooks are preserved in `archive/`. Obsolete intermediate data files are in `data/archive_data/`.

---

## Final Outputs Location

Cleaned CIFs and normalised isotherms are exported to:
```
C:\Users\james\OneDrive - Aix-Marseille Universite\CNE Wroclaw 2026\CNE Thesis 2026\MOF CIFs DATABASE - ALL
```
