# GitHub Actions：Python Build and Test 作業

## 專案結構
```
ci-demo/
├── .github/workflows/python-ci.yml   # CI 工作流程
├── calculator.py                     # 被測試的程式
├── tests/test_calculator.py          # pytest 測試
├── pytest.ini
└── requirements.txt                  # pytest, ruff
```

## 操作步驟
1. GitHub 建立新 repository（例如 `ci-demo`）。
2. 把此資料夾推上去：
   ```bash
   git init && git add . && git commit -m "Add Python CI"
   git branch -M main
   git remote add origin https://github.com/<帳號>/ci-demo.git
   git push -u origin main
   ```
3. 到 repo 的 **Actions** 分頁，會看到 "Python CI" 自動執行（Python 3.10 / 3.11 / 3.12 三個 job）。
4. 截圖：①Actions 列表綠勾 ②點進 run 看 matrix 三個 job ③展開 "Test with pytest" 步驟 ④Artifacts 區的 pytest-results。
5. （加分）把 `add` 改成 `return a - b` 再 push，截圖紅叉，說明 CI 如何擋下錯誤。

## Workflow 逐段說明
| 區塊 | 作用 |
|---|---|
| `on: push / pull_request` | 推到 main 或開 PR 時觸發 |
| `strategy.matrix` | 同一份流程在多個 Python 版本各跑一次 |
| `actions/checkout` | 把程式碼拉到 runner |
| `actions/setup-python` + `cache: pip` | 安裝指定 Python 並快取 pip 套件加速 |
| `pip install -r requirements.txt` | 安裝相依套件 |
| `ruff check` | 程式碼風格 / 靜態檢查（官方教學用 Ruff 取代 flake8） |
| `pytest --junitxml` | 執行測試並輸出報告 |
| `upload-artifact` + `if: always()` | 即使測試失敗也保存報告 |

## 本機驗證結果
`ruff check` 通過；`pytest`：3 passed。
