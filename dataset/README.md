# Historical 186-Epoch Example

These inputs come from the downloaded `dataset/` and accompany the teaching example. The original UBX is in the parent folder. The five measurement CSV files use one column per epoch; each epoch occupies three consecutive columns in the satellite positions file.

| Content | File |
| --- | --- |
| Raw observations and navigation | `COM6___9600_250916_020025_blockz.obs` and the matching `.nav` |
| Measurement inputs | `pseudoranges_meas.csv`, `satellite_positions.csv`, `satellite_clock_bias.csv`, `ionospheric_delay.csv`, `tropospheric_delay.csv` |
| Observation times | `epochs.csv`, extracted from the matching OBS |
| Receiver reference | `NAV-HPPOSECEF.csv`; not measurement ground truth |
| Historical results | `reference_results/estimated_positions.csv`, `positioning_errors.csv` |
| Other original results | Matching `.pos` and `.kml` files |

The default historical mode reads these inputs and writes separately to `outputs/example/`. The original CSV files have no satellite IDs. Interpret the historical reproduction and the newly generated GPS baseline separately.
