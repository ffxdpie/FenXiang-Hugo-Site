---
title: "ffmpeg的使用方法"
date: 2023-08-01T14:25:15+08:00
description: "FFmpeg 是视频处理最常用的开源软件。本文介绍 FFmpeg 命令行如何处理视频，比桌面视频处理软件更简洁高效。"
showToc: true
TocOpen: false
categories:
  - "教程"
tags:
  - "FFmpeg"
---

# FFmpeg使用教程 


转载自[阮一峰](https://www.ruanyifeng.com/blog/2020/01/ffmpeg.html)

`FFmpeg 是视频处理最常用的开源软件。`

它功能强大，用途广泛，大量用于视频网站和商业软件（比如 Youtube 和 iTunes），也是许多音频和视频格式的标准编码/解码实现。
FFmpeg 本身是一个庞大的项目，包含许多组件和库文件，最常用的是它的命令行工具。本文介绍 FFmpeg 命令行如何处理视频，比桌面视频处理软件更简洁高效。

如果你还没安装，可以根据  [官方文档](https://www.ffmpeg.org/download.html)  先完成安装。

## 概念

介绍 FFmpeg 用法之前，需要了解一些视频处理的基本概念。

### 容器

视频文件本身其实是一个容器（container），里面包括了视频和音频，也可能有字幕等其他内容。

常见的容器格式有以下几种。一般来说，视频文件的后缀名反映了它的容器格式。

- MP4

- MKV

- WebM

- AVI

    

 下面的命令查看 FFmpeg 支持的容器。
    
$ ffmpeg -formats

###  编码格式

视频和音频都需要经过编码，才能保存成文件。不同的编码格式（CODEC），有不同的压缩率，会导致文件大小和清晰度的差异。

常用的视频编码格式如下。

- H.262
- H.264
- H.265


上面的编码格式都是有版权的，但是可以免费使用。此外，还有几种无版权的视频编码格式。

- VP8
- VP9
- AV1

常用的音频编码格式如下。

- MP3
- AAC

上面所有这些都是有损的编码格式，编码后会损失一些细节，以换取压缩后较小的文件体积。无损的编码格式压缩出来的文件体积较大，这里就不介绍了。

下面的命令可以查看 FFmpeg 支持的编码格式，视频编码和音频编码都在内。

```shell
ffmpeg -codecs
```




###  编码器

编码器（encoders）是实现某种编码格式的库文件。只有安装了某种格式的编码器，才能实现该格式视频/音频的编码和解码。

以下是一些 FFmpeg 内置的视频编码器。

- libx264：最流行的开源 H.264 编码器
- NVENC：基于 NVIDIA GPU 的 H.264 编码器
- libx265：开源的 HEVC 编码器
- libvpx：谷歌的 VP8 和 VP9 编码器
- libaom：AV1 编码器

音频编码器如下。

- libfdk-aac
- aac

下面的命令可以查看 FFmpeg 已安装的编码器。

```shell
ffmpeg -encoders
```




## FFmpeg 的使用格式

FFmpeg 的命令行参数非常多，可以分成五个部分。

```shell
ffmpeg {1} {2} -i {3} {4} {5}
```


上面命令中，五个部分的参数依次如下。

1. 全局参数
2. 输入文件参数
3. 输入文件
4. 输出文件参数
5. 输出文件

参数太多的时候，为了便于查看，ffmpeg 命令可以写成多行。

```shell
ffmpeg  \[全局参数] \[输入文件参数] \-i [输入文件] \[输出文件参数] \[输出文件]
```

下面是一个例子。

```shell
ffmpeg \

-y \ # 全局参数

-c:a libfdk_aac -c:v libx264 \ # 输入文件参数

-i input.mp4 \ # 输入文件

-c:v libvpx-vp9 -c:a libvorbis \ # 输出文件参数

output.webm # 输出文件
```

上面的命令将 mp4 文件转成 webm 文件，这两个都是容器格式。输入的 mp4 文件的音频编码格式是 aac，视频编码格式是 H.264；输出的 webm 文件的视频编码格式是 VP9，音频格式是 Vorbis。
如果不指明编码格式，FFmpeg 会自己判断输入文件的编码。因此，上面的命令可以简单写成下面的样子。

```shell
ffmpeg -i input.avi output.mp4
```

## FFmpeg推流命令

```shell
ffmpeg -re -stream_loop -1 -i "/home/fx/video/睡前故事.mp4" -preset ultrafast -vcodec libx264 -g 60 -b:v 1500k -c:a aac -b:a 128k -strict -2 -f flv "rtmp://live-push.bilivideo.com/live-bvc/?streamname=live_26234208_7077477&key=16498bac00ebba87e45314359ddd437d&schedule=rtmp&pflag=1"
```

## 在ubuntu上使用脚本定时监控

如果你决定保留所有日志，而不仅仅是错误日志，那么你可以维持脚本不做修改，它会继续记录所有的`ffmpeg`输出消息。你的脚本如下：

```shell
#!/bin/bash

# 设置输入视频文件路径
input_video="/home/video/睡前故事.mp4"

# 设置RTMP推流地址
rtmp_url="rtmp://live-push.bilivideo.com/live-bvc/?streamname=live_26234208_7077477&key=16498bac00ebba87e45314359ddd437d&schedule=rtmp&pflag=1"

# 设置日志文件路径
log_file="/home/video/ffmpeg_error_log.txt"

# 检查运行状态并重新启动
check_and_restart() {
    if ! pgrep -x "ffmpeg" > /dev/null; then
        echo "FFmpeg is not running. Restarting..."
        ffmpeg -re -stream_loop -1 -i "$input_video" -preset ultrafast -vcodec libx264 -g 60 -b:v 1500k -c:a aac -b:a 128k -strict -2 -f flv "$rtmp_url" 2>> "$log_file" &
    fi
}

# 主循环，每60秒检查一次
while true; do
    check_and_restart
    sleep 60
done

```



### [docker版ffmpeg](https://hub.docker.com/r/linuxserver/ffmpeg)

运行是视频卡顿

```shell
docker run --rm -it \
  -v /home/fx/video:/config \
  linuxserver/ffmpeg:arm64v8-latest \
  -stream_loop -1 -i "/config/睡前故事.mp4" -preset ultrafast -vcodec libx264 -g 60 -b:v 1500k -c:a aac -b:a 128k -strict -2 -f flv "rtmp://live-push.bilivideo.com/live-bvc/?streamname=live_26234208_7077477&key=16498bac00ebba87e45314359ddd437d&schedule=rtmp&pflag=1"
```

## 常用命令行参数

FFmpeg 常用的命令行参数如下。

- c：指定编码器
- c copy：直接复制，不经过重新编码（这样比较快）
- c:v：指定视频编码器
- c:a：指定音频编码器
- i：指定输入文件
- an：去除音频流
- vn： 去除视频流
- preset：指定输出的视频质量，会影响文件的生成速度，有以下几个可用的值 ultrafast, superfast, veryfast, faster, fast, medium, slow, slower, veryslow。
- y：不经过确认，输出时直接覆盖同名文件。

## 常见用法

下面介绍 FFmpeg 几种常见用法。

###  查看文件信息

查看视频文件的元信息，比如编码格式和比特率，可以只使用-i参数。

```shell
ffmpeg -i input.mp4
```

上面命令会输出很多冗余信息，加上-hide_banner参数，可以只显示元信息。

```shell
ffmpeg -i input.mp4 -hide_banner
```



###  转换编码格式

转换编码格式（transcoding）指的是， 将视频文件从一种编码转成另一种编码。比如转成 H.264 编码，一般使用编码器libx264，所以只需指定输出文件的视频编码器即可。

```shell
ffmpeg -i [input.file] -c:v libx264 output.mp4
```

下面是转成 H.265 编码的写法。

```shell
ffmpeg -i [input.file] -c:v libx265 output.mp4
```



### 转换为m3u8格式

```shell
ffmpeg -i "D:\下载\Video\睡前故事.mp4" -c:v libx264 -c:a aac -strict -2 -f hls -hls_list_size 2 -hls_time 60 output.m3u8
```



###  转换容器格式

转换容器格式（transmuxing）指的是，将视频文件从一种容器转到另一种容器。下面是 mp4 转 webm 的写法。

```shell
ffmpeg -i input.mp4 -c copy output.webm
```

上面例子中，只是转一下容器，内部的编码格式不变，所以使用-c copy指定直接拷贝，不经过转码，这样比较快。

###  调整码率

调整码率（transrating）指的是，改变编码的比特率，一般用来将视频文件的体积变小。下面的例子指定码率最小为964K，最大为3856K，缓冲区大小为 2000K。

```shell
ffmpeg \
-i input.mp4 \
-minrate 964K -maxrate 3856K -bufsize 2000K \
output.mp4
```



###  改变分辨率（transsizing）

下面是改变视频分辨率（transsizing）的例子，从 1080p 转为 480p 。

```shell
ffmpeg \
-i input.mp4 \
-vf scale=480:-1 \
output.mp4
```



### 提取音频

有时，需要从视频里面提取音频（demuxing），可以像下面这样写。

```shell
ffmpeg \
-i input.mp4 \
-vn -c:a copy \
output.aac
```

上面例子中，-vn表示去掉视频，-c:a copy表示不改变音频编码，直接拷贝。

###  添加音轨

添加音轨（muxing）指的是，将外部音频加入视频，比如添加背景音乐或旁白。

```shell
ffmpeg \
-i input.aac -i input.mp4 \
output.mp4
```

上面例子中，有音频和视频两个输入文件，FFmpeg 会将它们合成为一个文件。

###  截图

下面的例子是从指定时间开始，连续对1秒钟的视频进行截图。

```shell
ffmpeg \
-y \
-i input.mp4 \
-ss 00:01:24 -t 00:00:01 \
output_%3d.jpg
```

如果只需要截一张图，可以指定只截取一帧。

```shell
ffmpeg \
-ss 01:23:45 \
-i input \
-vframes 1 -q:v 2 \
output.jpg
```

上面例子中，-vframes 1指定只截取一帧，-q:v 2表示输出的图片质量，一般是1到5之间（1 为质量最高）。

###  裁剪

裁剪（cutting）指的是，截取原始视频里面的一个片段，输出为一个新视频。可以指定开始时间（start）和持续时间（duration），也可以指定结束时间（end）。

```shell
ffmpeg -ss [start] -i [input] -t [duration] -c copy [output]
ffmpeg -ss [start] -i [input] -to [end] -c copy [output]
```

下面是实际的例子。

```shell
ffmpeg -ss 00:01:50 -i [input] -t 10.5 -c copy [output]
ffmpeg -ss 2.5 -i [input] -to 10 -c copy [output]
```

上面例子中，-c copy表示不改变音频和视频的编码格式，直接拷贝，这样会快很多。



###  为音频添加封面

有些视频网站只允许上传视频文件。如果要上传音频文件，必须为音频添加封面，将其转为视频，然后上传。

下面命令可以将音频文件，转为带封面的视频文件。

```shell
ffmpeg \
-loop 1 \
-i cover.jpg -i input.mp3 \
-c:v libx264 -c:a aac -b:a 192k -shortest \
output.mp4
```

上面命令中，有两个输入文件，一个是封面图片cover.jpg，另一个是音频文件input.mp3。-loop 1参数表示图片无限循环，-shortest参数表示音频文件结束，输出视频就结束。

## ffplay 的用法

### 主要选项

ffplay [选项] ['输入文件']

```shell
'-x width'        强制以 "width" 宽度显示
'-y height'       强制以 "height" 高度显示
'-an'             禁止音频
'-vn'             禁止视频
'-ss pos'         跳转到指定的位置(秒)
'-t duration'     播放 "duration" 秒音/视频
'-bytes'          按字节跳转
'-nodisp'         禁止图像显示(只输出音频)
'-f fmt'          强制使用 "fmt" 格式
'-window_title title'  设置窗口标题(默认为输入文件名)
'-loop number'    循环播放 "number" 次(0将一直循环)
'-showmode mode'  设置显示模式
```

 可选的 mode ：

```shell
- '0, video'    显示视频
- '1, waves'    显示音频波形
- '2, rdft'     显示音频频带
- 默认值为 'video'，你可以在播放进行时，按 "w" 键在这几种模式间切换
- '-i input_file'   指定输入文件
```

### 一些高级选项

```shell
'-sync type'          设置主时钟为音频、视频、或者外部。默认为音频。主时钟用来进行音视频同步
'-threads count'      设置线程个数
'-autoexit'           播放完成后自动退出
'-exitonkeydown'      任意键按下时退出
'-exitonmousedown'    任意鼠标按键按下时退出
'-acodec codec_name'  强制指定音频解码器为 "codec_name"
'-vcodec codec_name'  强制指定视频解码器为 "codec_name"
'-scodec codec_name'  强制指定字幕解码器为 "codec_name"
```

### 一些快捷键

```shell
'q, ESC'            退出
'f'                 全屏
'p, SPC'            暂停
'w'                 切换显示模式(视频/音频波形/音频频带)
's'                 步进到下一帧
'left/right'        快退/快进 10 秒
'down/up'           快退/快进 1 分钟
'page down/page up' 跳转到前一章/下一章(如果没有章节，快退/快进 10 分钟)
'mouse click'       跳转到鼠标点击的位置(根据鼠标在显示窗口点击的位置计算百分比)
```

### ffplay 高级使用方式

- 循环播放

  ```shell
  ffplay pm.mp4 -loop 10
  ```

  

- 播放 pm.mp4 ，播放完成后自动退出

  ```shell
  ffplay -autoexit pm.mp4
  ```

  

- 以 320 x 240 的大小播放 test.mp4

  ```shell
  ffplay -x 320 -y 240 pm.mp4
  ```

  

- 将窗口标题设置为 "myplayer"，循环播放 2 次  

  ```shell
  ffplay -window_title myplayer -loop 2 pm.mp4
  ```

  

- 播放 双通道 32K 的 PCM 音频数据 

  ```shell
  ffplay -f s16le -ar 32000 -ac 2 test.pcm
  ```

  

### ffplay音画同步

- ffplay也是一个视频播放器，所以不得不提出来的一个问题是：音画同步。ffplay的音画同步的实现方式其实有三种，分别是：以音频为主时间轴作为同步源，以视频为主时间轴作为同步源，以外部时钟为主时间轴作为同步源。

- 下面就以音频为主时间轴来作为同步源来作为案例进行讲解，而且ffplay默认也是以音频为基准进行对齐的，那么以音频作为对齐基准是如何实现的呢？

- 首先需要说明的是，播放器接收到的视频帧或者音频帧，内部都是会有时间戳（PTS时钟）来标识它实际应该在什么时刻展示，实际的对齐策略如下：比较视频当前的播放时间和音频当前的播放时间，如果视频播放过快，则通过加大延迟或者重复播放来降低视频播放速度，如果视频播放满了，则通过减小延迟或者丢帧来追赶音频播放的时间点。关键就在于音视频时间的比较和延迟的计算，当前在比较的过程中会设置一个阈值，如果超过预设的阈值就应该作出调整（丢帧或者重复渲染），这就是整个对齐策略。

- 在使用ffplay的时候，我们可以明确的指定使用那种对齐方式，比如

```shell
ffplay pm.mp4 -sync audio
```

上面这个命令显式的指定了使用以音频为基准进行音视频同步的方式播放视频文件，当然这也是ffplay的默认播放设置。

```shell
ffplay pm.mp4 -sync video
```

上面这个命令显式的指定了使用以视频为基准进行音视频同步的方式播放视频文件。

```shell
ffplay pm.mp4 -sync ext
```

上面这个命令显式的指定了使用外部时钟为基准进行音视频同步的方式播放视频文件。

大家可以分别使用这三种方式进行播放，尝试听一听，做一些快进或者seek的操作，看看不同的对齐策略对最终的播放会产生什么样的影响。

## 参考链接

- [FFmpeg libav tutorial](https://github.com/leandromoreira/ffmpeg-libav-tutorial#chapter-3---transcoding)

- [Digital video introduction](https://github.com/leandromoreira/digital_video_introduction/blob/master/encoding_pratical_examples.md#split-and-merge-smoothly)

- [FFmpeg encoding and editing course](http://slhck.info/ffmpeg-encoding-course/)

- [Making Slideshows w/FFMpeg](https://dragonquest64.blogspot.com/2019/10/making-slideshows-wffmpeg.html)

- [The Complete Guide for Using ffmpeg in Linux](https://itsfoss.com/ffmpeg/)

- [Adding subtitles to your videos the easy way](https://bernd.dev/2020/04/adding-subtitles/)

- [播放媒体文件的工具ffplay](https://www.cnblogs.com/renhui/p/8458802.html)
