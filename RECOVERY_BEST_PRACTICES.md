# Audio M4A Recovery Best Practices / 音声M4A復旧ベストプラクティス

## Scope / 対象
- EN: Safe, repeatable recovery workflow for broken `.m4a` files.
- JA: 破損した `.m4a` を安全かつ再現可能に復旧するための標準手順。

## Privacy Rules for Public Sharing / 公開共有時の匿名化ルール
- EN: Never publish real user names, directory trees, mount points, device names, or original project labels.
- JA: 実名、実ディレクトリ構成、マウント名、端末名、案件名は公開しない。
- EN: Replace all sensitive strings with placeholders.
- JA: 機微情報は必ずプレースホルダに置換する。

Recommended placeholders / 推奨プレースホルダ:
- `<WORK_ROOT>` (example: `/path/to/audio-recovery`)
- `<GOOD_SAMPLE>.m4a` (known-good reference)
- `<BROKEN_FILE>.m4a`
- `<RUN_ID>` (timestamp like `YYYYMMDD_HHMMSS`)

## Core Findings Pattern / 復旧でよく効く観測パターン
- EN: If `ffprobe` shows `moov atom not found`, the file often has MP4/M4A container metadata loss.
- JA: `ffprobe` で `moov atom not found` が出る場合、コンテナメタデータ欠損が主因のことが多い。
- EN: Recovery success improves when a similar known-good recording is available (same app/device/settings).
- JA: 同一アプリ・端末・設定に近い正常参照ファイルがあると復旧率が上がる。

## Non-Negotiable Safety Rules / 必須安全ルール
1. EN: Never overwrite originals. JA: 元データを上書きしない。
2. EN: Create a working copy first. JA: 最初に作業コピーを作る。
3. EN: Save recovery outputs with new names. JA: 復旧出力は別名保存。
4. EN: Keep all logs and intermediate outputs. JA: ログと中間成果物を保持。

## Standard Workflow / 標準ワークフロー
1. EN: Create working copy (`_work_copy_<RUN_ID>`). JA: 作業コピー作成。
2. EN: Run diagnostics with `ffprobe`. JA: `ffprobe` で全件診断。
3. EN: Confirm known-good reference file metadata. JA: 正常参照ファイルの特性確認。
4. EN: Run `untrunc` (default and `-M`). JA: `untrunc` を通常と `-M` で実行。
5. EN: Validate decode at head and middle via `ffmpeg`. JA: 先頭・中盤を `ffmpeg` で検証。
6. EN: Select best output and write a report. JA: 最良出力を採用しレポート化。

## CLI-Agnostic Command Templates / CLI非依存コマンドテンプレート
- EN: Use these from any shell or any LLM CLI that can run terminal commands.
- JA: どのシェル／LLM CLIでも、端末コマンド実行機能があれば利用可能。

### 1) Working copy / 作業コピー
```bash
cd <WORK_ROOT>
RUN_ID=$(date +%Y%m%d_%H%M%S)
WORK_COPY="_work_copy_${RUN_ID}"
mkdir -p "$WORK_COPY"
rsync -a --exclude '_work_copy_*' ./ "$WORK_COPY"/
cd "$WORK_COPY"
```

### 2) Diagnose / 診断
```bash
for f in *.m4a; do
  echo "===== $f"
  ffprobe -v error -show_format -show_streams "$f" || true
done
```

### 3) untrunc with Docker / Dockerでuntrunc
```bash
# pull once
docker pull thej6s/untrunc

# default mode
docker run --rm --platform linux/amd64 -v "$PWD":/files thej6s/untrunc \
  -o /files/<BROKEN_FILE>_UNTRUNC.m4a \
  /files/<GOOD_SAMPLE>.m4a \
  /files/<BROKEN_FILE>.m4a

# search mode (-M)
docker run --rm --platform linux/amd64 -v "$PWD":/files thej6s/untrunc -M \
  -o /files/<BROKEN_FILE>_UNTRUNC_M.m4a \
  /files/<GOOD_SAMPLE>.m4a \
  /files/<BROKEN_FILE>.m4a
```

### 4) Validation / 検証
```bash
# start segment
ffmpeg -v error -t 30 -i '<OUTPUT>.m4a' -f null - && echo OK

# middle segment
ffmpeg -v error -ss 1800 -t 20 -i '<OUTPUT>.m4a' -f null - && echo OK

# metadata
ffprobe -v error -show_entries stream=codec_name,profile,sample_rate,channels:format=duration,size,bit_rate '<OUTPUT>.m4a'
```

## Best Output Criteria / 最良出力の採用基準
- EN: `ffprobe` readable, `ffmpeg` decode succeeds, non-trivial duration, plausible codec params.
- JA: `ffprobe` 可読、`ffmpeg` デコード成功、極端に短すぎない再生時間、妥当なコーデック設定。

## LLM Prompt Templates (CLI-Independent) / LLM指示テンプレート（CLI非依存）

### Prompt A: Main run / 本実行
```text
Recover all broken .m4a files in this directory.
Requirements:
1) Create _work_copy_<timestamp> first and operate only there.
2) Never overwrite originals.
3) Diagnose every file with ffprobe and save a summary.
4) Use a known-good reference file and run untrunc in both default and -M modes.
5) Save outputs as new files (e.g., *_RECOVERED_UNTRUNC.m4a).
6) Validate head and middle decode with ffmpeg.
7) Keep existing recovered files and all logs.
8) Produce a final report table: original, candidate outputs, duration, codec, selected best.
```

### Prompt B: Fallback run / 失敗時の追加指示
```text
If untrunc fails or output is tiny/invalid:
- Try a different known-good reference.
- Try using a previously recovered file as reference.
- Always keep logs per attempt.
- Reject 0-byte or unreadable outputs.
- Continue until all files are either partially recovered or clearly marked unrecoverable.
```

### Prompt C: NotebookLM upload split / NotebookLMアップロード用分割
```text
Split playable audio files for NotebookLM upload.
Requirements:
1) Never overwrite originals.
2) Output into notebooklm_splits_ready_<timestamp>/.
3) Keep each chunk under 200MB (target ~180MB for safety).
4) Add 10-15 seconds overlap between adjacent chunks (recommended 12s).
5) Skip unreadable files and record them in skipped_unreadable.tsv.
6) Use ffprobe for readability checks.
7) Prefer stream copy (-c copy) to avoid quality loss and speed up.
8) Write manifest.tsv with source, chunk, start_sec, duration_sec, bytes, path.
9) Verify that no chunk exceeds 200MB.
10) Print upload order list at the end.
```

## Public Repo Checklist / 公開前チェックリスト
- [ ] No personal names in filenames or examples / 実名を含まない
- [ ] No real absolute paths / 実絶対パスを含まない
- [ ] No host/device identifiers / ホスト名・端末識別子なし
- [ ] Commands use placeholders / コマンドはプレースホルダ化
- [ ] Include both English and Japanese / 英日併記

## Notes / 注意
- EN: Long reported duration does not guarantee perfect audio quality; always listen.
- JA: duration が長くても音質保証にはならないため、必ず試聴確認する。
