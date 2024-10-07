
# Blast from the past
[Challenge](https://play.picoctf.org/practice/challenge/432)

## Description

The judge for these pictures is a real fan of antiques. Can you age this photo to the specifications?Set the timestamps on this picture to `1970:01:01 00:00:00.001+00:00` with as much precision as possible for each timestamp. In this example, `+00:00` is a timezone adjustment. Any timezone is acceptable as long as the time is equivalent. As an example, this timestamp is acceptable as well: `1969:12:31 19:00:00.001-05:00`. For timestamps without a timezone adjustment, put them in GMT time (+00:00). The checker program provides the timestamp needed for each.

Use this [picture](https://artifacts.picoctf.net/c_mimas/91/original.jpg).

## Hints

Exiftool is really good at reading metadata, but you might want to use something else to modify it.

## Solution

把圖片下載下來後先看一下內容：

```bash
┌──(kali㉿kali)-[~/Downloads]
└─$ exiftool original.jpg
ExifTool Version Number         : 12.76
File Name                       : original.jpg
Directory                       : .
File Size                       : 2.9 MB
File Modification Date/Time     : 2024:03:24 23:18:59-04:00
File Access Date/Time           : 2024:03:24 23:19:18-04:00
File Inode Change Date/Time     : 2024:03:24 23:19:18-04:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
Exif Byte Order                 : Little-endian (Intel, II)
Image Description               : 
Make                            : samsung
Camera Model Name               : SM-A326U
Orientation                     : Rotate 90 CW
X Resolution                    : 72
Y Resolution                    : 72
Resolution Unit                 : inches
Software                        : MediaTek Camera Application
Modify Date                     : 2023:11:20 15:46:23
Y Cb Cr Positioning             : Co-sited
Exposure Time                   : 1/24
F Number                        : 1.8
Exposure Program                : Program AE
ISO                             : 500
Sensitivity Type                : Unknown
Recommended Exposure Index      : 0
Exif Version                    : 0220
Date/Time Original              : 2023:11:20 15:46:23
Create Date                     : 2023:11:20 15:46:23
Components Configuration        : Y, Cb, Cr, -
Shutter Speed Value             : 1/24
Aperture Value                  : 1.9
Brightness Value                : 3
Exposure Compensation           : 0
Max Aperture Value              : 1.8
Metering Mode                   : Center-weighted average
Light Source                    : Other
Flash                           : On, Fired
Focal Length                    : 4.6 mm
Sub Sec Time                    : 703
Sub Sec Time Original           : 703
Sub Sec Time Digitized          : 703
Flashpix Version                : 0100
Color Space                     : sRGB
Exif Image Width                : 4000
Exif Image Height               : 3000
Interoperability Index          : R98 - DCF basic file (sRGB)
Interoperability Version        : 0100
Exposure Mode                   : Auto
White Balance                   : Auto
Digital Zoom Ratio              : 1
Focal Length In 35mm Format     : 25 mm
Scene Capture Type              : Standard
Compression                     : JPEG (old-style)
Thumbnail Offset                : 1408
Thumbnail Length                : 64000
Image Width                     : 4000
Image Height                    : 3000
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Time Stamp                      : 2023:11:20 15:46:21.420-05:00
MCC Data                        : United States / Guam (310)
Aperture                        : 1.8
Image Size                      : 4000x3000
Megapixels                      : 12.0
Scale Factor To 35 mm Equivalent: 5.4
Shutter Speed                   : 1/24
Create Date                     : 2023:11:20 15:46:23.703
Date/Time Original              : 2023:11:20 15:46:23.703
Modify Date                     : 2023:11:20 15:46:23.703
Thumbnail Image                 : (Binary data 64000 bytes, use -b option to extract)
Circle Of Confusion             : 0.006 mm
Field Of View                   : 71.5 deg
Focal Length                    : 4.6 mm (35 mm equivalent: 25.0 mm)
Hyperfocal Distance             : 2.13 m
Light Value                     : 4.0

```

按照題目的意思，就是要把所有日期都改成`1970:01:01 00:00:00.001+00:00`，使用 exiftool 的指令就可以做改寫。

```php
exiftool '-modifydate=1970:01:01 00:00:00.001+00:00' original.jpg
exiftool '-datetimeoriginal=1970:01:01 00:00:00.001+00:00' original.jpg
exiftool '-createdate=1970:01:01 00:00:00.001+00:00' original.jpg
exiftool '-SubSecCreateDate=1970:01:01 00:00:00.001+00:00' original.jpg
exiftool '-SubSecDateTimeOriginal=1970:01:01 00:00:00.001+00:00' original.jpg
exiftool '-SubSecModifyDate=1970:01:01 00:00:00.001+00:00' original.jpg
```

剩下最後一個 Time Stamp 我一直找不到指令或是工具可以做編輯，後來同事提點可以直接去改它的hex。於是用 hexeditor 去找一下有沒有線索。後來在檔案的最後找到這個。

![](/picoCTF%202024/img/2024_4.png)

看起來像是時間戳記的東西，當我把它改成0後，Time Stamp 也會歸零，所以我把它改成1，就順利修改成`1969:12:31 19:00:00.001-05:00`了，提交後就可以拿到flag了~

>[!Tip]  
> **UNIX時間**是從UTC1970年1月1日0時0分0秒起至現在的總秒數。
> ![https://upload.wikimedia.org/wikipedia/commons/e/e9/Year_2038_problem.gif](https://upload.wikimedia.org/wikipedia/commons/e/e9/Year_2038_problem.gif)