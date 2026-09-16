# Localizer statistical maps and atlas correlations

Subject-level statistical maps for the two functional localizer tasks of this dataset, the corresponding
group-level maps, and a table giving, for every map, its correlation with a published probabilistic atlas
of the network the localizer targets.

## Contents

```
dataset_description.json
README.md
atlas_correlations.tsv        # one row per statistical map
atlas_correlations.json       # column descriptions, correlation method, atlas provenance
sub-<ID>/func/                # subject-level maps (117 participants, 233 maps)
group/                        # group-level maps (n = 116)
```

## Subject-level maps

```
sub-<ID>/func/sub-<ID>_task-<AliceLocalizer|MDLocalizer>_space-IXI549Space_contrast-<intactMinusDegraded|hardMinusEasy>_stat-t_statmap.nii.gz
```

Each file is the SPM t-statistic map (`spmT`) of the localizer contrast for that participant, estimated from the
participant's two runs of that task modelled together. Maps are in SPM12's MNI space (`IXI549Space`),
91 x 109 x 91 voxels at 2 mm isotropic resolution.

- **116 language maps** (`AliceLocalizer`, intact minus degraded), residual degrees of freedom 320-352.
- **117 multiple-demand maps** (`MDLocalizer`, hard minus easy), residual degrees of freedom 552-592.
- **233 maps for 117 of the 118 participants.** `sub-A60` has no localizer data at all and therefore no maps;
  `sub-A30` has a multiple-demand map only (see *Participant notes*).

No session entity is used: the localizer runs fall in different sessions for different participants, and the
maps are estimated per participant rather than per session.

## First-level model

Preprocessing and first-level modelling were carried out with SPM12 through CONN (versions in
`dataset_description.json`). Values below were read from the archived first-level model files.

| | |
|---|---|
| Repetition time | 1.4 s |
| Runs per model | 2 (both runs of the task, modelled as separate sessions) |
| Conditions, language | `intact`, `degraded` — 6 blocks per run, 18 s per block |
| Conditions, multiple-demand | `hard`, `easy` — 5 blocks per run, 34 s per block |
| Basis function | canonical HRF with temporal derivative, onsets in seconds |
| High-pass filter | 128 s |
| Serial correlations | AR(0.2) |
| Global normalization | none |
| Nuisance regressors | six motion parameters per run, plus outlier (scrubbing) regressors where these were generated; the number varies by participant, which is why residual degrees of freedom vary |
| Published contrast | `intact-degraded` (language) and `hard-easy` (multiple-demand), the third contrast of each model |

Functional images were spatially smoothed with a Gaussian kernel before modelling. The archived pipeline logs
record 4 mm FWHM for the `sub-A01`-`sub-A32` runs and a two-step 4 mm followed by 6.93 mm — that is, 8 mm
effective FWHM — for the `sub-A33`-`sub-A77` runs.

## Group maps

```
group/task-<AliceLocalizer|MDLocalizer>_space-IXI549Space_contrast-<intactMinusDegraded|hardMinusEasy>_desc-<coverage|propT309>_<mask|statmap>.nii.gz
```

Both group maps are computed over the same **116 participants** — the 117 participants with maps, minus
`sub-A30`, whose language localizer has only one run.

- `desc-coverage_mask` — number of participants contributing data at each voxel.
- `desc-propT309_statmap` — proportion of those participants whose t value at that voxel exceeds 3.09
  (the one-tailed p < 0.001 threshold).

## atlas_correlations.tsv

One row per statistical map (**233 rows**), giving the correlation of that map with the probabilistic atlas of
its own network (`r_atlas`) and with the atlas of the other network (`r_other_atlas`). Column descriptions and
the full method are in `atlas_correlations.json`.

For each map and atlas the value is the Pearson correlation between the t values and the atlas probability
values, over all voxels where the map is finite and non-zero (the participant's whole-brain analysis mask).
Correlations are not Fisher-transformed, and no further thresholding is applied. Maps and atlases share the
same grid, so no resampling is involved.

`r_other_atlas` is a specificity check rather than a quality metric: for a network-specific map the correlation
with its own atlas is the higher of the two.

## Atlases

The two probabilistic atlases used for the correlations are published by the Fedorenko laboratory (MIT) and are
**not redistributed here**. Their terms require attribution, which would conflict with the CC0 waiver this
dataset is released under. Only the derived correlation values are published. To reproduce the table, download
the atlases from the sources below; the md5 checksums identify the exact files used.

| Atlas label | File | md5 | Source |
|---|---|---|---|
| `Langloc_n806_top10%` | `Langloc_n806_top10%_atlas.nii` | `3d17e6e22a0994eb7716edb1e46c67b4` | https://doi.org/10.6084/m9.figshare.20425209 |
| `MDloc_n691_top10%` | `MDloc_n691_top10%_atlas.nii` | `ee7f316220422c97f4d65e708e338f95` | https://doi.org/10.6084/m9.figshare.22306348 |

Both files are thresholded to the top 10% of voxels and are on the same 91 x 109 x 91, 2 mm grid as the maps.

Please cite:

> Lipkin, B., Tuckute, G., Affourtit, J., Small, H., Mineroff, Z., Kean, H., Jouravlev, O., Rakocevic, L.,
> Pritchett, B., Siegelman, M., Hoeflin, C., Pongos, A., Blank, I. A., Struhl, M. K., Ivanova, A., Shannon, S.,
> Sathe, A., Hoffmann, M., Nieto-Castañón, A., & Fedorenko, E. (2022). Probabilistic atlas for the language
> network based on precision fMRI data from >800 individuals. *Scientific Data*, 9, 529.
> https://doi.org/10.1038/s41597-022-01645-3

The multiple-demand atlas is distributed by the same group in the online supplement to that paper.

## Participant notes

- **`sub-A30`** — the language localizer was acquired in one run only (a second run was never collected).
  A one-run map cannot support the cross-validated analyses this map set is intended for, and the map does not
  resemble the language network, so **no language map is published** for this participant and there is no
  language row in `atlas_correlations.tsv`. The multiple-demand map, estimated from two full runs, is published
  normally. `sub-A30` is excluded from both group maps.
- **`sub-A60`** — no functional localizer data were acquired; the source dataset contains only the anatomical
  scan. No maps and no rows.
- Participant identifiers are not contiguous. `sub-A03`, `sub-B02` and `sub-B04` do not exist in the source
  dataset, and identifiers were not renumbered to close the gaps.

## Provenance

These maps come from an existing analysis of the three studies the participants were originally scanned in, and
were re-identified to this dataset's participant labels. Software versions in `dataset_description.json` were
read from the archived first-level model files; the CONN version is not recorded for all participants.

## License

CC0, as for the source dataset. This does not extend to the atlases described above, which are not part of
this dataset.
