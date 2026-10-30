# Lab1 Local Verification

Verification date: 2026-10-29. The baseline is course repository commit `ead5920dd0561d2beaf9ca2c15a899b337d825d1`. At that time the files were downloaded and merged locally; no GitHub submission or push was made in that verification step.

## Original files and course rules

- All 625 files in the downloaded copy passed their original SHA256 checks. The 641 files recorded in the earlier upload candidate retained their original contents.
- The merged copy changed only four existing files: repository `README.md`, `Lab1/Readme.md`, `Lab1/rinex2csv.m` and `Lab1/tools/check_submission.py`. No original repository files were deleted.
- The course report content, page limits, AI rules, submission locations, CSV columns, ranking requirements, grading, policies and timetable were retained.
- The two PDFs, report template, license, MatRTKLIB, workflow figure, Lab2 and other weekly materials were retained. Original course converter and checker copies are in `legacy/`.

## Recorded test results

| Check | Result |
| --- | --- |
| Historical example | 186/186 epochs; ECEF matched the saved result, maximum difference 0 m |
| Receiver-reference difference | 2D RMS 37.9107584162 m; 3D RMS 51.0428443069 m. This is not accuracy against independent ground truth. |
| Included GPS cache and Python | 1437/1534 epochs, 93.6766623207% coverage; 97 epochs omitted |
| MATLAB conversion in merged folder | The 10 outputs generated from this package's OBS/NAV matched the verified cache byte for byte |
| Regenerated Python solution | 1437/1534 epochs; CSV format and actual OBS time checks passed |
| Original single-file checker | `python tools/check_submission.py filename.csv` remained supported |
| Time checks | `--week`, `--epochs` and `--epoch-file` matched the README usage |
| Numerical regression | Synthetic GPS position and clock errors below 1 mm; historical results unchanged |
| Missing epoch | Omitted with a reason, not filled with the previous position |
| Invalid CSV | Duplicate times, NaN, wrong column count or units, empty data and unsorted times rejected |
| Online format comparison | 22 representative valid and invalid inputs agreed with the server parser |

The checker also retains course filename and Earth-surface range checks. Representative tests do not prove full equivalence; the online system decides acceptance, time matching and grades. With `--epoch-file`, coverage refers to OBS times; a format pass is not an accuracy assessment.

## Commands

From `Lab1`:

```powershell
python -m pip install -r requirements.txt
python WLSE_spp.py --reference dataset/NAV-HPPOSECEF.csv
python WLSE_spp.py --mode gps --input-dir benchmark/preprocessed_gps --output-dir outputs/benchmark --submission G05_benchmark.csv
python tools/check_submission.py outputs/benchmark/G05_benchmark.csv --week 2158 --epoch-file benchmark/preprocessed_gps/epochs.csv
python tools/verify_package.py
```

Set MATLAB's current directory to `Lab1`:

```matlab
rinex2csv('benchmark/UrbanNav-HK-Deep-Urban-1.ublox.f9p.obs', ...
          'benchmark/UrbanNav_20210521_multiGNSS_clean.nav', ...
          'outputs/benchmark_regenerated_inputs');
```

Then run:

```powershell
python WLSE_spp.py --mode gps --input-dir outputs/benchmark_regenerated_inputs --output-dir outputs/benchmark_regenerated --submission G05_benchmark.csv
python tools/check_submission.py outputs/benchmark_regenerated/G05_benchmark.csv --week 2158 --epoch-file outputs/benchmark_regenerated_inputs/epochs.csv
```

## Scope

The recorded environment was Windows x64, Python 3.10.20, NumPy 2.2.6 and MATLAB R2023a. The solver only requires NumPy; the receiver converter is optional.

The GPS baseline does not solve multiple constellations. The teaching team's UrbanNav scoring reference is not included, so a benchmark rank cannot be calculated offline. The historical CSV and new GPS conversion use different inputs and filters; do not confuse the 113/186 regenerated example with the historical 186-epoch regression.

Field collection, RTKCONV GUI buttons, campus network access, Google Drive sync and online scoring were not tested. Generated files in `outputs/` are excluded by `.gitignore` and are not student submissions.
