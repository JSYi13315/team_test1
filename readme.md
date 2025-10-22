# 🛡️ 嚴謹的 Git 開發工作流程 (短命期個人功能分支 + Rebase)

本流程旨在保持 dev 主線分支絕對的線性與乾淨，並確保個人開發的程式碼安全。

## I. 專案與環境準備

1.  **首次克隆（Clone）：**
    複製遠端倉庫`(github repo)`，並指定以 `dev` 為本地預設工作分支。

    ```sh
    git clone -b dev <repo-url> [本地資料夾名稱]
    ```

## II. 功能開發啟動 (最嚴謹的同步與切換)

2.  **強制同步 dev (最嚴謹)：**
    確保本地 dev 與遠端 origin/dev 完全一致，**強制清除**所有本地髒數據，預防汙染。

    ```sh
    # 1. 抓取遠端最新數據，更新本地追蹤指標 (origin/dev)
    git fetch origin

    # 2. 切換到 dev 分支
    git switch dev

    # 3. 強制重設本地 dev 分支到遠端追蹤分支的最新位置
    git reset --hard origin/dev
    ```

3.  **建立並切換新個人功能分支 (單一功能原則)：**
    從最新、最乾淨的 dev 上**建立**一個專門用於此功能的個人分支。

    ```sh
    # 推薦命名格式：user/<your-username>/<feature-name>
    git switch -c user/john/feature-login
    ```

## III. 持續開發與備份

4.  **開發與提交 (Commit)：**
    在您的個人功能分支上進行程式碼修改，並經常性地提交。

    ```sh
    git add .
    git commit -m "feat: [Module] Implemented feature details"
    ```

5.  **首次遠端備份 (設定追蹤)：**
    第一次推送時，使用 -u 參數建立追蹤關係。

    ```sh
    git push -u origin user/john/feature-login
    ```

6.  **日常備份：**
    後續只需使用 push 將本地提交備份到遠端。

    ```sh
    git push
    ```

## IV. 準備合併：清理歷史 (Rebase)

在發起 Pull Request (PR) 前，執行 Rebase 保持 dev 歷史線性。

7.  **更新基底狀態：**
    抓取遠端 origin/dev 的最新提交，作為 Rebase 的新基底。

    ```sh
    git fetch origin
    ```

8.  **執行 Rebase (變基)：**
    將您的功能提交，重新套用在最新的 origin/dev 之上。

    ```sh
    git rebase origin/dev
    ```

9.  **處理衝突 (Conflict Resolution)：**
    如果發生衝突，需手動解決並繼續衍合。

      * a. 手動編輯所有衝突檔案。
      * b. 標記已解決：`git add .`
      * c. 繼續衍合：`git rebase --continue`
      * *💡 快速取消：`git rebase --abort`*

10. **本地驗證：**
    Rebase 成功後**務必**運行專案，確認功能不受影響且無新 Bug。

## V. Pull Request (PR) 提交流程

11. **安全強制推送：**
    由於 Rebase 改變了提交歷史，必須使用 **安全強制推送** 來覆蓋遠端分支。

    * **情況 A (分支已存在於遠端)：** 使用安全強制推送覆蓋歷史。
      ```sh
      git push --force-with-lease
      ```
    * **情況 B (分支首次推送)：** 強制推送並設定遠端追蹤關係。
      ```sh
      git push -u --force-with-lease origin user/john/feature-login
      ```

12. **發起 PR：**
    到遠端平台`(github)`發起 PR，確保目標合併分支`(base)`為 `dev`，來源分支`(compare)`為`您的個人功能分支`。

### ⏸️ 流程交接：發起 PR 後立即繼續開發

* **PR 已送出：** 您的個人分支 (`user/<your-username>/<feature-name>`) 現在進入等待審核狀態。

* **下一輪工作 (持續開發)：** 為了避免等待閒置，您應**立即開始下一項工作**：
    * **操作：** 保持在您當前的個人分支 (`user/<your-username>/<feature-name>`) 上。新工作的 `git add` / `git commit` / `git push` 指令**照常執行**。
    * *❗ 重要提醒：* 您的新提交會**自動更新**目前正在審核的 PR 內容。審查者將看到所有新舊提交。請確保新提交與正在審核的內容**邏輯獨立**，以避免混淆審核者。

* **管理員操作：** 審查者通過審核後，會將您的 PR 合併到 `dev` 主線。
* **過渡到清理：** 請在確認 PR 已合併後，再執行後續的 **VI. PR 完成與工作區清理** 步驟。

## VI. PR 完成與工作區清理 (嚴謹同步)

13. **PR 合併後同步主線 (最嚴謹)**：
    PR 合併完成後，將本地 dev **強制重設**到最新的遠端狀態。

    ```sh
    # 1. 抓取遠端最新狀態 (更新 origin/dev 指標)
    git fetch origin

    # 2. 切換到 dev 分支
    git switch dev

    # 3. 強制重設 (確保與遠端完全一致)
    git reset --hard origin/dev
    ```

14. **清理分支 (單一功能原則)：**
    刪除已完成的本地功能分支。

    ```sh
    git branch -d user/john/feature-login
    ```

15. **下一輪開發：**
    回到 **II. 功能開發啟動 (步驟 2)**，**重新建立一個新的個人功能分支**，重複流程。

---

## 🚨 誤操作應急處理 (本地工作區被污染)

如果您在 dev 或其他不該有提交的分支上進行了**有價值的**修改或提交：

1.  **【挽救】備份工作到一個新分支：**

    ```sh
    git branch feature/temp-rescue
    # 將所有當前工作保存到一個臨時分支。
    ```

2.  **【清理】恢復被污染的分支到乾淨狀態：**

    ```sh
    git switch dev
    git fetch origin
    git reset --hard origin/dev
    # 徹底清理 dev 上的所有錯誤提交和未提交變更。
    ```

3.  **【繼續】切換到救援分支繼續開發：**

    ```sh
    git switch feature/temp-rescue
    # 從這裡開始，將此分支當作正常的功能分支繼續開發。
    ```