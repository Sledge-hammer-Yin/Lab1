# Historical Example: 186 Epochs

These inputs match the slides and come from the downloaded example dataset. The raw UBX file is in the repository root. Measurement CSVs use one column per epoch; satellite positions use three consecutive columns per epoch.

| Content | Files |
| --- | --- |
| Observations and navigation | `COM6___9600_250916_020025_blockz.obs` and matching `.nav` |
| Measurements | `pseudoranges_meas.csv`, `satellite_positions.csv`, `satellite_clock_bias.csv`, `ionospheric_delay.csv`, `tropospheric_delay.csv` |
| Observation times | `epochs.csv`, extracted from the matching OBS |
| Receiver reference | `NAV-HPPOSECEF.csv`; not ground truth |
| Historical results | `reference_results/estimated_positions.csv`, `positioning_errors.csv` |
| Other example results | Matching `.pos` and `.kml` |

Historical mode reads these inputs and writes to `outputs/example/`. The old CSVs have no satellite identifiers; interpret historical reproduction and the new GPS-only baseline separately.
