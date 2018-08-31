# MiniDLNA for Stora/Media Formats

## Sony Equipment

The main HD video format supported over DLNA by Sony equipment (up until the 2010 models at least) seems to be AVCHD. This may also be true for other renderers built into other brands of TV (though I believe most others support HD MKVs, and possibly HD MP4s). HD MPEG 2 is also supported, but has enormous file sizes.

AVCHD is, believe it or not, the industry standard for distribution of HD media. It is used both on Blu-ray disks and in DVB-S/T/C 2 broadcasting. It is also used in video cameras.

The trouble with Sony devices (and more than likely those of other manufacturers) is that they are very picky about what they will play back, and even then your media needs to be very specifically encoded to allow seeking (i.e. FF/RW).

The supported format for AVCHD files on Sony TVs, Blu-ray and media players and the PS3 is...
- MPEG2 Transport stream or .m2ts - this can be created with a tool called tsMuxer
- H264 video (also called AVC) @ level 4.1 or 4, possibly lower. Blu-ray content is usually 4.1, HDTV is usually 4.0 (in the UK at least)
- AC3 audio, also known as Dolby digital. MP2 audio can be used if your video format is MPEG2 (MP2 is not MP3 mis-written) this is as used in SD broadcast TV.

## What's wrong with my media files?

So, having ripped your Blu-ray you find it has VC-1 video rather than AVC (all Warner Bros Blu-rays up until late 2011 seem to use VC-1), or you've sourced an MKV and it does not appear on your TV.

What can you do to play your nice content on your TV? The problem could be solved by one of the methods below...

- Remux them to .m2ts if they already have h.264 video and AC3 audio
- Convert the video to h.264 with Blu-ray like encoding parameters if it is either another format, or it does not seek properly.
- Convert the audio to AC3

## Tools needed
### Creating AVCHD m2ts container files (Remuxing)

Use [tsMuxeR](http://www.videohelp.com/tools/tsMuxeR) (multiplatform, GUI and command line) to put all the bits and pieces together in the m2ts package.

There's plenty of info on using this tool on various forums, but it's pretty easy to use.

### Generating Blu-ray quality h.264 video

The steps below use command line tools in linux to generate videos. The same tools are (I believe) available for Windows and Mac environments, but I do not know if the commands are executed in the same way. The tools I have used are:

- ffmpeg (default repo version in Ubuntu 11.10)
- mencoder (from medibuntu repos, version 2:1.0~rc4.dfsg1+svn33713-1+medibuntu2)
- x264 (built from source, my version is 3:0.119.2106+git07efeb4-1)

libx264 is the linchpin of the h.264 re-encodes below.
## Conversion commands

Similar commands to these can be found all over the Internet. I have tried many of these and failed to produce videos that are listed in the tree, or work correctly when rendered on my Sony TVs via DLNA. The more important aspects for matching Blu-ray encodes (which seem to produce the best results) are those regarding b frames, b pyramids, the keyint value (number of frames before a full frame is generated - highly correlated with seeking) and the vbv settings.

To alter the quality of the re-encode, and therefore usually the file size, you can change the "crf" value (15 offering the highest quality, with 22 being the default value, and above that giving a faster re-encode, but degraded quality).

With ffmpeg you can also change the "preset" value (ultrafast, superfast, veryfast, faster, fast, medium, slow, slower, veryslow), but the other parameters used in the commands may over-ride the presets. I have only tested slow and medium (the default) to work with Sony TVs.
### Convert VC1 (or in fact anything) to H264, Blu-Ray format

For a high quality VC1 Blu-ray re-encode...
```
ffmpeg -i filename.m2ts -acodec copy -vcodec libx264 -preset slow -level 4.1 -b-pyramid strict -crf 18 -x264opts keyint=24:slices=4:bframes=3:nal-hrd=vbr:aud=1:vbv-bufsize=30000:vbv-maxrate=40000 out/filename.m2ts
```
or for a lower quality mkv
```
ffmpeg -i filename.mkv -acodec copy -vcodec libx264 -preset medium -level 4.1 -b-pyramid strict -crf 22 -x264opts keyint=24:slices=4:bframes=3:nal-hrd=vbr:aud=1:vbv-bufsize=30000:vbv-maxrate=40000 out/filename.m2ts
```
### Convert other audio to AC3 audio

Necessary for some Blu-ray rips (where the main audio track may be some form of DTS or uncompressed PCM-WAV) to be played on Bravias as AVCHD requires AC3 audio
```
mencoder input.m2ts -ovc copy -channels 6 -oac lavc -lavcopts acodec=ac3:abitrate=640 -of rawaudio -o output.ac3
```
Remux this AC3 audio and the video from the original file into an m2ts using tsmuxer

I have found that when running this command with a MKV, I often get an overflow and the job fails. To work around this I had to convert the file to an m2ts with tsmuxer, then run the command on the m2ts file instead (another step to add!)

### Add subtitles to an MPEG

As Sony devices do not support subtitles via DLNA, use this re-encode to force subtitles into an mpeg2 DVD rip and convert it to h264. The command uses subtitle track 0 "-sid 0", changing the number will select a different subtitle track.

If your mpeg does not have the original DVD subs, or they are not very good (e.g. on some of my Chinese imports the subs are quite poor translations) you can remove "-sid 0" and use an external srt file e.g. "-sub Way_of_the_Dragon.srt"

I found if I also copied the audio (making it a one step process), mencoder crashed so I only output video here then remux the audio from the original mpeg and the video from this output file into an m2ts using tsmuxer.
```
mencoder Way_of_the_Dragon.mpg -sid 0 -nosound -vf harddup -ovc x264 -x264encopts crf=20:level=4.1:bframes=3:b-pyramid=strict:deblock:cabac:8x8dct:me=umh:subq=4:trellis=1:keyint=25:slices=4:nal-hrd=vbr:aud=1:vbv-bufsize=30000:vbv-maxrate=40000:threads=auto -of lavf -o Way_of_the_Dragon_avc.ts
```

### Convert SD files to AVCHD-SD

Yes, there really is a rather stupidly named format called AVCHD-SD (advanced video codec high definition - standard definition!!). I use it to convert my DVD rips to files that still work on my Sony TVs, but that use the better compression offered by h.264 video encoding). On average this makes the file size about 1/2 to 2/3 of the original size. Some of my files have ended up almost as large or even slightly larger than the originals, I simply kept the originals in this case.
```
 ffmpeg -i filename.mpg -acodec copy -vcodec libx264 -preset slow -level 4.1 -b-pyramid strict -crf 18 -x264opts \
 keyint=24:slices=4:bframes=3:nal-hrd=vbr:aud=1:vbv-bufsize=30000:vbv-maxrate=40000 filename.m2ts
```
### Convert both video and audio

