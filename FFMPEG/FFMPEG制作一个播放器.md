# FFMPEG学习笔记

**学习参考博客：**

[CSDN博客主：令狐掌门](https://blog.csdn.net/yao_hou/article/details/80553660)

## 一、环境配置

### 1.1 ffmpeg下载

[链接：https://pan.baidu.com/s/1PPeEpWytpdbmDyQof-ahnA 
提取码：hoh6 ](https://pan.baidu.com/s/1PPeEpWytpdbmDyQof-ahnA 
)

![image-20200113213057809](C:\Users\86151\AppData\Roaming\Typora\typora-user-images\image-20200113213057809.png)

依次下载下面三个zip文件。

`Dev`文件夹下是`include`和`lib`文件，而`Shared`文件的`bin`下是`ffmpeg`的`dll`文件。

### 1.2 在VS中配置ffmpeg

**参考博客：**

[https://blog.csdn.net/yao_hou/article/details/80553660](https://blog.csdn.net/yao_hou/article/details/80553660)

`QT`和`VS`的安装省略，可以自行百度。

## 二、项目编写

### 2.1日志使用及目录操作

- 头文件 `#include<libavutil/log.h>`
- `av_log_set_level(AV_LOG_DEBUG)`//设置日志等级
- `av_log(NULL,AV_LOG_INFO,"....%s\n",op)`//输出日志

日志级别：

1. **`AV_LOG_ERROR`**

2. **`AV_LOG_WARNING`**
3. **`AV_LOG_INFO`**

4. **`AV_LOG_DEBUG`**

日志级别从上到下依次递减，如果`av_log`里面的日志级别大于`av_log_set_level`设置的日志级别，那么就会输出。

**示例：**

```cpp
#include <iostream>
//因为ffmpeg的文件使用c语言写的，因此这里要使用c来编译
extern "C"
{
#include <libavutil/log.h>
}

using namespace std;
int main()
{
    //设置日志级别
	av_log_set_level(AV_LOG_DEBUG);
    //打印日志，这里因为AV_LOG_INFO的级别大于DEBUG，因此会输出
	av_log(NULL, AV_LOG_INFO, "hello world! %s.\n","szq");
	return 0;
}
```

**结果为：**

```sh
hello world! szq.
```

如果将日志等级设置高一点，而`av_log`里的等级小于它，那么就会没有输出。

**示例：**

```cpp
#include <iostream>
extern "C"
{
#include <libavutil/log.h>
}
using namespace std;

int main()
{
	av_log_set_level(AV_LOG_INFO);
	av_log(NULL, AV_LOG_DEBUG, "hello world! %s.\n","szq");
	return 0;
}
```

**结果为空，不会输出。**

### 2.2文件操作

- 引入头文件`#include "libavformat/avformat.h"`

- 删除文件：`avpriv_io_delete()`函数
- 重命名文件：`avpriv_io_move()`函数

**示例：**

```cpp
extern "C" {
//需要新引入一个头文件avformat.h
#include "libavformat/avformat.h"
#include "libavutil/log.h"
}

int main(int argc, char* argv[]) {
	//用来接收删除文件的结果
	int ret = 0;
	//设置日志级别
	av_log_set_level(AV_LOG_DEBUG);

	//重命名
	//重命名文件，如果成功，返回值大于0，否则返回值小于0
	ret = avpriv_io_move("./1.pm4", "2.mp4");
	//如果返回值小于0，那么删除文件出错，输出一个日志信息
	if (ret < 0) {
		av_log(NULL, AV_LOG_ERROR, "failed to rename file of 1.mp4 to 2.mp4!\n");
		return -1;
	}
	//重命名成功，输出一个日志信息
	av_log(NULL, AV_LOG_INFO, "rename file of 1.mp4 to 2.pm4 have done!\n ");

	//删除文件
	//删除文件，如果删除成功，返回值大于0，否则返回值小于0
	ret = avpriv_io_delete("./1.mp4");
	//如果返回值小于0，那么删除文件出错，输出一个日志信息
	if (ret < 0) {
		av_log(NULL, AV_LOG_ERROR, "failed to delete file 1.mp4!n");
		return -1;
	}
	//删除文件成功，输出一个日志信息
	av_log(NULL, AV_LOG_INFO, "delete file 1.mp4 have done!\n");
	return 0;
}
```

**结果为：**

```sh
rename file of 1.mp4 to 2.pm4 have done!
 failed to delete file 1.mp4!n
```

并且你会发现下面一行报错的信息被标红了。

### 2.3 目录操作

- `avio_open_dir()`函数
- `avio_read_dir()`函数
- `avio_close_dir()`函数
- `AVIODirContext`结构体：操作目录的上下文
- `AVIODirEntry`结构体：目录项，用来存放文件名，文件大小等信息。

**示例：实现ls功能**

```cpp
extern "C"{
#include <libavformat/avformat.h>
#include <libavutil/log.h>
};

int main(int argc,char* argv[]){
    //用来保存执行结果
    int ret;
    //创建一个目录上下文对象
    AVIODirContext *ctx = NULL;
    //创建一个目录实体
    AVIODirEntry *entry = NULL;
    //设置日志级别
    av_log_set_level(AV_LOG_DEBUG);
    //打开目录
    avio_open_dir(&ctx,"./",NULL);
    //如果打开失败，输出日志信息，并关闭目录上线文对象再结束程序
    if(ret < 0){
        av_log(NULL,AV_LOG_ERROR,"打开目录失败...\n");
        goto __fail;
    }
    //依次遍历目录
    while(1){
        //读取目录
        ret = avio_read_dir(ctx,&entry);
        //如果读取目录失败，输出日志信息，并关闭目录上下文对象再结束程序
        if(ret < 0){
            av_log(NULL,AV_LOG_ERROR,"读取目录失败....\n");
            goto __fail;
        }
        //如果目录实体为空，则退出循环
        if(!entry){
            break;
        }
        //输出文件的大小和文件名
        av_log(NULL,AV_LOG_INFO,"%12" PRId64" %s \n",entry->size,entry->name);
        //关闭目录实体
        avio_free_directory_entry(&entry);
    }
    __fail:
    //关闭目录上下文对象
    avio_close_dir(&ctx);
    return 0;
}
```

**结果为：**

```sh
    4096 CMakeFiles 
        5662 ffmpegDemo1.cbp 
           0 test.mkv 
        5234 Makefile 
        1539 cmake_install.cmake 
       16456 ffmpegDemo1 
       45717 CMakeCache.txt 
```

### 2.4 处理多媒体文件

几个重要的结构体：

- `AVFormatContext`
- `AVStream`
- `AVPacket`

流程：

解复用->获取流->读数据包->释放资源

**示例1：打印音/视频信息**

用到的函数：

- av_register_all();
- avformat_open_input()/avformat_close_input()
- av_dump_format();

```cpp
extern "C"{
#include <libavformat/avformat.h>
#include <libavutil/log.h>
};

int main(int argc,char* argv[]){
    //用来保存执行的结果
    int ret;
    //创建一个音视频格式上下文对象
    AVFormatContext *fmt_ctx = NULL;
    //设置日志级别
    av_log_set_level(AV_LOG_DEBUG);
    //注册
    av_register_all();
    //打开输入文件的格式
    ret = avformat_open_input(&fmt_ctx,"./test.mp4",NULL,NULL);
    //如果打开失败，则输出日志信息，并结束程序
    if(ret < 0 ){
        av_log(NULL,AV_LOG_ERROR,"无法打开文件！\n");
        return -1;
    }
    //格式化输出格式信息
    av_dump_format(fmt_ctx,0,"./test.mp4",0);
    //关闭格式上下文
    avformat_close_input(&fmt_ctx);

    return 0;
}
```

**结果为：**

```sh
[NULL @ 0x55cfc5efbbe0] Opening './test.mp4' for reading
[file @ 0x55cfc5efc480] Setting default whitelist 'file,crypto'
[mov,mp4,m4a,3gp,3g2,mj2 @ 0x55cfc5efbbe0] Format mov,mp4,m4a,3gp,3g2,mj2 probed with size=2048 and score=100
[mov,mp4,m4a,3gp,3g2,mj2 @ 0x55cfc5efbbe0] ISO: File Type Major Brand: isom
[mov,mp4,m4a,3gp,3g2,mj2 @ 0x55cfc5efbbe0] Unknown dref type 0x206c7275 size 12
[mov,mp4,m4a,3gp,3g2,mj2 @ 0x55cfc5efbbe0] Processing st: 0, edit list 0 - media time: 0, duration: 222213
Input #0, mov,mp4,m4a,3gp,3g2,mj2, from './test.mp4':
  Metadata:
    major_brand     : isom
    minor_version   : 512
    compatible_brands: isomiso2mp41
    encoder         : Lavf57.83.100
  Duration: 00:00:14.47, bitrate: N/A
    Stream #0:0(und), 0, 1/15360: Video: mpeg4, 1 reference frame (mp4v / 0x7634706D), none, 1360 kb/s, 30 fps, 30 tbr, 15360 tbn (default)
    Metadata:
      handler_name    : VideoHandler
[AVIOContext @ 0x55cfc5f04860] Statistics: 35498 bytes read, 1 seeks
```

**示例2：抽取音频数据**

用到的函数

- av_init_packet()
- av_find_best_stream()
- av_reaf_frame()/av_packet_unref()

```cpp
extern "C"{
#include <libavformat/avformat.h>
#include <libavutil/log.h>
};
int main(int argc,char* argv[]){
    //用来保存执行的结果
    int ret;
    //创建一个包对象
    AVPacket *pkt;
    //创建一个音视频格式上下文对象
    AVFormatContext *fmt_ctx = NULL;
    //设置日志级别
    av_log_set_level(AV_LOG_DEBUG);
    //注册
    av_register_all();

    //从控制台获取两个参数
    //如果控制台传入的参数不够三个，那么输出日志信息
    if(argc < 3){
        av_log(NULL,AV_LOG_ERROR,"传入的参数数量不够...\n");
        return -1;
    }
    //读取参数
    char *src = argv[1];//源文件
    char *dst = argv[2];//保存的目的文件
    //如果有参数为空，那么输出日志信息
    if(!src || !dst){
        av_log(NULL,AV_LOG_ERROR,"src或者dst为空...\n");
        return -1;
    }

    //打开输入文件的格式
    ret = avformat_open_input(&fmt_ctx,src,NULL,NULL);
    //如果打开失败，则输出日志信息，并结束程序
    if(ret < 0 ){
        av_log(NULL,AV_LOG_ERROR,"无法打开文件！\n");
        return -1;
    }


    //打开保存的目的文件，没有就会自动创建
    FILE* dst_fd = fopen(dst,"wb");
    //如果文件打开失败，那么输出日志信息
    if(!dst_fd){
        av_log(NULL,AV_LOG_ERROR,"文件打开失败...\n");
        //关闭格式上下文
        avformat_close_input(&fmt_ctx);
        return -1;
    }

    //获取流对象
    ret = av_find_best_stream(fmt_ctx,AVMEDIA_TYPE_AUDIO,-1,-1,NULL,0);
    //如果获取流失败，那么输出日志信息并关闭已打开的资源
    if(ret < 0){
        av_log(NULL,AV_LOG_ERROR,"无法查找最好的流\n");
        avformat_close_input(&fmt_ctx);
        fclose(dst_fd);
        return -1;
    }
    //打开成功后返回的是流对象的索引
    int audio_index = ret;
    //初始化一个包
    av_init_packet(pkt);
    //循环读取包中的数据
    while(av_read_frame(fmt_ctx,pkt) >= 0){
        if(pkt->stream_index == audio_index){
            int len = fwrite(pkt->data,1,pkt->size,dst_fd);
            if(len != pkt->size){
                av_log(NULL,AV_LOG_ERROR,"数据长度不等于pkt的长度....\n");
            }
        }
        av_packet_unref(pkt);
    }

    //关闭格式上下文
    avformat_close_input(&fmt_ctx);

    return 0;
}
```

**结果为：传入两个参数，生成第二个参数为文件名的文件**

**示例3：抽取视频数据**

