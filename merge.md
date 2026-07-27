# How to Merge Split Video Files
## GUI Method
1. Install [MKVToolNix](https://www.bunkus.org/videotools/mkvtoolnix/downloads.html)
2. Drag your files into the input sources section and hit `Ok`
   - Make sure videos are in the correct order to be merged
3. Hit `Start multiplexing` and it should finish in a minute or so
   - This is just merging, not re-encoding, so it's mostly reliant on your disk speed

## Command Line Method
```
ffmpeg -f concat -safe 0 -i "concat:Part1.mkv|Part2.mkv|Part3.mkv" -c copy Final.mkv
```