# Sandy — 茶葉採收預測 AI 系統

Sandy 是一個以 30×30 公分木框照片進行茶葉採收預測的 AI 專案。系統採用弱監督學習，使用照片層級文字標註訓練模型，不需要人工框選茶芽。

## 預測目標

模型需從單張照片預測：

- 一葉期到七葉期芽數
- 對口芽數
- 總芽數
- 平均葉期

## 技術架構

- **ML**：Python、PyTorch、弱監督多任務回歸模型
- **API**：FastAPI
- **Web**：Vue
- **Deployment**：Docker、Docker Compose
- **Docs**：資料合約、任務拆解、部署說明

## Repository 結構

```text
.
├── backend/          # FastAPI 推論服務
├── data/             # 原始、暫存與處理後資料；大型資料不應直接提交
├── docs/             # 需求、資料合約、開發任務與部署文件
├── frontend/         # Vue 前端網站
├── infra/            # Docker 與部署相關設定
└── ml/               # 模型訓練、驗證、測試與實驗設定
```

## 開發順序

完整任務拆解請見 [`docs/development-tasks.md`](docs/development-tasks.md)。專案採取逐步交付方式，每完成一個任務並通過驗收後，再進行下一個任務。

1. 專案初始化與需求凍結
2. 資料盤點與標註格式轉換
3. 弱監督學習基準模型
4. 模型驗證與誤差分析
5. 可解釋性與弱定位分析
6. FastAPI 推論服務
7. Vue 前端網站
8. Docker 與部署
9. MLOps 與持續改善

## 資料格式

標準資料欄位與品質規則請見 [`docs/data-contract.md`](docs/data-contract.md)。

## 目前狀態

目前已建立專案骨架與逐步開發任務，下一步是完成資料盤點與標註格式轉換。
