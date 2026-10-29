# 186历元历史示例

这些输入取自下载副本的 `dataset/`，与课件示例配套。原始UBX位于上一层目录。
五个测量CSV以“每列一个历元”为布局，卫星坐标每个历元占连续三列；不是每行一个历元。

| 内容 | 文件 |
| --- | --- |
| 原始观测与导航 | `COM6___9600_250916_020025_blockz.obs`、同名 `.nav` |
| 测量输入 | `pseudoranges_meas.csv`、`satellite_positions.csv`、`satellite_clock_bias.csv`、`ionospheric_delay.csv`、`tropospheric_delay.csv` |
| 真实观测时间 | `epochs.csv`，从配套OBS提取 |
| 接收机参考 | `NAV-HPPOSECEF.csv`，不是测量真值 |
| 历史结果 | `reference_results/estimated_positions.csv`、`positioning_errors.csv` |
| 其他原示例结果 | 同名 `.pos`、`.kml` |

默认历史模式只读取这里的配套输入，输出写入独立的 `outputs/example/`。
原CSV没有卫星标识，所以历史复现模式与新生成的纯GPS基线应分别解释。
