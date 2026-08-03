# stripaud

Interactive audio track manager for video files. Keep what you want, strip what you don't.

## Usage

```bash
./stripaud video.mp4                  # single file
./stripaud /path/to/videos            # search a directory, then pick file(s)
./stripaud /path/to/*prefix*          # glob — batches every match, no picker
./stripaud /path/to/videos -a         # batch a whole directory, no picker
./stripaud /path/to/*.mkv -p          # force the picker on an explicit list
./stripaud . --dry-run                # walk the flow, change nothing
```

**Batch vs. pick.** Whenever the args match more than one file, stripaud forks
first:

```
? Found 24 media files. What do you want to do?
  » Process all 24 files — ask me about each one
    Pick specific file(s) from a list
```

"Process all" skips the file picker entirely and goes straight to track
questions, file by file — no TAB-marking 24 entries. "Pick specific" opens the
fuzzy picker, which is what you want when you're after just the 13th one.

Which option starts highlighted depends on how you invoked it: naming files
yourself (a glob, or several paths) defaults to *all*, pointing at a directory
defaults to *pick*. It's only a default. `-a`/`--all` and `-p`/`--pick` skip the
fork question altogether.

`stripaud` is a self-contained Python script run via [uv](https://docs.astral.sh/uv/)
inline dependencies — no manual `pip install` needed. The original Bash version is
kept as `stripaud.sh` for fallback.

## How it works

All questions are asked up front; nothing is encoded until you've answered for
every file. Answer, walk away, come back to a finished batch.

1. **Pick files** — gathers media files from the dir/glob args (directories are
   searched recursively), then forks: take them all, or open an fzf-style fuzzy
   picker (TAB marks multiple files, ENTER confirms).
2. **Pick tracks, per file** — a checkbox menu for audio and one for subtitles
   (SPACE toggles, ENTER confirms); all tracks start checked. This repeats for
   each file, back to back, with no processing in between.
3. **Confirm the plan** — a one-line-per-file recap of what will be kept and
   dropped, then a single yes/no for the whole batch.
4. **Strip** — removes the unselected audio/subtitle streams with ffmpeg (stream
   copy, no re-encode) and overwrites the originals, one file after another.

## Flags

- `-n`, `--dry-run` — walk the entire flow but take no action: prints the exact
  ffmpeg command instead of running it, and never touches your files.
- `-a`, `--all` — work on every match, skipping the file picker.
- `-p`, `--pick` — always show the file picker, even for explicitly named files.
- `-y`, `--yes` — skip the batch confirmation prompt.

## Requirements

- ffmpeg / ffprobe
- uv (resolves `questionary` + `rich` automatically on first run)
- fzf (optional; without it, the file picker falls back to a checkbox list)

## Notes

- Skips files where all tracks are kept (no-op) or none are selected
- Preserves video, attachments, and other data streams
- Uses stream copy (fast, no re-encoding)
- Multi-select lets you batch several files in one run
- Cancelling a file's track menu (ESC) skips just that file; the rest of the
  batch is unaffected
