# Audio M4A Recovery Best Practices

Public, privacy-safe guide for recovering broken `.m4a` files with a reproducible workflow.

## What this repo contains
- `RECOVERY_BEST_PRACTICES.md`: full bilingual (EN/JA) best-practice guide
- `prompts/`: reusable LLM prompt templates (CLI-agnostic)
- `prompts/llm_notebooklm_split_prompt_ja.txt`: NotebookLM upload split prompt (<=200MB + overlap)

## Key principles
1. Never overwrite originals.
2. Always create a timestamped working copy first.
3. Diagnose with `ffprobe` before recovery.
4. Use a known-good reference file with `untrunc` (default and `-M`).
5. Validate outputs by decoding both head and middle segments.
6. Keep all logs and intermediate artifacts.

## Quick start
See [`RECOVERY_BEST_PRACTICES.md`](./RECOVERY_BEST_PRACTICES.md).

## Privacy policy for sharing
- No real names, no private paths, no host/device identifiers.
- Use placeholders such as `<WORK_ROOT>`, `<GOOD_SAMPLE>.m4a`, `<BROKEN_FILE>.m4a`.

## License
MIT
