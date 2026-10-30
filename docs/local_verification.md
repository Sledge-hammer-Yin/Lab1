# Lab1 Local Verification

The merged package was checked on 2026-10-29 against course repository commit `ead5920dd0561d2beaf9ca2c15a899b337d825d1`. The original downloaded files and earlier staging copy were preserved. Course report, grading, AI-use, submission, CSV and ranking rules were retained; original course scripts remain in `legacy/`.

| Check | Result |
| --- | --- |
| Historical example | 186/186 epochs; ECEF positions match the saved reference, maximum difference 0 m. |
| Receiver-reference differences | 2D RMS 37.9107584162 m; 3D RMS 51.0428443069 m. This is not independent ground-truth accuracy. |
| Benchmark GPS inputs | 1437/1534 epochs, 93.6766623207% coverage; 97 omitted. |
| MATLAB conversion | Ten generated input files matched the verified cache byte for byte. |
| Regenerated-input Python solution | 1437/1534 epochs; CSV format and actual OBS-time checks passed. |
| Checker | Single-filename and optional `--week`, `--epochs`, `--epoch-file` calls passed. Duplicate, NaN, wrong-column, wrong-unit, empty and unsorted cases were rejected. |
| Synthetic GPS | Position and clock error below 1 mm; historical regression unchanged. |
| Missing epochs | Omitted with a reason; prior positions are not copied. |
| Server format comparison | 22 representative valid and invalid cases matched the examined server parser. |

These cases do not prove equivalence for every input. Final acceptance, time matching and scoring depend on the online platform. Coverage from `--epoch-file` uses the OBS timetable; format success does not establish accuracy.

## Commands

Run in `Lab1`:

```powershell
python -m pip install -r requirements.txt
python WLSE_spp.py --reference dataset/NAV-HPPOSECEF.csv
python WLSE_spp.py --mode gps --input-dir benchmark/preprocessed_gps --output-dir outputs/benchmark --submission G05_benchmark.csv
python tools/check_submission.py outputs/benchmark/G05_benchmark.csv --week 2158 --epoch-file benchmark/preprocessed_gps/epochs.csv
python tools/verify_package.py
```

To regenerate the benchmark inputs in MATLAB:

```matlab
rinex2csv('benchmark/UrbanNav-HK-Deep-Urban-1.ublox.f9p.obs', ...
          'benchmark/UrbanNav_20210521_multiGNSS_clean.nav', ...
          'outputs/benchmark_regenerated_inputs');
```

```powershell
python WLSE_spp.py --mode gps --input-dir outputs/benchmark_regenerated_inputs --output-dir outputs/benchmark_regenerated --submission G05_benchmark.csv
python tools/check_submission.py outputs/benchmark_regenerated/G05_benchmark.csv --week 2158 --epoch-file outputs/benchmark_regenerated_inputs/epochs.csv
```

The recorded environment was Windows x64, Python 3.10.20, NumPy 2.2.6 and MATLAB R2023a. The numerical Python code only requires NumPy. GPS mode does not implement multi-constellation positioning, and the package does not include the teaching scoring reference. Historical and newly converted GPS inputs differ; do not confuse the 113/186 new conversion with the 186-epoch historical regression. Field collection, RTKCONV GUI interaction, campus network access, Drive synchronization and online scoring were not tested. Generated `outputs/` are ignored and are not student submissions.
