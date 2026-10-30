# Lab1 Running Instructions

## 1. Setup

Open a terminal in the downloaded `Lab1` directory. All paths below are relative to this directory.

```powershell
python --version
python -m pip install -r requirements.txt
```

NumPy is the only required third-party package for the numerical code. Matplotlib and Cartopy are optional. If `python` is unavailable, try `py -3` or use the full path to your Python executable.

## 2. Run the historical example

```powershell
python WLSE_spp.py --reference dataset/NAV-HPPOSECEF.csv
python tools/check_submission.py outputs/example/G01_owndata.csv --epoch-file dataset/epochs.csv
```

Outputs are saved in `outputs/example/`: the five-column submission CSV, `solution_details.csv`, `omitted_epochs.csv`, and `summary.json`. Times in the submission use a 0.1-second grid; solution details retain the original OBS time precision.

Expected: 186/186 epochs; 2D RMS 37.9107584162 m and 3D RMS 51.0428443069 m relative to the receiver solution. The receiver reference is not ground truth. Reference coordinates use centimetres and `iTOW` uses milliseconds; matching is by time, not row number. This example is not a group's own field data.

Re-converting the example OBS/NAV with the new converter and GPS mode gives 113/186 epochs; 73 are omitted by the current GPS filters. Its 2D/3D RMS differences against the receiver are about 15.393/82.106 m. Different inputs and models mean it need not reproduce the historical result.

## 3. Run the UrbanNav benchmark

The included GPS inputs allow a first run without MATLAB:

```powershell
python WLSE_spp.py --mode gps --input-dir benchmark/preprocessed_gps --output-dir outputs/benchmark --submission G01_benchmark.csv
python tools/check_submission.py outputs/benchmark/G01_benchmark.csv --week 2158 --epoch-file benchmark/preprocessed_gps/epochs.csv
```

The solver reads measurements, satellite positions, corrections and OBS times. Ground truth is not required. `coverage_percent` is solved epochs divided by OBS epochs; it is not positioning accuracy.

To regenerate the RINEX inputs, run in MATLAB with `Lab1` as the current directory:

```matlab
rinex2csv('benchmark/UrbanNav-HK-Deep-Urban-1.ublox.f9p.obs', ...
          'benchmark/UrbanNav_20210521_multiGNSS_clean.nav', ...
          'outputs/benchmark_inputs');
```

Then run:

```powershell
python WLSE_spp.py --mode gps --input-dir outputs/benchmark_inputs --output-dir outputs/benchmark_regenerated --submission G01_benchmark.csv
python tools/check_submission.py outputs/benchmark_regenerated/G01_benchmark.csv --week 2158 --epoch-file outputs/benchmark_inputs/epochs.csv
```

The converter uses MatRTKLIB MEX files and opens no plots; Mapping Toolbox is not needed for conversion. The baseline uses GPS L1, a 15-degree elevation mask, 15 dB-Hz minimum signal, broadcast ephemerides, atmospheric corrections, TGD and Earth rotation correction. Failed epochs are omitted rather than filled with the previous position. Check compatibility before using other receivers or data.

## 4. Process your own data

1. Record and back up the raw UBX file with u-center.
2. Convert UBX to matching OBS/NAV with RTKCONV; record its version and settings.
3. In MATLAB, pass your OBS, NAV and output folder to `rinex2csv`:

```matlab
rinex2csv('mydata/my.obs','mydata/my.nav','outputs/own_inputs');
```

4. Run Python, replacing `G01` with your group number:

```powershell
python WLSE_spp.py --mode gps --input-dir outputs/own_inputs --output-dir outputs/own --submission G01_owndata.csv
python tools/check_submission.py outputs/own/G01_owndata.csv --epoch-file outputs/own_inputs/epochs.csv
```

5. Align times and compare with RTKPOST and the receiver solution; none is automatically ground truth.
6. Prepare plots, report, code documentation and AI-use statement as required by the course.

## 5. Extract the receiver reference

```powershell
python -m pip install -r requirements-optional.txt
python pyubx2_csv_converter_gui.py
```

Select `COM6___9600_250916_020025_blockz.ubx` in the repository root and output to `outputs/receiver`. The converter was tested without its GUI and produced 186 reference rows; button interaction was not separately tested.

## 6. Troubleshooting

| Issue | Check |
| --- | --- |
| CSV not found | Check the working directory and `--input-dir`; do not mix in the old 4548-epoch CSVs. |
| NumPy missing | Run `-m pip install -r requirements.txt` with the same Python used for the program. |
| MATLAB cannot find `gt` | Keep `matRTKLIB/` beside `rinex2csv.m`; the function adds it to the path. |
| MEX cannot load | Check operating system and MATLAB compatibility with the toolbox binaries. |
| Fewer than four satellites | The epoch may be omitted; inspect the conversion summary and `omitted_epochs.csv`. Do not invent a position. |
| Benchmark format passes | Accuracy and ranking still require the teaching team's scoring system. |
| Results differ from slides | Use historical mode with the supplied 186-epoch inputs; the new GPS baseline has different inputs and models. |

`legacy/` contains earlier scripts for comparison; they may use fixed paths, plotting dependencies or different data. Use the root entry points for normal runs.

## 7. Course rules and checks

The course report, grading, AI-use, submission and ranking rules remain in `Readme.md` and the course materials. Use the three-argument `rinex2csv(obs_file, nav_file, out_dir)` function, not `run('rinex2csv.m')`. The checker supports a single filename and optional `--week`, `--epochs` and `--epoch-file` arguments. Passing the format check does not guarantee server acceptance or a score. See [local verification](local_verification.md).
