# 資料合約

本資料合約定義茶葉採收預測 AI 系統的標準輸入資料與模型輸出格式。

## 影像資料

- 圖片來源：30×30 公分木框照片。
- 圖片格式：建議使用 JPG 或 PNG。
- 圖片命名：建議包含日期、地點、品種與流水號，但模型訓練只依 metadata 欄位讀取。
- 拍攝限制：照片應完整包含木框內茶樹冠層，並盡量避免強烈陰影、過曝、模糊與非茶樹背景占比過高。

## 標註資料

標註資料建議以 UTF-8 CSV 儲存，欄位如下：

| 欄位 | 型別 | 必填 | 說明 |
| --- | --- | --- | --- |
| image_id | string | yes | 圖片唯一識別碼 |
| image_path | string | yes | 圖片相對路徑或 URI |
| location | string | yes | 種植地點 |
| cultivar | string | yes | 茶樹品種 |
| captured_at | date | yes | 拍攝日期，ISO 8601 格式 |
| bud_1_leaf_count | integer | yes | 一葉期芽數 |
| bud_2_leaf_count | integer | yes | 二葉期芽數 |
| bud_3_leaf_count | integer | yes | 三葉期芽數 |
| bud_4_leaf_count | integer | yes | 四葉期芽數 |
| bud_5_leaf_count | integer | yes | 五葉期芽數 |
| bud_6_leaf_count | integer | yes | 六葉期芽數 |
| bud_7_leaf_count | integer | yes | 七葉期芽數 |
| banjhi_bud_count | integer | yes | 對口芽數 |
| total_bud_count | integer | yes | 總芽數 |
| average_leaf_stage | float | yes | 平均葉期 |
| split | string | yes | train、val 或 test |

## 資料品質規則

- 所有芽數欄位必須大於或等於 0。
- `total_bud_count` 應等於一葉期到七葉期芽數與對口芽數的合理彙總；若現場定義不同，必須在資料說明中註記。
- `average_leaf_stage` 必須在合理範圍內，建議為 1.0 到 7.0。
- 同一張照片不可同時出現在 train、val、test。
- 若資料量足夠，test set 應包含不同地點、品種與日期組合，以評估泛化能力。

## 模型輸出

```json
{
  "image_id": "example_001",
  "predictions": {
    "bud_1_leaf_count": 12.3,
    "bud_2_leaf_count": 18.7,
    "bud_3_leaf_count": 9.5,
    "bud_4_leaf_count": 3.1,
    "bud_5_leaf_count": 0.8,
    "bud_6_leaf_count": 0.2,
    "bud_7_leaf_count": 0.0,
    "banjhi_bud_count": 4.4,
    "total_bud_count": 49.0,
    "average_leaf_stage": 2.4
  },
  "confidence": {
    "method": "model_uncertainty_or_calibration",
    "score": 0.82
  },
  "artifacts": {
    "heatmap_url": "/artifacts/example_001_heatmap.png"
  }
}
```
