# FFMPEG将YUV图片生成视频

[TOC]



## 1.基本步骤

- 查找编码器AVCodec，函数为av_codec_find_encoder()或者av_codec_find_encoder_by_name()
- 根据编码器创建编码器上下文AVCodecContext,函数为avcodec_alloc_context3()
- 分配`AVPacket`内存空间（`AVPacket`保存数据包）,函数为av_packet_alloc()
- 设置AVCodecContext的参数
  - bit_rate (一般设置为400000，原因目前还没弄懂，先记着吧，后面深究的时候在研究)
  - width和height（编码后视频的大小）
  - time_base(相当于一帧的时间，含den和num两个参数，den表示分母，num表示分子)
  - framerate（表示帧率，和上面的time_base刚好相反，互为倒数，也有den和num两个参数，值与上面time_base的den和num相反)
  - gop_size
  - max_b_frame
  - pix_fmt(编码格式)

- 设置AVOptions参数，函数为av_opt_set
- 打开编码器，函数avcodec_open2()
- 打开输出文件
- 分配帧空间AVFrame，函数为av_frame_alloc()
- 设置帧参数
  - format
  - width
  - height
- 获取帧缓存，函数为av_frame_get_buffer()
- 打开yuv图片文件
- 读取yuv数据
- 将帧数据设置为可写
- 设置帧数据
  - data[0]表示Y
  - data[1]表示U
  - data[2]表示V
  - pts(时间戳)

- 将帧数据发送给编码器，函数为avcodec_send_frame()
- 接收包，函数为avcodec_receive_packet()，即将帧数据进行编码后放入包数据中
- 写入输出文件中
- 写完后刷新编码器
- 写入尾数据
- 释放资源

## 2.示例：

```cpp
#include <string.h>
#include <iostream>
extern "C" {
#include <libavcodec/avcodec.h>

#include <libavutil/opt.h>
#include <libavutil/imgutils.h>
}
using namespace std;

#pragma comment(lib,"avformat.lib")
#pragma comment(lib,"avutil.lib")
#pragma comment(lib,"avcodec.lib")

static int encode(AVCodecContext *enc_ctx, AVFrame *frame, AVPacket *pkt,
	FILE *outfile)
{
	int ret;

	/* send the frame to the encoder */
	if (frame)
		cout << "Send frame " << frame->pts << endl;

	ret = avcodec_send_frame(enc_ctx, frame);
	if (ret < 0) {
		fprintf(stderr, "Error sending a frame for encoding\n");
		return -1;
	}

	while (ret >= 0) {
		ret = avcodec_receive_packet(enc_ctx, pkt);
		if (ret == AVERROR(EAGAIN) || ret == AVERROR_EOF)
			return -1;
		else if (ret < 0) {
			fprintf(stderr, "Error during encoding\n");
			return -1;
		}

		cout << "Write packet " << pkt->pts << " (size=" << pkt->size << ")" << endl;
		fwrite(pkt->data, 1, pkt->size, outfile);
		av_packet_unref(pkt);
	}
	return 0;
}

int main(int argc, char **argv)
{
	const char *filename, *codec_name;
	const AVCodec *codec;
	AVCodecContext *c = NULL;
	int i, ret, x, y;
	FILE *f;
	AVFrame *frame;
	AVPacket *pkt;
	uint8_t endcode[] = { 0, 0, 1, 0xb7 };

	filename = "test.h264";

	/* find the mpeg1video encoder */
	codec = avcodec_find_encoder(AV_CODEC_ID_H264);
	if (!codec) {
		fprintf(stderr, "Codec not found encoder\n");
		return -1;
	}

	c = avcodec_alloc_context3(codec);
	if (!c) {
		fprintf(stderr, "Could not allocate video codec context\n");
		return -1;
	}

	pkt = av_packet_alloc();
	if (!pkt)
		return -1;

	/* put sample parameters */
	c->bit_rate = 400000;
	/* resolution must be a multiple of two */
	c->width = 256;
	c->height = 256;
	/* frames per second */
	c->time_base.den = 25;
	c->time_base.num = 1;
	c->framerate.den = 1;
	c->framerate.num = 25;

	/* emit one intra frame every ten frames
	 * check frame pict_type before passing frame
	 * to encoder, if frame->pict_type is AV_PICTURE_TYPE_I
	 * then gop_size is ignored and the output of encoder
	 * will always be I frame irrespective to gop_size
	 */
	c->gop_size = 10;
	c->max_b_frames = 1;
	c->pix_fmt = AV_PIX_FMT_YUV420P;

	if (codec->id == AV_CODEC_ID_H264)
		av_opt_set(c->priv_data, "preset", "slow", 0);

	/* open it */
	ret = avcodec_open2(c, codec, NULL);
	if (ret < 0) {
		fprintf(stderr, "Could not open codec: %d\n", ret);
		return -1;
	}

	f = fopen(filename, "wb");
	if (!f) {
		fprintf(stderr, "Could not open %s\n", filename);
		return -1;
	}

	frame = av_frame_alloc();
	if (!frame) {
		fprintf(stderr, "Could not allocate video frame\n");
		return -1;
	}
	frame->format = c->pix_fmt;
	frame->width = c->width;
	frame->height = c->height;

	ret = av_frame_get_buffer(frame, 32);
	if (ret < 0) {
		fprintf(stderr, "Could not allocate the video frame data\n");
		return -1;
	}
	FILE *fp = fopen("lena_256x256_yuv420p.yuv", "rb");
	if (!fp) {
		cout << "open file failed!" << endl;
		return -1;
	}
	unsigned char *pic = (unsigned char *)malloc(256*256 * 3);
	fread(pic, 1, 256*256 * 3 / 2, fp);

	/* encode 1 second of video */
	for (i = 0; i < 125; i++) {
		fflush(stdout);

		/* make sure the frame data is writable */
		ret = av_frame_make_writable(frame);
		if (ret < 0)
			return -1;

		/* prepare a dummy image */
		/* Y */
		for (y = 0; y < c->height * c->width; y++) {
			frame->data[0][y] = pic[y];
		}

		/* Cb and Cr */
		for (y = 0; y < c->height * c->width / 4; y++) {
				frame->data[1][y] = pic[c->height * c->width + y];
				frame->data[2][y] = pic[c->height * c->width * 5 / 4 + y];
		}

		frame->pts = i;

		/* encode the image */
		encode(c, frame, pkt, f);
	}

	/* flush the encoder */
	encode(c, NULL, pkt, f);

	/* add sequence end code to have a real MPEG file */
	fwrite(endcode, 1, sizeof(endcode), f);
	fclose(f);
	free(pic);
	avcodec_free_context(&c);
	av_frame_free(&frame);
	av_packet_free(&pkt);
	system("pause");
	return 0;
}
```