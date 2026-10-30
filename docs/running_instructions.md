# Lab1 Run Guide

## 1. Setup

Open PowerShell in the `Lab1` repository root. All paths below are relative to this directory.

```powershell
python --version
python -m pip install -r requirements.txt
```

NumPy is the only required third-party dependency for the numerical solver. Matplotlib and Cartopy are not required. If `python` is unavailable, try `py -3` or the full path to your Python executable.

## 2. Historical teaching example

```powershell
python WLSE_spp.py --reference dataset/NAV-HPPOSECEF.csv
python tools/check_submission.py outputs/example/G01_owndata.csv --epoch-file dataset/epochs.csv
```

| Output | Content |
| --- | --- |
| `outputs/example/G01_owndata.csv` | Five ECEF columns, with times on a 0.1-second grid |
| `outputs/example/solution_details.csv` | Positions, clock offsets and valid measurement counts at the original OBS time precision |
| `outputs/example/omitted_epochs.csv` | Omitted epochs and reasons; normally header only for this example |
| `outputs/example/summary.json` | Solution count and optional receiver-reference comparison |

Expected: 186/186 epochs; 2D RMS 37.9107584162 m and 3D RMS 51.0428443069 m relative to the receiver solution. That solution is not ground truth. Receiver coordinates are in centimetres and `iTOW` is in milliseconds; records are matched by time, not row number. This example is not your group's own collected data.

Reprocessing the example OBS/NAV with `rinex2csv.m` and `gps` mode solves 113/186 epochs and omits 73 under the GPS filters. The 2D/3D RMS differences are about 15.393/82.106 m against the receiver reference. Its inputs and model differ from the historical CSV workflow, so the historical values are not expected.

## 3. UrbanNav benchmark

Run the included GPS cache without MATLAB:

```powershell
python WLSE_spp.py --mode gps --input-dir benchmark/preprocessed_gps --output-dir outputs/benchmark --submission G01_benchmark.csv
python tools/check_submission.py outputs/benchmark/G01_benchmark.csv --week 2158 --epoch-file benchmark/preprocessed_gps/epochs.csv
```

The program reads measurements, satellite positions, corrections and OBS times. It does not require ground truth. `coverage_percent` is the proportion of OBS epochs solved, not positioning accuracy.

To regenerate the inputs, run in MATLAB from the repository root:

```matlab
rinex2csv('benchmark/UrbanNav-HK-Deep-Urban-1.ublox.f9p.obs', ...
          'benchmark/UrbanNav_20210521_multiGNSS_clean.nav', ...
          'outputs/benchmark_inputs');
```

The converter does not open a plot window. It uses MatRTKLIB native MEX files and does not need Mapping Toolbox plotting. Then run:

```powershell
python WLSE_spp.py --mode gps --input-dir outputs/benchmark_inputs --output-dir outputs/benchmark_regenerated --submission G01_benchmark.csv
python tools/check_submission.py outputs/benchmark_regenerated/G01_benchmark.csv --week 2158 --epoch-file outputs/benchmark_inputs/epochs.csv
```

The baseline uses GPS L1, a 15-degree elevation mask, a 15 dB-Hz signal threshold, broadcast ephemerides, modelled atmospheric corrections, TGD and Earth rotation correction. Failed epochs are omitted individually; previous positions are not copied. The converter targets this package's RINEX 3/GPS baseline; check other receivers and datasets separately.

## 4. Your own data

1. Record UBX with u-center and back up the raw file.
2. Convert UBX to matching OBS/NAV with RTKCONV. Record the software version and settings.
3. In MATLAB, convert your files:

```matlab
rinex2csv('mydata/my.obs','mydata/my.nav','outputs/own_inputs');
```

4. Solve in Python, replacing `G01` with your group number:

```powershell
python WLSE_spp.py --mode gps --input-dir outputs/own_inputs --output-dir outputs/own --submission G01_owndata.csv
python tools/check_submission.py outputs/own/G01_owndata.csv --epoch-file outputs/own_inputs/epochs.csv
```

5. Align times when comparing with RTKPOST and the receiver solution. Do not call any of these solutions ground truth without evidence.
6. Prepare figures, the report, code documentation and AI-use statement under the course rules.

## 5. Receiver reference from UBX

```powershell
python -m pip install -r requirements-optional.txt
python pyubx2_csv_converter_gui.py
```

Select the root `COM6___9600_250916_020025_blockz.ubx` and output to `outputs/receiver`. A local non-GUI conversion produced 186 `NAV-HPPOSECEF.csv` rows; the GUI buttons were not separately tested.

## 6. Troubleshooting

| Issue | Action |
| --- | --- |
| CSV missing | Check the working directory and `--input-dir`; do not mix in the older 4,548-epoch CSV files. |
| NumPy missing | Install `requirements.txt` with the same Python executable used to run the program. |
| MATLAB cannot find `gt` | Keep `matRTKLIB/` next to `rinex2csv.m`; the function adds its path. |
| MEX cannot load | Check the MATLAB and operating-system compatibility of the supplied binaries; see the toolbox README. |
| Fewer than four satellites | The epoch may be omitted; check the conversion summary and `omitted_epochs.csv`. Do not invent positions. |
| Benchmark format passes | The TA server still determines error and rank; the scoring ground truth is not included. |
| Output differs from the slides | Use `historical` mode with the included 186-epoch example; the new GPS baseline need not reproduce it. |

`legacy/` contains older scripts for comparison. They may use fixed paths, plotting dependencies or different data. Use the root entry points for normal operation.

## 7. Course repository and checks

The original grading, report length, AI use, submission and ranking rules remain in `Readme.md` and the course documents. The two PDFs, report template, MatRTKLIB and original workflow figures are retained. The MATLAB entry point takes three arguments: `rinex2csv(obs_file, nav_file, out_dir)`.

The standard-library CSV checker supports the original single-file call and `--week`, `--epochs` and `--epoch-file`. It also checks the course filename and Earth-surface bounds. Passing locally does not guarantee online acceptance. See [local verification](local_verification.md).
