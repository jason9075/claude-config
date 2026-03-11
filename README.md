# Claude Config

個人 Claude Code CLI 配置檔。

## 安裝

若已安裝 Claude Code（`~/.claude` 已存在）：

```bash
cd ~/.claude
git init
git remote add origin git@github.com:jason9075/claude-config.git
git fetch origin
git checkout -f main
```

若為全新安裝：

```bash
git clone git@github.com:jason9075/claude-config.git ~/.claude
```

## 環境切換

`CLAUDE.md` 是 symlink，指向對應系統的設定檔：

```bash
# NixOS
ln -sf CLAUDE.nixos.md CLAUDE.md

# macOS
ln -sf CLAUDE.mac.md CLAUDE.md
```

## 內容

- `CLAUDE.md` - 全域指令設定（symlink）
- `CLAUDE.nixos.md` - NixOS 專用設定
- `CLAUDE.mac.md` - macOS 專用設定
- `settings.json` - Claude Code 設定
- `keybindings.json` - 快捷鍵設定
- `skills/` - 自訂 skills

## Skills

### english-tutor

英文輔助模式，在對話中提供英文建議。

**啟用方式：** 在 Claude Code 中輸入 `/english-tutor`

**功能：**
- 中文輸入時，提供工程師風格的英文表達建議
- 英文輸入有錯誤時，提供修正與更自然的說法
- 英文正確時不會干擾對話

## License

MIT
