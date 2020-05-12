# FFMPEG命令行指令

## 1.查看设备

```sh
ffmpeg -list_devices true -f dshow -i dummy
```

## 2.通过ffplay打开设备

```sh
ffplay -f dshow -i video="摄像头名称":audio="麦克风名称"
```

上面的摄像头名称和麦克风名称需要做相应的变换，通过`1`可以查看电脑中支持的设备，用相应的设备名称替换就可以了。

## 3. 录一段视频，按q键停止

```sh
ffmpeg -f dshow -i video="摄像头名称":audio="麦克风名称" -s 640x360 -b:v 1000k -b:a 128k output.mkv
```

## 4.查看视频信息

```sh
ffmpeg -i 1.mp4			
```

**结果为**

```sh
Input #0, mov,mp4,m4a,3gp,3g2,mj2, from '1.mp4':
  Metadata:
    major_brand     : mp42
    minor_version   : 1
    compatible_brands: M4V mp42isom
    creation_time   : 2015-01-11T01:12:27.000000Z
  Duration: 00:23:09.43, start: 0.000000, bitrate: 305 kb/s
    Stream #0:0(eng): Video: h264 (High) (avc1 / 0x31637661), yuv420p(tv, bt709), 1280x720 [SAR 1:1 DAR 16:9], 209 kb/s, 15 fps, 15 tbr, 15k tbn, 30 tbc (default)
    Metadata:
      creation_time   : 2015-01-11T01:12:27.000000Z
      handler_name    : Video Media Handler
      encoder         : AVC Coding
    Stream #0:1(eng): Audio: aac (LC) (mp4a / 0x6134706D), 44100 Hz, stereo, fltp, 93 kb/s (default)
    Metadata:
      creation_time   : 2015-01-11T01:12:27.000000Z
      handler_name    : Sound Media Handler
```

