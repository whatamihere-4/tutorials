# How to Merge Split Video Files

1. Put **only** the `PART` files in one folder — nothing else.
2. Install [ffmpeg](https://ffmpeg.org/download.html) if needed (`winget install ffmpeg` / `brew install ffmpeg` / `apt install ffmpeg`).
3. Open a terminal in that folder.
4. Copy the command below, paste into the terminal, press Enter.

### Mac / Linux

```bash
ls -1v *.PART* | sed "s/^/file '/;s/$/'/" > list.txt && ffmpeg -y -f concat -safe 0 -i list.txt -c copy merged.mp4
```

### Windows (PowerShell)

```powershell
gci *PART* | sort Name | % { "file '$($_.Name)'" } | sc list.txt && ffmpeg -y -f concat -safe 0 -i list.txt -c copy merged.mp4
```

Your merged video is `merged.mp4` in the same folder. Rename it if you like.

The command builds a small `list.txt` from whatever `PART` files are in the folder, then ffmpeg stitches them together. Works for any number of parts.

**Old byte chunks** (`movie.mp4.part001`, …): `cat movie.mp4.part* > movie.mp4`

**ffmpeg not found?** Install it and open a new terminal.
