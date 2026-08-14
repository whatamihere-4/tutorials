# How to Merge Split Video Files

Large uploads are split into parts before they land in Filester. For most videos you will see playable parts named like `Video.PART1.mp4`, `Video.PART2.mp4`, and so on. Merging them back together is **stream copy** - ffmpeg just stitches the files; nothing gets re-encoded, so it is mostly limited by disk speed.

Install [ffmpeg](https://ffmpeg.org/download.html) if you do not already have it (`winget install ffmpeg` on Windows, `brew install ffmpeg` on Mac, `apt install ffmpeg` on Debian/Ubuntu).

## Quick method (paste one command)

1. Download every part into **one folder** (only the `PART` files - no extra copies).
2. Open a terminal in that folder:
   - **Windows:** Shift+right-click the folder → “Open PowerShell window here”, or `cd` into it.
   - **Mac/Linux:** `cd` into the folder in Terminal.
3. Paste the one-liner for your system and press Enter. The merged file appears next to the parts, named after `PART1` (e.g. `Video.PART1.mp4` → `Video.mp4`).

### Windows (PowerShell)

```powershell
$p1=(gci *.PART1.*|select -first 1).Name;$o=$p1-replace'\.PART1\.';$l=New-TemporaryFile;for($i=1;$i -le 20;$i++){if(Test-Path ($p1-replace'PART1',"PART$i")){Add-Content $l "file '$($p1-replace'PART1',"PART$i")'"}};ffmpeg -y -f concat -safe 0 -i $l -c copy -movflags +faststart $o;Remove-Item $l
```

### Mac / Linux

```bash
f=$(ls -1v *.PART1.* | head -1); o=${f/.PART1./.}; { i=1; while [ -f "${f/.PART1./.PART$i.}" ]; do echo "file '${f/.PART1./.PART$i.}'"; i=$((i+1)); done; } > .parts.txt; ffmpeg -y -f concat -safe 0 -i .parts.txt -c copy -movflags +faststart "$o" && rm -f .parts.txt
```

`-movflags +faststart` moves metadata to the front of the file so playback can start sooner - harmless to leave in.

## Manual method (step by step)

Use this if you want to see what is happening, have more than 20 parts, or need to fix the order by hand.

1. Put all parts in one folder. Names must stay in order: `PART1`, `PART2`, `PART3`, …
2. Create a text file listing each part (paths are relative to the folder you run ffmpeg from):

```text
file 'Video.PART1.mp4'
file 'Video.PART2.mp4'
file 'Video.PART3.mp4'
```

Save it as `.parts.txt` in the same folder.

3. Run ffmpeg:

```bash
ffmpeg -y -f concat -safe 0 -i .parts.txt -c copy -movflags +faststart Video.mp4
```

Replace `Video.mp4` with whatever output name you want. `-c copy` means no re-encode.

If every part shares the same base name and only the `PARTn` segment differs, you can build the list automatically:

```bash
f=$(ls -1v *.PART1.* | head -1)
o=${f/.PART1./.}
{ i=1; while [ -f "${f/.PART1./.PART$i.}" ]; do echo "file '${f/.PART1./.PART$i.}'"; i=$((i+1)); done; } > .parts.txt
ffmpeg -y -f concat -safe 0 -i .parts.txt -c copy -movflags +faststart "$o"
rm -f .parts.txt
```

## Older `bytes` splits (`.part001`, `.part002`, …)

Some uploads use raw byte chunks instead of playable parts:

```text
movie.mp4.part001
movie.mp4.part002
…
```

Those rejoin with plain concatenation - no ffmpeg needed:

```bash
cat movie.mp4.part* > movie.mp4
```

Order matters: `part001` must come first.

## Troubleshooting

| Problem | What to check |
|---------|----------------|
| Hitch or stutter at a join | Use **ffmpeg concat** (above). Do not use `mkvmerge` to merge these `PART` files - it can misalign timestamps even when frame counts look fine. |
| `PART1` not found | You need exactly one `*.PART1.*` file in the folder. Download any missing parts. |
| Wrong order | Parts must be `PART1`, `PART2`, … with no gaps. Renaming is fine as long as the number sequence is correct. |
| Output already exists | Delete or rename the old merged file, or pick a different output name in the manual command. |
| ffmpeg not found | Install ffmpeg and make sure it is on your `PATH` (restart the terminal after installing). |
