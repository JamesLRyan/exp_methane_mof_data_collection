# LLM MOF-to-CSD Matching Pipeline — Coverage Report
Generated: 2026-05-21

## Summary

| Stage | MOFs matched | Coverage |
|-------|-------------|----------|
| Total NIST MOFs (after filtering) | 418 | 100% |
| Stage 1 — DOI matching (notebook 03) | 223 | 53.3% |
| Stage 2 — LLM pass-1 (Haiku API, element filter) | 120 | 28.7% |
| Stage 2 — LLM pass-2 (local: synonym / knowledge / formula) | 48 | 11.5% |
| **Total matched** | **391** | **93.5%** |
| Still unmatched | 27 | 6.5% |

## Pass-2 Method Breakdown (48 matches)

| Method | Count |
|--------|-------|
| CSD synonym lookup | 13 |
| Chemistry knowledge table | 16 |
| Programmatic formula search | 22 |

## Sanity Checks (all pass ✓)

- No overlap between DOI-matched and LLM-matched sets
- All LLM-matched CSD identifiers verified present in step-02.csv (135,253 entries)
- No duplicate hashkeys in llm_matched_mofs.csv
- All 27 unmatched MOFs tracked in llm_unmatched.csv
- All matched entries have confidence ≥ 0.40

## Output Files Updated

| File | Rows | Description |
|------|------|-------------|
| `data/llm_matched_mofs.csv` | 179 | All LLM-matched MOFs (pass-1 + pass-2) |
| `data/llm_unmatched.csv` | 43 | Remaining unresolved MOFs (43 total; 27 in filtered list) |
| `data/llm_pass2_audit.csv` | 94 | Full pass-2 audit trail with all confidence scores |

## 27 Truly Unmatched MOFs

These MOFs appear in the filtered NIST list but were resolved by neither DOI nor LLM matching.
Most have generic/non-standard names (MOF-1C', MOF-1M', Zn4O, WV-1050) or are variants
(Co21-MOF-5, MOF-508b) that lack a clear unique CSD entry.

| nist_name                           | nist_hashkey                                | doi                             |
|:------------------------------------|:--------------------------------------------|:--------------------------------|
| (me2NH2)6[In10(TTCA)12]*24DMF*15H2O | NIST-MATDB-dc1f4b7ddd96245d3704815ee72b638d | 10.1021/Ic501413r               |
| Al-BDC                              | NIST-MATDB-cce03d7a2de79334704ac30e9b3ab673 | 10.1016/j.micromeso.2016.01.030 |
| Co21-MOF-5                          | NIST-MATDB-1d7283de5a7f43b960a5dd98d52ee542 | 10.1016/j.electacta.2013.10.190 |
| Cu-SDU-1                            | NIST-MATDB-ed5049cd0f3b495ea431f5c70dd110be | 10.1021/Ic5017092               |
| DUT-25                              | NIST-MATDB-e7c1a673818ca05ad7638253e9b7cbc3 | 10.1002/chem.201202352          |
| MOF-1C'                             | NIST-MATDB-07af26bd81573554d0dfd228787e3e50 | 10.1016/j.micromeso.2010.10.048 |
| MOF-1C'-Li                          | NIST-MATDB-7cbc8489b9167ea2715313a978d69d2d | 10.1016/j.micromeso.2010.10.048 |
| MOF-1M'                             | NIST-MATDB-e3d41515552e2ff3025163484768aab2 | 10.1016/j.micromeso.2010.10.048 |
| MOF-1M'-Li                          | NIST-MATDB-2af8e8ffa63fe22e424cc773bf4c5ba4 | 10.1016/j.micromeso.2010.10.048 |
| MOF-2'                              | NIST-MATDB-7a6c5ef61582a13f273203d99661f16f | 10.1016/j.micromeso.2010.10.048 |
| MOF-2'-Li                           | NIST-MATDB-c66fcb7fe4e6e8bddff9867285addb18 | 10.1016/j.micromeso.2010.10.048 |
| MOF-508                             | NIST-MATDB-a4b3fc60364cef8a9eb12e7bbc908a8b | 10.1021/Ja904782h               |
| MOF-508b                            | NIST-MATDB-eb8a03810908350733a3ad317d9a5927 | 10.1002/anie.200502844          |
| NOTT-102a                           | NIST-MATDB-057c4e880a99e188b6631fbf7739748b | 10.1039/C3ee41166d              |
| PCN-14                              | NIST-MATDB-491f2d4553a5f70eb3e8a54f967be37f | 10.1016/j.energy.2015.08.096    |
| SDU-6                               | NIST-MATDB-4f85440d6d4094ed428b9f705dc7dd2f | 10.1021/Ic3015207               |
| SDU-7                               | NIST-MATDB-2be8da142933090d1f2b6bc5e697086f | 10.1021/Ic3015207               |
| SDU-8                               | NIST-MATDB-03645c4b39bf449dbc22ac8d02c59f8b | 10.1021/Ic3015207               |
| UMCM-150                            | NIST-MATDB-8b8f5dc21e8758d0adfbc6da4cee39a6 | 10.1007/s10934-015-0060-4       |
| UTSA                                | NIST-MATDB-f168f08e72b4714243e42c649168c63b | 10.1021/acs.jpcc.6b10751        |
| WV-1050                             | NIST-MATDB-35609bd6d3323afd4f16231afa50faaa | 10.1007/s10450-021-00330-y      |
| WV1050                              | NIST-MATDB-379eabf2c0360c5c1d4a172186cf72a9 | 10.1007/s10450-014-9639-3       |
| ZIF-93                              | NIST-MATDB-5a53918a0d77cc8dc4fdaed222acfb5f | 10.1016/j.seppur.2019.115850    |
| Zn-DABCO                            | NIST-MATDB-df647d61b00b35b8b88ae26030802346 | 10.1002/ceat.201300046          |
| Zn4O                                | NIST-MATDB-73875395ea6add86b0f24072ddc472e5 | 10.1007/s10450-014-9620-1       |
| Zn4O (rho=300kg/m^3)                | NIST-MATDB-25d294b7f85566e0a4522bc95245bbf8 | 10.1007/s10450-014-9620-1       |
| [Co(2)(pyridine)2(H2O)]             | NIST-MATDB-002415bcd538b26626f2f03e5443f598 | 10.1039/B927499e                |
