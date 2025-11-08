# stripaud

Interactive audio track manager for video files. Keep what you want, strip what you don't.

## Usage

```bash
./stripaud.sh video.mp4                  # single file
./stripaud.sh /path/to/videos            # search directory (uses fzf if available)
./stripaud.sh *.mp4                      # batch process with glob
./stripaud.sh /path/to/*prefix*          # pattern matching
```

## How it works

1. Analyzes audio tracks in your video file
2. Shows interactive menu with track details
3. Select tracks to keep (up/down arrows, space to toggle)
4. Strips unwanted tracks using ffmpeg
5. Overwrites original file with cleaned version

## Requirements

- ffmpeg
- ffprobe
- fzf (optional, for better directory search)

## Notes

- Skips processing if all tracks selected (no-op)
- Preserves video, subtitles, and other streams
- Uses stream copy (fast, no re-encoding)
- Interactive menu handles Ctrl-C gracefully
