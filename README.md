# stripaud

Interactive audio track manager for video files. Keep what you want, strip what you don't.

## Usage

```bash
./stripaud video.mp4                  # single file
./stripaud /path/to/videos            # search a directory, then pick file(s)
./stripaud /path/to/*prefix*          # glob (expanded by your shell)
./stripaud . --dry-run                # walk the flow, change nothing
```

`stripaud` is a self-contained Python script run via [uv](https://docs.astral.sh/uv/)
inline dependencies — no manual `pip install` needed. The original Bash version is
kept as `stripaud.sh` for fallback.

## How it works

1. **Pick files** — gathers media files from the dir/glob args (directories are
   searched recursively), then an fzf-style fuzzy picker. TAB marks multiple
   files, ENTER confirms. A single explicit file skips this step.
2. **Pick audio tracks** — a checkbox menu per file (SPACE toggles, ENTER
   confirms); all tracks start checked.
3. **Pick subtitle tracks** — same checkbox menu for subtitles (skipped if the
   file has none).
4. **Strip** — removes the unselected audio/subtitle streams with ffmpeg (stream
   copy, no re-encode) and overwrites the original.

## Flags

- `-n`, `--dry-run` — walk the entire flow but take no action: prints the exact
  ffmpeg command instead of running it, and never touches your files.

## Requirements

- ffmpeg / ffprobe
- uv (resolves `questionary` + `rich` automatically on first run)
- fzf (optional; without it, the file picker falls back to a checkbox list)

## Notes

- Skips files where all tracks are kept (no-op) or none are selected
- Preserves video, attachments, and other data streams
- Uses stream copy (fast, no re-encoding)
- Multi-select lets you batch several files in one run
