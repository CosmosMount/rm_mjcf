# rm_mjcf

`unified_control` 使用的 MuJoCo MJCF 运行时资产集合。仓库只保留各入口
MJCF 实际引用的 mesh、纹理和必要的来源说明，不包含转换脚本、预览图或未引用的
源网格。

## 模型入口

| 模型 | 入口 | 来源 |
| --- | --- | --- |
| RM26 PNX WBR | `rm26_pnx_wbr_mjcf/mjmodel.xml` | `fudan_rl/assets/rm26_pnx_wbr_mjcf` |
| RMUC 2026 赛场 | `rmuc2026_battlefield_mjcf/model.xml` | `CosmosMount/rmuc2026_battlefield_mjcf` commit `67c13270e213cf3f69da2619e349eed273432330` |
| 过洞步兵（FGOW） | `infantry_mjcf/model.xml` | 本地 `infantry_mjcf` 运行时模型 |
| RM27 新场地 | `rm27_battlefield_mjcf/model.xml` | 本地更新的 `rmuc2026_battlefield_mjcf` 场地模型 |
| RM27 機庫 + 基地 | `rm27_drone_mjcf/hanger_base_mjcf/model.xml` | 本地 RM27 dock/base 組合場景 |
| RM27 飛鏢區 + 基地 | `rm27_dart_mjcf/dart_base_mjcf/model.xml` | 本地飛鏢發射區、基地與停機庫組合場景 |
| RM27 無人機 | `rm27_drone_mjcf/{visual,dynamic,dock}/model.xml` | 本地 RM27 visual/dynamic/dock 資產 |
| RM27 飛鏢 | `rm27_dart_mjcf/model.xml` | 本地 `research/Darts` CAD 網格與 FPV 相機 |

请将本仓库与 `unified_control` 放在同一父目录：

```text
workspace/
├── rm_mjcf/
└── unified_control/
```

模型中的资产路径均为相对路径，移动单个模型时请保留其目录结构。

## 权利说明

各模型和资产的权利归其原作者所有。上游未附带许可证的模型不因此获得新的
许可；使用或再分发前请自行确认授权。WBR 的详细来源和校验值见
`rm26_pnx_wbr_mjcf/README.md` 与 `UPSTREAM.json`。
