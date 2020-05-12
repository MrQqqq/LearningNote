# FFMPEG解码视频的大致过程

## 1.注册组件

```cpp
av_register_all();
```



## 2.网络初始化

```cpp
avformat_networt_init();
```

## 3.创建格式上下文

```cpp
AVFormatContext* fmt = avformat_alloc_context();
```

## 4.打开文件，并把文件格式信息保存到格式上下文中

```cpp
int ret avformat_open_input(&fmt,filepath,NULL,NULL);
```

这里可以判断是否打开成功，成功的话返回值大于等于0，否则返回值小于0.

## 5.查找流信息

```cpp
ret = avformat_find_stream_info(fmt,NULL);
```

这里也可以判断是否查找成功。

## 6.找到视频流在输入文件的流数据中的索引

```cpp
int videoindex = -1;
for (i = 0; i < pFormatCtx->nb_streams; i++) {
	if (pFormatCtx->streams[i]->codec->codec_type == AVMEDIA_TYPE_VIDEO) {
		videoindex = i;
		break;
	}
}
//如果视频索引依然等于-1，表示没有在输入文件的流数据中找到视频流
if (videoindex == -1) {
printf("Didn't find a video stream.\n");
return -1;
}
```



## 7.获取视频流编解码上下文

```cpp
AVCodecContext *pCodecCtx = fmt->streams[videoindex]->codec;
```



## 8.查找解码器

```cpp
AVCodec *pCodec = avcodec_find_decoder(pCodecCtx->codec_id);

//没有找到解码器
	if (pCodec == NULL) {
		printf("Codec not found.\n");
		return -1;
	}
```



## 9.avcodec_open2()函数用于初始化一个视音频编解码器的AVCodecContext

```cpp
if (avcodec_open2(pCodecCtx, pCodec, NULL) < 0) {
		printf("Could not open codec.\n");
		return -1;
	}
```



## 10.给帧数据分配内存

```cpp
AVFrame pFrame = av_frame_alloc();//原数据

AVFrame pFrameYUV = av_free_alloc();//解码后的数据

uint8* out_buffer = (uint8*)av_malloc(avpicture_get_size(AV_PIX_FMT_YUV420P,pCodecCtx->width,pCodecCtx->height));
```



## 11.avpicture_fill函数为已经分配空间的AVPicture挂上保存数据的空间

```cpp
avpicture_fill((AVPicture *)pFrameYUV, out_buffer, AV_PIX_FMT_YUV420P, pCodecCtx->width, pCodecCtx->height);
```



## 12.设置图片转换上下文

```cpp
struct SwsContext *img_convert_ctx = sws_getContext(pCodecCtx->width, pCodecCtx->height, pCodecCtx->pix_fmt,
		pCodecCtx->width, pCodecCtx->height, AV_PIX_FMT_YUV420P, SWS_BICUBIC, NULL, NULL, NULL);
```



## 13.分配包数据内存空间

```cpp
	AVPacket *packet = (AVPacket *)av_malloc(sizeof(AVPacket));
```

## 14.对视频流进行解码

```cpp
while(1){
	if (packet->stream_index == videoindex) {
		ret = avcodec_decode_video2(pCodecCtx, pFrame, &got_picture, packet);
        if (ret < 0) {
            printf("Decode Error.\n");
            return -1;
        }
        if (got_picture) {
            sws_scale(img_convert_ctx, (const uint8_t* const*)pFrame->data, pFrame->linesize, 0, pCodecCtx->height, pFrameYUV->data, pFrameYUV->linesize);
        }
    }
    av_free_packet(packet);
}
```

## 15.释放资源

```cpp
sws_freeContext(img_convert_ctx);
av_frame_free(&pFrameYUV);
av_frame_free(&pFrame);
avcodec_close(pCodecCtx);
avformat_close_input(&fmt);
```

