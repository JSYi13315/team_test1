## I. 專案初始化 (初次設定或重開專案)

如果您是第一次開始開發，或者需要重新建立本地工作區，請執行以下指令：

```sh
# 複製遠端儲存庫，並指定以 dev 分支為本地預設分支
git clone -b dev <repo-url> [本地端資料夾名稱]
```

## II. 標準開發流程

1. **切換到功能分支：**

   - 確認目前在 `dev` 分支上。

   - 同步 遠端(origin/dev) 跟 本地(dev)

     ```
     git switch dev
     git pull origin dev
     ```

   - **建立並切換**到新的開發分支 (例如：`feature/xxx`):

     ```
     git switch -c feature/xxx
     ```

2. **進行開發與提交：**

   - 進行程式碼修改，並**經常性地提交 (Commit)**，確保工作不遺失。

   - **重要原則：** 在發起 PR 前，請確保所有修改都已在本地提交。

## III. PR 準備與清理歷史 (使用 Rebase)

Rebase 用於將您的分支歷史乾淨地嫁接到最新的 `dev` 分支上，避免不必要的合併提交 (Merge Commits)。

3. **抓取遠端最新狀態：**

   - 這是關鍵步驟，用來更新本地的遠端追蹤分支 (`origin/dev`)。

     ```
     git fetch origin
     ```

4. **執行 Rebase：**

   - 確保您在 `feature/xxx` 分支上 (`git switch feature/xxx`)。

   - 將 `feature/xxx` 的提交歷史，重新套用在最新的 `origin/dev` 之上：

     ```
     git rebase origin/dev
     ```

5. **處理衝突 (Conflict Resolution)：**

   - 如果 Git 提示衝突，請依循以下步驟：

     - **a. 手動解決衝突：** 編輯所有衝突檔案，手動移除 Git 留下的衝突標記 (`<<<<<<<`, `=======`, `>>>>>>>`)。

     - **b. 標記為已解決 (不需要 Commit)：**

       ```
       git add .
       ```

     - **c. 繼續衍合：**

       ```
       git rebase --continue
       ```

     - **💡 快速取消：** 如果衝突無法解決，可執行 `git rebase --abort` 取消操作。

## IV. PR 提交流程

6. **本地功能驗證：**

   - Rebase 成功後，**務必在本地運行專案**，確認功能不受影響且無新 Bug。

7. **推送到遠端：**

   - 由於 Rebase 導致提交歷史改變，必須使用 **安全強制推送** 來覆蓋遠端分支。

     ```
     git push origin feature/xxx --force-with-lease
     ```

8. **發起 Pull Request：**

   - 到遠端平臺 (如 GitHub/GitLab) 發起 PR。

   - **再次確認：** Base (目標合併分支) 為 `dev`，Compare (來源分支) 為 `feature/xxx`。
