# How to Merge Split Video Files

1. Install [MKVToolNix](https://www.bunkus.org/videotools/mkvtoolnix/downloads.html)
2. Create a folder and put the individual video parts inside
3. Run the command for your system:

### Windows (PowerShell)
```powershell
$p1=(gci *.PART1.*|select -first 1).Name;$o=$p1-replace'\PART1\.';$t=1;$h=($o-replace'\..+$','')+'.merge_trim_frames';if(Test-Path $h){$t=[int](gc $h)};$x=@('-o',$o,$p1);for($i=2;$i -le 20;$i++){if(Test-Path ($p1-replace'PART1',"PART$i")){if($t-gt0){$x+=('--split','parts-frames:'+$t+'-')};$x+=('+'+($p1-replace'PART1',"PART$i"))}};mkvmerge @x
```

### Linux / Mac
```bash
f=$(ls -1v *.PART1.* | head -1); o=${f/.PART1./.}; t=1; [ -f "${o%.*}.merge_trim_frames" ] && t=$(tr -d '\r\n ' < "${o%.*}.merge_trim_frames"); mkvmerge -o "$o" "$f" $(i=2; while [ -f "${f/.PART1./.PART$i.}" ]; do [ "$t" -gt 0 ] && echo --split parts-frames:$t-; echo +${f/.PART1./.PART$i.}; i=$((i+1)); done)
```
