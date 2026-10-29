# UrbanNav 基准输入

本目录只有原始观测、清理后的广播导航文件和可复用的 GPS 预处理输入；没有教师评分真值。

| 文件 | 用途 |
| --- | --- |
| `UrbanNav-HK-Deep-Urban-1.ublox.f9p.obs` | 1534个观测历元，GPS周2158 |
| `UrbanNav_20210521_multiGNSS_clean.nav` | 对应日期的多星座广播导航文件 |
| `preprocessed_gps/` | 本包 MATLAB 函数实际生成的 GPS L1 输入，供 Python直接运行 |

快速运行见上一级 `Readme.md`。导航文件含多个星座，当前教学基线只选择GPS。
预处理会对失败历元保留NaN；Python省略这些历元，并在输出目录记录原因。
请勿把其他年份的示例 NAV 用到这份2021年 OBS。

来源和清理细节见 `../docs/资料来源.md`。
