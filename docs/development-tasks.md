# 茶葉採收預測 AI 系統：逐步開發任務

本文件將專案拆解為可逐步完成的 GitHub 任務。每個任務完成並通過驗收後，再進入下一個任務，以降低弱監督學習、資料工程、模型服務與部署整合的風險。

## 專案目標

建立一套以 30×30 公分木框照片為輸入的茶葉採收預測 AI 系統。系統不要求人工框選茶芽，而是使用照片層級文字標註進行弱監督學習，預測一葉期到七葉期芽數、對口芽數、總芽數與平均葉期，並提供 FastAPI 推論服務、Vue 網站與 Docker 部署。

## 原始資料欄位

每張照片至少需要下列標註欄位：

| 欄位 | 說明 |
| --- | --- |
| image_id | 圖片唯一識別碼 |
| image_path | 圖片路徑或物件儲存 URI |
| location | 種植地點 |
| cultivar | 茶樹品種 |
| captured_at | 拍攝日期 |
| bud_1_leaf_count | 一葉期芽數 |
| bud_2_leaf_count | 二葉期芽數 |
| bud_3_leaf_count | 三葉期芽數 |
| bud_4_leaf_count | 四葉期芽數 |
| bud_5_leaf_count | 五葉期芽數 |
| bud_6_leaf_count | 六葉期芽數 |
| bud_7_leaf_count | 七葉期芽數 |
| banjhi_bud_count | 對口芽數 |
| total_bud_count | 總芽數 |
| average_leaf_stage | 平均葉期 |
| split | train、val 或 test |

## 開發任務清單

### 1. 專案初始化與需求凍結

**目標**：建立共同的專案骨架、資料定義與工作流程。

**工作項目**：
- 建立 GitHub repository 目錄結構。
- 建立 README、資料合約、任務拆解與貢獻流程。
- 決定主要技術棧：Python、PyTorch、FastAPI、Vue、Docker Compose。
- 定義模型輸入、輸出與評估指標。

**驗收標準**：
- Repo 具有可追蹤的 backend、frontend、ml、data、docs、infra 目錄。
- 文件說明資料欄位、任務順序與完成定義。

### 2. 資料盤點與標註格式轉換

**目標**：將照片與文字標註轉為可訓練的標準資料集。

**工作項目**：
- 撰寫標註匯入腳本，支援 CSV 或 Excel。
- 檢查圖片存在、尺寸、重複檔案與缺漏欄位。
- 產生標準 metadata 檔案。
- 固定 train、val、test 切分，避免同一地點或同日照片洩漏到不同集合。

**驗收標準**：
- 可以輸出 `data/processed/metadata.csv`。
- 資料檢查報告列出樣本數、欄位缺漏、芽數分布與切分比例。

### 3. 弱監督學習基準模型

**目標**：先用照片層級標籤建立可重現的 baseline。

**工作項目**：
- 建立 PyTorch Dataset 與 DataLoader。
- 設計多任務模型：共享影像 backbone，輸出各葉期芽數、對口芽數、總芽數與平均葉期。
- 使用 count regression loss 與 average leaf stage regression loss。
- 加入資料增強，保留 30×30 公分木框語意。

**驗收標準**：
- 可執行單次訓練並產生 checkpoint。
- 訓練結果包含 MAE、RMSE、R² 或 Spearman correlation。

### 4. 模型驗證與誤差分析

**目標**：確認模型是否能泛化到不同地點、品種與拍攝日期。

**工作項目**：
- 建立 validation 與 test 評估腳本。
- 依地點、品種、拍攝月份、葉期分布分層分析誤差。
- 產生預測 vs. 標註圖表。
- 找出資料不足或標註不一致案例。

**驗收標準**：
- 測試集報告包含整體與分層指標。
- 針對弱點提出下一輪資料補強或模型改善建議。

### 5. 可解釋性與弱定位分析

**目標**：在不框選茶芽的前提下，提供模型關注區域佐證。

**工作項目**：
- 加入 Grad-CAM、attention map 或 heatmap。
- 建立推論時可輸出的視覺化結果。
- 檢查模型是否關注茶芽與木框內區域，而不是背景或標籤紙。

**驗收標準**：
- 對任一測試圖片可輸出 heatmap。
- 文件記錄可解釋性限制，避免把 heatmap 誤解成精準框選。

### 6. FastAPI 推論服務

**目標**：將訓練好的模型包成 API。

**工作項目**：
- 建立 `/health`、`/predict`、`/models/current` API。
- 支援上傳照片並回傳各葉期芽數、對口芽數、總芽數、平均葉期與可信度資訊。
- 加入輸入驗證、錯誤處理與推論日誌。
- 撰寫 API 測試。

**驗收標準**：
- `pytest` 通過 API 單元測試。
- Swagger 文件可直接測試上傳圖片推論。

### 7. Vue 前端網站

**目標**：提供使用者上傳照片、查看預測與下載結果的介面。

**工作項目**：
- 建立 Vue 專案與頁面路由。
- 建立圖片上傳元件與結果表格。
- 顯示總芽數、各葉期芽數、對口芽數、平均葉期與 heatmap。
- 支援 CSV 匯出。

**驗收標準**：
- 使用者可透過瀏覽器完成一次照片推論。
- 前端測試與格式檢查通過。

### 8. Docker 與部署

**目標**：讓模型服務與網站能以容器化方式啟動。

**工作項目**：
- 建立 backend Dockerfile、frontend Dockerfile 與 docker-compose。
- 設定模型權重掛載目錄。
- 設定環境變數與基本健康檢查。
- 撰寫部署說明。

**驗收標準**：
- `docker compose up` 可啟動 API 與網站。
- README 說明本機開發與正式部署流程。

### 9. MLOps 與持續改善

**目標**：建立可持續迭代的模型更新流程。

**工作項目**：
- 設定實驗追蹤與模型版本。
- 加入資料版本管理策略。
- 建立 GitHub Actions：測試、lint、Docker build。
- 定義模型上線門檻與回滾策略。

**驗收標準**：
- 每次模型更新都有資料版本、程式版本、模型版本與測試報告。
- CI 能阻擋未通過測試的變更。

## 建議 GitHub Milestones

1. **M0 — Project Foundation**：完成任務 1。
2. **M1 — Data Pipeline**：完成任務 2。
3. **M2 — Weakly Supervised Model**：完成任務 3 到 5。
4. **M3 — Product API and Web**：完成任務 6 到 7。
5. **M4 — Deployment and MLOps**：完成任務 8 到 9。

## 開發節奏

- 每個任務以一個 issue 或 pull request 管理。
- 每個 pull request 必須包含測試方式、影響範圍與後續任務。
- 若前一任務未通過驗收，不進入下一任務。
