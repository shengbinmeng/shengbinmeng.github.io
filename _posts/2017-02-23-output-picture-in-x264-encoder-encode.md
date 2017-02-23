---
title: 在调用x264_encoder_encode时输出重建图像
date: 2017-02-23 12:00:00
category: 技术
tags: [x264, 编程, C/C++]
---

x264提供的视频编码库libx264应用很广泛。这个库的核心API函数是`x264_encoder_encode`。这个函数的最后一个参数，`pic_out`，用的人少，文档也少，很难理解。
今天总算把它搞清楚了，并成功地输出了重建图像。这个参数本来就是为这用途设计的，可惜没有任何说明文档。本文来补上这个缺憾。

<!--more-->

整个函数声明如下：

```c
/* x264_encoder_encode:
 *      encode one picture.
 *      *pi_nal is the number of NAL units outputted in pp_nal.
 *      returns the number of bytes in the returned NALs.
 *      returns negative on error and zero if no NAL units returned.
 *      the payloads of all output NALs are guaranteed to be sequential in memory. */
int     x264_encoder_encode( x264_t *, x264_nal_t **pp_nal, int *pi_nal, x264_picture_t *pic_in, x264_picture_t *pic_out );
```

可以看到，没有对`pic_out`进行说明。

x264官方提供有一个样例程序，[example.c](http://git.videolan.org/?p=x264.git;a=blob;f=example.c;h=0fb7fbdb2ab7158de4e53e0ca897260efdd10fb4;hb=HEAD)，展示了如何使用其API。
从这个例子可以知道其它参数都是怎么用的，唯独`pic_out`没有涉及。

下面就来介绍关于它我所了解到的信息。

从命名也大概可以猜到，与`pic_in`相对，`pic_out`是用来输出图像的。它输出的是编码中的重建图像，也就是解码能得到的图像。
`pic_in`需要用户自己为其分配内存，而`pic_out`则不需要。它的指针指向编码器内部分配的重建图像内存。

这重建图像的数据排布也是出人意料。对于YUV420p来说，Y分量在plane[0]，这很正常。可是U、V分量竟然都在plane[1]！plane[2]和plane[3]均未使用。
对于Y分量来说，每一行后有stride，读取时需要每次读一行，然后跳过stride长度。而对于U、V分量，却是交织在一起的，需要自行解开。
这使得从`pic_out`读像素比向`pic_in`写入像素要复杂地多。

每次得到一帧的NAL数据包时，`pic_out`就对应这帧的重建图像，此时可以从中读取数据并保存下来。不过需要注意，这样是按编码顺序保存，并非显示顺序。
为了使存下来的文件能正常播放，可以根据`pic_out`里的时间戳字段（`i_pts`）来计算出它的显示帧序号，然后跳到文件的相应位置写入。

综合以上信息，可以用如下代码来在编码过程中输出重建图像：

```c
// This is a private function of libx264, copied from mc.c.
static void x264_plane_copy_deinterleave_c(uint8_t *dstu, intptr_t i_dstu,
                                           uint8_t *dstv, intptr_t i_dstv,
                                           uint8_t *src,  intptr_t i_src, int w, int h )
{
    for( int y=0; y<h; y++, dstu+=i_dstu, dstv+=i_dstv, src+=i_src )
        for( int x=0; x<w; x++ )
        {
            dstu[x] = src[2*x];
            dstv[x] = src[2*x+1];
        }
}

// See x264_frame_dump (a private function of libx264, in encoder.c) for reference.
static int write_picture_out(FILE *file, x264_picture_t *pic_out, int width, int height)
{
    // Seek to where the frame should be.
    int frame_size = width * height * 1.5;
    if (fseek(file, pic_out->i_pts * frame_size, SEEK_SET) != 0) {
        return -1;
    }

    for (int i = 0; i < height; i++) {
        if (fwrite(pic_out->img.plane[0] + i * pic_out->img.i_stride[0], 1, width, file) != width) {
            return -1;
        }
    }

    int cw = width / 2;
    int ch = height / 2;
    int chroma_size = cw * ch;
    uint8_t *planeu = malloc(2 * (chroma_size + 32));
    if (planeu == NULL) {
        return -1;
    }

    uint8_t *planev = planeu + chroma_size + 32;
    x264_plane_copy_deinterleave_c(planeu, cw, planev, cw, pic_out->img.plane[1], pic_out->img.i_stride[1], cw, ch);

    fwrite(planeu, 1, chroma_size, file);
    fwrite(planev, 1, chroma_size, file);

    free(planeu);
    return 0;
}
```

上述代码中`i_pts`直接就是帧的显示序号，这是由特定输入决定的，并不通用。

此外，为了得到与解码结果完全一致的文件，还必须开启编码器的`b_full_recon`选项。
否则，对于一些在编码中非必要的重建操作（如非参考帧的去块滤波），x264将会省去，以节省计算量，从而导致`pic_out`中的图像与解码得到的有差异。

事实上，x264外壳程序支持用`--dump-yuv`选项来输出重建图像。我也是看了与此相关的代码才搞明白`pic_out`具体用法的。
作为公开API的一个参数，本应该在文档或注释里说明的，如果需要读代码才能搞明白，只能说对用户不甚友好了。

最后需要指出，除了重建图像像素，`pic_out`里还有别的一些有用字段，具体可以参考`x264_picture_t`结构体的定义。