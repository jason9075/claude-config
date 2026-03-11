# Claude Config

個人 Claude Code CLI 配置檔。

## 安裝

```bash
git clone git@github.com:jason9075/claude-config.git ~/.claude
```

## 內容

- `CLAUDE.md` - 全域指令設定
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
