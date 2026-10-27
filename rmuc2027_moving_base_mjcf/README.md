# RMUC2027 完整場地：藍方基地可動標靶

入口是本目錄的 `model.xml`。這是一份**完整場地**，包含可左右平移的藍方基地
綠燈、上方裝甲與連接支架。下方斜裝甲、橫向導軌及紅方基地保持固定。
可用於在同一 MuJoCo 模擬中拍攝移動標靶；此模型本身不含飛鏢、相機或飛行控制。

## 取得與載入

下載本目錄的全部檔案（包含 `assets/` 和所有引用的 XML），保持目錄結構即可。
不依賴父倉庫、原始 CAD、場地生成器、無人機程式或其他模型目錄。

已驗證環境為 Python 3.12、MuJoCo 3.12.0：

```python
import mujoco

model = mujoco.MjModel.from_xml_path("rmuc2027_moving_base_mjcf/model.xml")
data = mujoco.MjData(model)
```

約 86 MB 的 XML 與網格是可攜式輸入；不附 `.mjb` 編譯快取。
完整碰撞模型首次載入通常需數分鐘，具體取決於電腦。
這個入口與 `rmuc2027_battlefield_mjcf/model.xml` 是二選一的場地；
組合自己的場景時不要同時加入兩份，否則會重複場地與碰撞。

## 標靶接口

| 對象 | MuJoCo 名稱／含義 |
| --- | --- |
| 可動剛體 | `blue_rail_carriage` |
| 滑動關節 | `blue_base_slide` |
| 位置致動器 | `blue_base_drive`，`ctrl` 單位是公尺 |
| 綠燈視覺 geom | `visual_116` |
| 綠燈碰撞 geom | `visual_116_detail_0` |
| 導軌支承 | `blue_rail_support`，固定 |

場地保持來源座標：公尺、Z 向上；滑台沿世界 **−Y** 移動，中央位置為 `q=0`。
`q>0` 是示範正面相機的畫面右方；其他相機方向不一定相同。
滑台行程約 `[-0.3005, +0.3005]` m，正式值請讀取關節的 `jnt_range`。
這是依導軌約 0.84 m 和整組滑台約 0.239 m 包絡推導的行程，尚未以實物限位校正。

載入 XML 不會自動隨機移動。最小控制方式是：

```python
drive = model.actuator("blue_base_drive").id
data.ctrl[drive] = 0.10  # 中央向世界 -Y 偏移 10 cm 的位置目標
mujoco.mj_step(model, data)
```

需要限速、隨機目標、停止及到位判斷時，使用 `shiuhou/rm27_sim` 中的
`rm27_sim/simulation/base_rail_control.py`。該單檔僅依賴 NumPy，可複製進自己的工程；
同倉庫 README 的「鏢組交付與接入」提供最小安裝、鍵盤示範和模擬迴圈範例。
控制器必須寫入鏢組自身模擬的同一個 `MjData`；另一個示範視窗的狀態不會自動同步。

合入其他動態物體後，不要假設關節或致動器是索引 0。依名稱查找，並透過
`jnt_qposadr`、`jnt_dofadr` 取得狀態地址；保留自己物體的相機、致動器與控制迴圈。

## 幾何、碰撞與來源

本場景由 `rmuc2027_battlefield_mjcf` 的 `4017a9e` 版本轉換。
`source_provenance.json` 保留上游來源；`assembly_manifest.json` 記錄來源雜湊、
選取的連通元件、行程推導與輸出雜湊。轉換程式位於獨立的 `rm27_sim` 倉庫：

```bash
python -m rm27_sim.integration.base_rail --output /new/output/directory --no-compile
```

轉換只拆分原本按顏色合併的視覺網格並重新掛接碰撞。中央位置的視覺三角形、
碰撞定義與世界位置保留；5 個視覺 geom、265 個碰撞 geom 隨上方整組平移。
原場地及保留機庫合計 41,389 個可碰撞 geom。
只排除滑台與固定導軌支承之間的內部接觸，其他物體仍可接觸綠燈、裝甲及支承。

質量 1 kg、慣量、驅動增益及 100 N 推力上限均為展示估計。
碰撞繼承來源的近似表面殼層與 2 ms 步長；高速、小尺寸物體需要另行驗證，
這不是連續碰撞或零穿透保證，也未驗證任何視覺／飛行／尾翼控制算法。
上游資產權利歸原作者，保留倉庫原有權利說明。
