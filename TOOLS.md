# TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to your setup.

## What Goes Here

Things like:

- Camera names and locations
- SSH hosts and aliases
- Preferred voices for TTS
- Speaker/room names
- Device nicknames
- Anything environment-specific

## Examples

```markdown
### Cameras

- living-room → Main area, 180° wide angle
- front-door → Entrance, motion-triggered

### SSH

- home-server → 192.168.1.100, user: admin
- DEVPRO-SERVER → 192.168.0.240, user: devpro, Windows Server 10, password: ab123456..!QAZ

### TTS

- Preferred voice: "Nova" (warm, slightly British)
- Default speaker: Kitchen HomePod
```

## Why Separate?

Skills are shared. Your setup is yours. Keeping them apart means you can update skills without losing your notes, and share skills without leaking your infrastructure.

---

## Sub-agent Timeout 設定

| 任務類型 | 建議逾時 |
|----------|----------|
| 一般訊息回覆 | 60s |
| 開發任務（複雜） | 360s（6分鐘） |
| 簡單 Script | 120s |

**派工時使用 `timeoutSeconds` 參數指定**

---

Add whatever helps you do your job. This is your cheat sheet.
---

## 開發伺服器

### Win11Pro (Windows 開發機)
- **IP**: 100.116.136.66
- **User**: devpro
- **Password**: ab1234..
- **Hostname**: Win11Pro
- **用途**: Windows 環境開發、編譯、測試
- **已安裝**: Claude Code v2.1.92, Python 3.13, Git

### DevPro-Server (Windows Server)
- **IP**: 192.168.0.240
- **User**: devpro
- **Password**: ab123456..!QAZ
- **Hostname**: DevPro-Server
- **用途**: IIS 網站代管
- **已安裝**: 多個 IIS Sites (參見 IIS 設定)
