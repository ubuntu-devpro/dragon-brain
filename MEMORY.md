# MEMORY.md — 龍哥 long-term memory

## 關於強哥（Johnny Cheng）

- **名稱：** 強哥
- **用戶名：** chengyuchi0813
- **時區：** Asia/Taipei（GMT+8）
- **語言：** 中文（繁體）

## 系統環境

- **主機名：** devpro-VMware-Virtual-Platform（VM）
- **作業系統：** Ubuntu 24.04.4 LTS
- **Git 版本：** 2.43.0
- **GitHub CLI：** v2.63.2（已登入 account: ubuntu-devpro）
- **Claude Code：** v2.1.92（已安裝）
- **Python：** 3.12.x（位於 `/usr/bin/python3`）

## Claude-Workspace 專案

- **路徑：** `/home/devpro/Claude-Workspace`
- **用途：** 強哥的多 AI 共享工作區規範
- **GitHub：** https://github.com/jc9527/Claude-Workspace
- **電腦命名：** `Johnny-MBP-M2`（Mac）、`devpro-VMware-Virtual-Platform`（VM）

### 重要技術細節

- OpenClaw exec 會阻擋「直譯器 + 相對路徑」的呼叫
- 執行 Python script 需用絕對路徑：`/usr/bin/python3 /path/to/script.py`
- Menu 腳本需設定：`CLAUDE_WORKSPACE_MACHINE=Johnny-MBP-M2`

## 待完成事項

- [ ] 協助強哥設定 VM 的電腦目錄（目前 machines/ 只有 Johnny-MBP-M2）
- [ ] 確認 OpenClaw 的 Python exec 安全設定（嚴格模式）

## Session 記錄

### 2026-04-04/05（第一次對話）

1. 互相認識：強哥是台灣人，用中文溝通
2. 設定身份：龍哥 🐉
3. 安裝 GitHub CLI 並登入
4. Clone `jc9527/Claude-Workspace` repo
5. 學習並內化 Claude-Workspace 規範到 AGENTS.md
6. 解決 Python exec 被阻擋的問題（用絕對路徑繞過）
