# Broken M4A Recovery Playbook (EN/JA)

## EN
I organized a privacy-safe, reproducible recovery workflow for broken `.m4a` files.

Highlights:
- Original files are never overwritten.
- Recovery is done only inside a timestamped working copy.
- Diagnostics first (`ffprobe`), then recovery (`untrunc`), then validation (`ffmpeg`).
- Works with any LLM CLI that can run terminal commands.

Repository and files:
- README: <REPO_URL>/blob/main/README.md
- Full guide: <REPO_URL>/blob/main/RECOVERY_BEST_PRACTICES.md
- Main prompt: <REPO_URL>/blob/main/prompts/llm_recovery_main_prompt.txt
- Fallback prompt: <REPO_URL>/blob/main/prompts/llm_recovery_fallback_prompt.txt

If you handle media recovery in teams, this helps standardize quality and reduce accidental data loss.

## JA
破損した `.m4a` の復旧手順を、匿名化・再現性重視で整理しました。

ポイント:
- 元ファイルは絶対に上書きしない
- タイムスタンプ付き作業コピー内だけで復旧
- `ffprobe` で診断 → `untrunc` で復旧 → `ffmpeg` で検証
- 端末コマンド実行ができるLLM CLIなら流用可能

リンク:
- README: <REPO_URL>/blob/main/README.md
- 詳細ガイド: <REPO_URL>/blob/main/RECOVERY_BEST_PRACTICES.md
- メインプロンプト: <REPO_URL>/blob/main/prompts/llm_recovery_main_prompt.txt
- 失敗時プロンプト: <REPO_URL>/blob/main/prompts/llm_recovery_fallback_prompt.txt

チームで復旧品質を揃えたいときに使える内容です。