I used this on some very old Doctor Who episodes in avi format to good effect. The crf value is high, but the source is in black and white and is pretty low quality. Set it lower to improve the re-encode. I'm unsure if ffmpeg is a good tool for doing multi-channel audio, some wide reading suggests this is handled better by mencoder, but for my 2 channel audio (which is probably the same on both channels on the Doctor Who episodes) ffmpeg was fine...
```
ffmpeg -i infile.avi -acodec ac3 -ac 2 -ab 128k -vcodec libx264 -level 4.0 -b-pyramid strict -crf 24 -x264opts keyint=30:slices=4:bframes=3:nal-hrd=vbr:aud=1:vbv-bufsize=30000:vbv-maxrate=40000 outfile.m2ts
```

### Batch convert SD files to AVCHD-SD

As above, but use a 'for' loop to batch process all .mpg files in the current folder.
```
for i in $(ls *.mpg|cut -d. -f1) ; do ffmpeg -i $i.mpg -acodec copy -vcodec libx264 -preset slow -level 4.1 -b-pyramid strict -crf 18 -x264opts keyint=24:slices=4:bframes=3:nal-hrd=vbr:aud=1:vbv-bufsize=30000:vbv-maxrate=40000 $i.m2ts; done
```
or
```
for i in $(ls *.avi|cut -d. -f1) ; do  ffmpeg -i $i.avi -acodec ac3 -ac 2 -ab 128k -vcodec libx264 -level 4.0 -b-pyramid strict\
-x264opts keyint=30:slices=4:bframes=3:nal-hrd=vbr:aud=1:vbv-bufsize=30000:vbv-maxrate=40000 $i.m2ts; done
```
Make sure any files to be converted do not have extra '.' characters (e.g. way.of.the.dragon.mpg) or the loop will fail to cut the filename properly - it will cut at the first period (e.g. try to re-encode 'way.mpg')


If you want to get really funky with your loops, you can do something like the following that looks inside a bunch of folders and encodes them one by one, putting the re-encodes into the correct folders in the 'outlocation' folder ...
```
 for folder in $(ls); do cd $folder; mkdir ~/outlocation/$folder; for i in $(ls *.mpg|cut -d. -f1) ; do ffmpeg -i $i.mpg -acodec copy -vcodec libx264 -preset slow \
 -level 4.1 -b-pyramid strict -crf 22 -x264opts keyint=24:slices=4:bframes=3:nal-hrd=vbr:aud=1:vbv-bufsize=30000:vbv-maxrate=40000 ~/outlocation/$folder/$i.m2ts; done; cd ../; done
```
