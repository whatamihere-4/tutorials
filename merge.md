# How to Merge Split Video Files

1. Put **only** the `PART` files in one folder - nothing else.
2. Install [ffmpeg](https://ffmpeg.org/download.html) if needed (`winget install ffmpeg` / `brew install ffmpeg` / `apt install ffmpeg`).
3. Open a terminal in that folder.
4. Copy the command below, paste into the terminal, press Enter.

### Mac / Linux

```bash
ls -1v *.PART* | sed "s/^/file '/;s/$/'/" > list.txt && \
ffmpeg -y -f concat -safe 0 -i list.txt -c copy \
"$(grep PART1 list.txt | sed "s/file '//;s/'$//;s/.PART1././")"
```

### Windows (PowerShell)

```powershell
gci *PART* | sort Name | % { "file '$($_.Name)'" } | sc list.txt && `
ffmpeg -y -f concat -safe 0 -i list.txt -c copy `
((Select-String PART1 list.txt).Line -replace "file '|'|\.PART1\.",'')
```

The merged file lands in the same folder, named from `PART1` (e.g. `HolidayClip.PART1.mp4` → `HolidayClip.mp4`).

The command builds a small `list.txt` from whatever `PART` files are in the folder, then ffmpeg stitches them together. Works for any number of parts.

**Old byte chunks** (`movie.mp4.part001`, …): `cat movie.mp4.part* > movie.mp4`

**ffmpeg not found?** Install it and open a new terminal.
