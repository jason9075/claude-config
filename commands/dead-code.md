---
description: 執行 Dead code elimination，僅限於 unreferenced code，請勿更動文件化註解（Documentation comments）
argument-hint: [target-file-or-dir]
---

你是一位嚴謹的重構工程師。請對 `$ARGUMENTS`（若未指定，則為當前工作目錄）執行 **dead code elimination**。

## 核心約束（不可違反）

### ✅ 可以刪除的 dead code

- 未被任何地方 import 或 require 的模組 / 函式 / 類別 / 變數
- 宣告後從未被呼叫或參考的函式
- 宣告後從未被讀取或賦值的變數
- 未被任何 selector 套用的 CSS class / ID / rule（靜態分析範圍內確認無使用）
- 條件永遠不成立的 dead branch（如 `if (false) { ... }`）
- 被 comment-out 掉的程式碼區塊（`// ...` 或 `/* ... */` 包覆的舊程式碼，**非**文件化註解）

### 🚫 絕對禁止刪除

- **Documentation comments**（JSDoc `/** ... */`、Rustdoc `///`、Python docstring `"""..."""` 等）
- 任何解釋數學原理、公式推導、演算法邏輯的註解，無論格式為何
- 包含 `@param`、`@returns`、`@example`、`@see`、`LaTeX`、數學符號（`∑`, `∂`, `∫`, `∇` 等）的註解區塊
- `// TODO`、`// FIXME`、`// HACK`、`// NOTE` 類的標記註解
- 任何包含數學公式（如 `f(x) =`、`O(n log n)`、矩陣、向量表示）的段落
- 被 `@preserve` 或 `/* ! ... */` 標記的程式碼

**判斷原則：當你不確定某段程式碼或註解是否屬於 dead code 時，保留它，並在回報中標注為「疑似 dead code，保留待確認」。**

## 執行步驟

### Step 1：掃描與分析

1. 讀取目標範圍內的所有相關檔案（`.js`, `.ts`, `.css`, `.scss`, `.py`, `.go`, `.rs` 等）
2. 建立 reference graph：哪些 symbols 被哪些地方使用
3. 識別所有 unreferenced symbols

### Step 2：逐一確認，不批量刪除

對每一個候選 dead code：
- 確認它在整個 codebase 內**真的沒有被參考**（包含動態呼叫、字串拼接呼叫、HTML 中的 class 名稱等）
- 確認它**不是** documentation comment 或數學說明

### Step 3：執行刪除

- 只刪除 Step 2 確認的項目
- 每次刪除後驗證語法完整性

### Step 4：回報結果

以清單格式回報：

**已刪除：**
- `path/to/file.js:42` — 函式 `unusedHelper()` 從未被呼叫
- `path/to/style.css:88` — `.old-button` class 從未被使用

**保留（疑似 dead code，待確認）：**
- `path/to/file.js:100` — `dynamicFn` 可能透過字串動態呼叫

**未發現可刪除的 dead code。**（若無任何項目，直接回報此訊息，不進行任何其他修改）

---

## 重要提醒

若掃描後**未發現任何可刪除的 dead code**，請直接回報「未發現可刪除的 dead code」，然後停止。不要為了「有所作為」而刪除或修改任何其他內容，包括但不限於：

- 數學公式或原理的說明註解
- 格式化空白或縮排
- 仍在使用中但看起來「不整潔」的程式碼
