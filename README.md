# AIoT-DIC2-MultipleLR: 50 Startups Regression & Feature Selection Explorer

本專案依據作業要求與 [huanchen1107/AIoT-DIC2-MultipleLR](https://github.com/huanchen1107/AIoT-DIC2-MultipleLR) 範例架構進行完整實作，包含：
- **Part 1: CRISP-DM 標準流程** 求解 50 Startups 多元線性回歸問題
- **Part 2: 特徵選擇 (Forward Stepwise Selection) 與機器學習多模型比較 Workflow**
- **🎯 加總分 1 分要求**：提供**下拉式選單 (Dropdown)**，即時切換呈現兩種 Problem
- **🎯 加總分 2 分要求**：提供**任意輸入 Dataset URL** 功能 (例如 `https://raw.githubusercontent.com/Avik-Jain/100-Days-Of-ML-Code/master/datasets/50_Startups.csv`)

---

## 📁 檔案結構說明

| 檔案名稱 | 說明 |
| :--- | :--- |
| **`50Start_up.ipynb`** | **主要提交檔案**：包含完整 CRISP-DM 流程、`ipywidgets` 互動式下拉選單 (+1分)、自訂網址輸入 (+2分)、特徵選擇 Markdown 表格與雙子圖繪製。 |
| **`index.html`** | **GitHub Pages 靜態網頁**：下拉選單切換 Problem 1 / Problem 2（+1 分）、可任意輸入 Dataset URL 或上傳 CSV（+2 分）；所有結果由瀏覽器即時計算，不依賴後端、沒有寫死的數值。 |
| **`project_summary.md`** | 實驗結果數據比對與交叉驗證摘要。 |
| **`50_Startups.csv`** | 本地離線備份資料集 (在無網路或 SSL 憑證異常時自動備援)。 |
| **`requirements.txt`** | 專案相依 Python 套件清單。 |

---

## 🚀 執行方式

### 方式一：使用 Jupyter Notebook (`50Start_up.ipynb`)
1. 安裝相依套件：
   ```bash
   pip install -r requirements.txt
   ```
2. 啟動 Jupyter Notebook / VS Code / Google Colab：
   ```bash
   jupyter notebook 50Start_up.ipynb
   ```
3. 執行最上方互動式控制台單元格：
   - 下拉選單選擇 **Problem 1 (CRISP-DM)** 或 **Problem 2 (Feature Selection)**。
   - 輸入任意 CSV 網址（預設已帶入 `Avik-Jain` 資料集）。
   - 點擊 **「執行分析 (Run Analysis)」** 按鈕即可即時動態運算並輸出圖表。
4. Notebook 同時附帶靜態執行單元格，即便在純靜態 GitHub 預覽模式下也能完整檢視圖表輸出。

### 方式二：開啟網頁版 `index.html`（免安裝，亦可部署到 GitHub Pages）
- 直接在瀏覽器開啟 `index.html`（或部署到 GitHub Pages 後開網址）：用下拉選單切換題目，輸入任意 CSV 網址（或上傳檔案）後按「執行分析」。網址需允許 CORS（例如 raw.githubusercontent.com）。

---

## 📊 成果與作業圖片對應說明

### 1. Part 1 (CRISP-DM 六大階段)
1. **Business Understanding (商業理解)**: 透過研發支出、行政管理費、行銷費用與註冊州別，建立多元線性回歸模型預測創投企業之獲利 (Profit)。
2. **Data Understanding (資料理解)**: 使用 `requests` 自 GitHub 抓取 CSV 並驗證 HTTP Status Code 200，檢視資料維度 (50 rows, 5 cols) 與特徵分佈。
3. **Data Preparation (資料準備)**:
   - 區分特徵矩陣 $X$ 與目標變數 $y$。
   - 使用 `OneHotEncoder` 對 `State` 進行類別編碼，並去除首個 Dummy 欄位避免虛擬變數陷阱 (Dummy Variable Trap)。
   - 以 80/20 比例切割訓練集與測試集 (`random_state=0`)。
4. **Modeling (建模)**: 採用 `sklearn.linear_model.LinearRegression` 進行最小平方法 (OLS) 訓練。
5. **Evaluation (評估)**:
   - $R^2$ Score: `0.934707`
   - Mean Absolute Error (MAE): `7,514.29`
   - Mean Squared Error (MSE): `8.35e+07`
   - Root Mean Squared Error (RMSE): `9,137.99`
6. **Deployment & Visualization (可視化部署)**: 繪製 Actual vs. Predicted Profit 散佈圖與 $y=x$ 基準線。

---

### 2. Part 2 (Forward Feature Selection & Multi-Model Workflow)

#### 逐步向前特徵選擇結果 (對應作業圖片 Image 3):
| Number of Features | Selected Features | RMSE | R-squared | 說明 |
| :---: | :--- | :---: | :---: | :--- |
| **1** | `['R&D Spend']` | 8274.868018 | 0.946459 | 研發費用為單一關鍵特徵 |
| **2** | `['R&D Spend', 'Marketing Spend']` | **8198.797191** | **0.947439** | **★ RMSE 最低、R² 最高** |
| **3** | `['R&D Spend', 'Marketing Spend', 'State_New York']` | 8309.059683 | 0.946015 | 誤差回升 |
| **4** | `['R&D Spend', 'Marketing Spend', 'State_New York', 'State_Florida']` | 8409.916714 | 0.944697 | 誤差持續上升 |
| **5** | `['R&D Spend', 'Marketing Spend', 'State_New York', 'State_Florida', 'Administration']` | 9137.990153 | 0.934707 | 全特徵模型（與 Part 1 相同） |

> 註：數值取自作業參考截圖，並與 Colab 實跑結果交叉驗證（k=1、2 相同；k=4 的 RMSE 8409.916714 相同；k=5 等於 Part 1 全特徵模型 RMSE 9137.99）。截圖 k=5 標示的 `State_California` 與 `drop_first=True` 的做法不符，應為 `Administration`。Notebook 與網頁使用 `pd.get_dummies(drop_first=True)`（作業 workflow 寫法）即時計算；若不加 `drop_first`，3 個州別虛擬變數共線，k=5 會與 k=4 完全相同。

#### 雙子圖視覺化 (對應作業圖片 Image 1):
- **圖左 (RMSE by Number of Features)**: 在特徵數為 2 時達到谷底（約 8198.80）。
- **圖右 (R-squared by Number of Features)**: 在特徵數為 2 時達到最高峰（約 0.9474）。

#### 多模型回歸比較 Workflow (對應作業圖片 Image 2):
使用 R&D Spend 特徵擬合不同機器學習模型，依 MSE 由小到大排序：
1. **Random Forest**: $MSE = 1.795 \times 10^7, RMSE = 4236.76, R^2 = 0.9887$
2. **Gradient Boosting Regressor**: $MSE = 2.573 \times 10^7, RMSE = 5072.65, R^2 = 0.9838$
3. **SVR**: $MSE = 7.792 \times 10^7, RMSE = 8827.47, R^2 = 0.9511$
4. **Mutual Information / Linear Regression**: $MSE = 8.512 \times 10^7, RMSE = 9226.10, R^2 = 0.9465$
5. **SVM (Linear)**: $MSE = 8.569 \times 10^7, RMSE = 9257.09, R^2 = 0.9462$
6. **Ridge Regression**: $MSE = 8.570 \times 10^7, RMSE = 9257.45, R^2 = 0.9462$
7. **Elastic Net**: $MSE = 9.917 \times 10^7, RMSE = 9958.57, R^2 = 0.9377$
8. **LASSO**: $MSE = 1.010 \times 10^8, RMSE = 10051.96, R^2 = 0.9365$
