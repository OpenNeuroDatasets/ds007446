# Localizer preprocessing and first-level models

Scripts used to preprocess and model the two localizer tasks (`AliceLocalizer`, `MDLocalizer`) with the EvLab
module (`el`) of the CONN toolbox. Requires SPM12 and CONN (tested with release 22.v2407).

- `run_el_preprocessing.m` — `el preprocessing` with the `DefaultMNI_PlusStructural` pipeline: realignment,
  outlier detection (ART), direct normalization of the functional data to MNI space, 4 mm FWHM smoothing.
- `run_el_model.m` — `el model` for each task with el's default first-level options: canonical HRF plus
  temporal derivative, motion and outlier regressors, 128 s high-pass filter, AR(1).

## Usage

Fill in the paths to CONN, the subjects directory, the paras directory and a subject list (a `.txt` file with
one subject folder name per line, e.g. `sub-A01`), then run `run_el_preprocessing.m` and `run_el_model.m`. Results are written to
`<subjects directory>/<subject>/DefaultMNI_PlusStructural/results/firstlevel/<task>/`.

## Inputs

**Subjects directory** — one folder per subject (e.g. `sub-B10/`) containing:

`data.cfg`, listing all of the subject's functional runs (unzipped `.nii`), the structural image and the RT.
Runs from both sessions go in the same file.

```
#functionals
/path/to/sub-B10_ses-01_task-MDLocalizer_run-01_bold.nii
/path/to/sub-B10_ses-01_task-MDLocalizer_run-02_bold.nii
/path/to/sub-B10_ses-02_task-AliceLocalizer_run-01_bold.nii
/path/to/sub-B10_ses-02_task-AliceLocalizer_run-02_bold.nii
#structurals
/path/to/sub-B10_ses-01_T1w.nii
#RT
1.4
```

`AliceLocalizer.cat` and `MDLocalizer.cat`, giving the runs of each task (numbered by their order in
`data.cfg`) and their `.para` files:

```
#runs
3 4
#files
sub-B10_ses-02_task-AliceLocalizer_run-01_events.para
sub-B10_ses-02_task-AliceLocalizer_run-02_events.para
```

**Paras directory** — one `.para` file per run, made from the run's `*_events.tsv` (onset in seconds and
condition number, the position of the condition in `#names`):

```
#onsets
14.516 2
32.533 1
...
#durations
18 18
#names
degraded intact
#units secs
```

and the contrast files `AliceLocalizer.con`:

```
intact intact 1
degraded degraded 1
intact-degraded intact 1 degraded -1
degraded-intact degraded 1 intact -1
```

and `MDLocalizer.con`:

```
hard hard 1
easy easy 1
hard-easy hard 1 easy -1
easy-hard easy 1 hard -1
```

`intact-degraded` and `hard-easy` are the localizer contrasts for the language and multiple-demand networks.

## Notes

- `sub-A30` has one run of the language localizer; `sub-A60` has no functional data.
