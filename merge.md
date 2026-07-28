# How to Merge Split Video Files

##Notice
Due to a bug I didn't catch in my program until about 40% of the way through my Filester migration (~2TB of data, alphabetically from 18VR to VirtualPapi videos) there will be a slight jump in the merged video where the splice occurred. There is not much I can do about this for the time being. I will go back and reupload proper versions of most/all of the videos when the migration is finished, however it will take a while due to having to reprocess hundreds of videos I have already moved. 

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
