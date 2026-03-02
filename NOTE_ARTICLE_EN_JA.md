# Broken M4A Recovery Playbook (EN/JA)

## EN
I organized a privacy-safe, reproducible recovery workflow for broken `.m4a` files.

Highlights:
- Original files are never overwritten.
- Recovery is done only inside a timestamped working copy.
- Diagnostics first (`ffprobe`), then recovery (`untrunc`), then validation (`ffmpeg`).
- Works with any LLM CLI that can run terminal commands.

Repository and files:
- README: https://github.com/mio-github/audio-m4a-recovery-guide/blob/main/README.md
- Full guide: https://github.com/mio-github/audio-m4a-recovery-guide/blob/main/RECOVERY_BEST_PRACTICES.md
- Main prompt: https://github.com/mio-github/audio-m4a-recovery-guide/blob/main/prompts/llm_recovery_main_prompt.txt
- Fallback prompt: https://github.com/mio-github/audio-m4a-recovery-guide/blob/main/prompts/llm_recovery_fallback_prompt.txt

If you handle media recovery in teams, this helps standardize quality and reduce accidental data loss.

## JA
破損した `.m4a` の復旧手順を、匿名化・再現性重視で整理しました。

ポイント:
- 元ファイルは絶対に上書きしない
- タイムスタンプ付き作業コピー内だけで復旧
- `ffprobe` で診断 → `untrunc` で復旧 → `ffmpeg` で検証
- 端末コマンド実行ができるLLM CLIなら流用可能

リンク:
- README: https://github.com/mio-github/audio-m4a-recovery-guide/blob/main/README.md
- 詳細ガイド: https://github.com/mio-github/audio-m4a-recovery-guide/blob/main/RECOVERY_BEST_PRACTICES.md
- メインプロンプト: https://github.com/mio-github/audio-m4a-recovery-guide/blob/main/prompts/llm_recovery_main_prompt.txt
- 失敗時プロンプト: https://github.com/mio-github/audio-m4a-recovery-guide/blob/main/prompts/llm_recovery_fallback_prompt.txt

チームで復旧品質を揃えたいときに使える内容です。
