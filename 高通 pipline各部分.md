1.lens shading

定义：

Lens shading，由于相机镜头的尺寸和质量，以及相机的结构的影响，镜头中心的光感二极管接收到的光会大于镜头边缘，这就造成图像中间比四周亮。高通通过rolloff模块来调整图像亮度shading和color shading

调试说明：

Lens shading校正需要使用lens shading golden sample。

Radius Percentage和Correction Percentage分别表示shading校正的区域和校正的程度。数值越大，校正的强度越大，照片的亮度也就越高，相应的产生的噪点也就越多。

1.   2D  LUT

目的：

准确的渲染颜色，使颜色更加真实；

渲染喜好的颜色（例如：蓝色的天空、绿色的草地、肤色）

平台常规处理方法

Color Correction、color conversion、skin color enhancement、memory color enhancement

常规处理方法的局限性：

颜色之间存在干扰，对色彩的处理没考虑到亮度的影响，SCE、MCE很复杂，不方便调试。

2DLUT在pps阶段，在RGB域处理的最后阶段。输入和输出都是在RGB空间，但是在模块内部是在HSL颜色空间上对图像进行处理。可以单独对特定的颜色进行处理。在不影响亮度的前提下调整色调和饱和度。调整色调和饱和度的同时可以限制亮度值。

可以局部的调整色彩的饱和度和色调，简单直观
![[Pasted image 20250224165757.png]]

ABF Tuning

adaptive Bayer filter

ABF Tuning，ABF是在raw图上对图像做的去噪处理。高通ABF去噪使用双边滤波。ABF在raw域根据不同的亮度条件对图像做去噪处理

4.ANR

Advanced Noise Reduction (ANR)，

ANR在IPE中的NPS阶段，这就意味着ANR在video和snapshot阶段都会生效。它是一个多通道的空间滤波器 
![[Pasted image 20250224165935.png]]

![[Pasted image 20250224165940.png]]
ASF

Adaptive Spatial Filter（ASF），Adaptive Spatial Filter模块主要是用来加强图像的边缘，是照片的锐度更高。但相应的也会带出更多的噪点
![[Pasted image 20250224170004.png]]
![[Pasted image 20250224170008.png]]
6.BPC/BCC/PDPC

Bad Pixel Correction/Bad Cluster Correction Correction/PD Pixel Correction 

一般情况下， RGB 信号应与景物亮度呈线性响应关系， 但由于 Senor 部分 pixel 不良导致输出的信号不正常， 出现白点或黑点。
![[Pasted image 20250224170033.png]]

PDPC主要用于对PD点的校正，PD点在ISP端会被当成坏点来处理
![[Pasted image 20250224170047.png]]
由厂家去calibration 

- 测试说明：

拍摄一半全黑一半全白卡片。拍摄2张图片，一张左边黑右边白，一张左边白右边黑。同时dump mipi raw数据

1、坏点是否去除干净，查看输入图是否存在坏点，主要查看彩色坏点，黑白坏点。

2、去除换点对噪声细节的影响，同时查看和mipi raw数据的噪声差异，理想状态下只存在坏点的差异

7.blacklevel subtraction

8.color correction

9.gamma

10.GIC

GIC在BPS流程里面，处于ABF和LSC之前。只在snapshot模式生效。主要是为了解决一些差的sensor无法做到Gr/Gb的平衡
![[Pasted image 20250224170107.png]]

11.HNR

- 定义说明：

Hybrid Noise Reduction (HNR)，

HNR模块是用来去除亮度噪点和保持细节的混合模块，它处于BPS的最后阶段，因HNR只在snapshot的模式起作用。是基于DCT的频域降噪，梯度平滑和空间域混合组成

HNR作用：

1.降低空域和频域的噪点

2.输入照片参与加权

3.渐进平滑处理锯齿边缘

4.在去噪和混合阶段多重控制

5.只去除亮度噪点

6.10bit的输入和输出

- 调试说明：

HNR包含level、radial、chroma、skin、flatness和frequency去噪滤波器。

HNR去噪效果
![[Pasted image 20250224170123.png]]

12.image correction and adjustmer

主要处理照片的畸变问题

13.local Tone Mapping

LTM主要功能是拉高图像中暗部的亮度，降低亮区的亮度。

14.radial

为了保留图像四角的细节，建议使用活动调整进行radial处理。

15 temporal filter

在video和preview中每一帧都起作用，混合的当前帧和前一阵的信息
![[Pasted image 20250224170134.png]]

16upscaler
在upscaler模块中会对图像的细节做增强处理
![[Pasted image 20250224170152.png]]

Upscaler在pps阶段，对image进行滤波、缩放和锐化。使用定向滤波器来提高图像的质量。在增强细节的同时对image进行锐化和平滑。支持1X-20X的放大，输入和输出都是10bit的YUV444图片

![[Pasted image 20250224170202.png]]

