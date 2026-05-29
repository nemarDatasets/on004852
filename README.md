[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on004852-blue)](https://doi.org/10.82901/nemar.on004852)

InsurgentCivilian dataset

This is a placeholder dataset.

## NEMAR curation changes (2026-05-21, revised 2026-05-27)

The BIDS validator went from 1 error + 47 warnings to 0 errors + 37 warnings. The raw `.set` and `.fdt` binary payloads were not modified; every change is to a text sidecar.

**Event dictionary (`task-nback_events.json`)**
- The dataset has one `events.tsv` (`sub-001_task-nback_events.tsv`) with 197 onsets where two rows share the same `onset` value. BIDS allows duplicate onsets, but the HED validator merges the HED annotations for rows that share an onset into one combined string per time bucket. The HED dictionary tagged about 80 of the 107 event codes with the same tag (`"Task"`), so most merged strings became `"Task,Task"` and the validator legitimately fired a duplicate-tag error. Across the 197 collisions, 196 are between codes that both map to `"Task"`; only one pair (codes `3` and `55011`) uses two distinct tags and merges cleanly. This same defect appears byte-identical in the rest of the STRONG cohort (on004849, on004850, on004853, on004854, on004855).
- To remove the duplicate-tag merges without losing event information, the `value.HED` entries for codes `"1"` and `"307"` were dropped (105 of the 107 HED entries remain). Those two codes only ever appear paired with another code already tagged `"Task"`, so dropping their HED entries removes the `"Task,Task"` merges and leaves every lone event still annotated.
- The full `value.Levels` block was left untouched, so all 107 codes still have a human-readable label.
- Added a top-level `sample` column definition so the validator stops flagging it as undocumented.

**Channels table (`sub-001/eeg/sub-001_task-nback_channels.tsv`)**
- All 64 rows had `type=n/a` and `units=n/a`. The channel names are standard 10-10 scalp labels, so they were set to `type=EEG, units=uV` so the validator recognizes them as EEG channels with the conventional microvolt unit. The channel names themselves were not touched.

**Recording sidecar (`sub-001/eeg/sub-001_task-nback_eeg.json`)**
- Added `MISCChannelCount: 0` and `TriggerChannelCount: 0` to make the channel-count bookkeeping explicit (the recording has 64 EEG channels and no misc or trigger channels).
- Added `EEGPlacementScheme: "10-10"` because the channel labels in `channels.tsv` are 10-10 names.
- The other keys in this file were left unchanged.

**Dataset description (`dataset_description.json`)**
- Added `DatasetType: "raw"` so the dataset is validated as raw data rather than a derivative.
- Updated `BIDSVersion` from the previously published value to `1.11.1` (the version the current validator checks against).
- `ReferencesAndLinks` was published as `[""]` (a list containing one empty string, which is not a valid reference) and is now an empty list `[]`.
- `GeneratedBy` was left absent, exactly as the source published it; nothing was added there.

**Remaining warnings (37) — left on purpose**
- These are all "recommended but missing" fields that need information from the study, lab, or equipment that isn't available in the dataset itself, including `GeneratedBy` at the dataset-description level and a long list of EEG-sidecar fields such as `Manufacturer`, `ManufacturersModelName`, `SoftwareVersions`, `DeviceSerialNumber`, `TaskDescription`, `Instructions`, `CogAtlasID`, `CogPOID`, `InstitutionName`, `InstitutionAddress`, `InstitutionalDepartmentName`, `CapManufacturer`, `CapManufacturersModelName`, `EEGGround`, `HeadCircumference`, `HardwareFilters`, `SubjectArtefactDescription`, and `StimulusPresentation`. They were left blank rather than filled with guesses.
