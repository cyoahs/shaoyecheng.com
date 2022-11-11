---
title: 使用pip安装CUDA
date: 2022-11-11 21:47:52
tags: 工具
---

五六年前深度学习还是个新鲜事的时候，linux下显卡驱动、CUDA的很容易把小白折磨的非常痛苦，以至于当时还有一个叫manjaro的发行版，因为驱动安装简单流行。老黄也意识到了这个问题，增加了很多新的安装方式。

最近发现了[使用pip安装CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#pip-wheels-installation-linux)的方式。这种方法只安装一些必要的运行时，没有开发工具。好处是可以配合python的虚拟环境，无痛管理环境，windows/linux都能适用。但如果想在python以外使用，需要一些额外设置。

方法非常简单，只需要先编写一个`requirements.txt`，然后使用`pip`安装。

requirements.txt文件：
```
--extra-index-url https://pypi.ngc.nvidia.com

nvidia-cuda-runtime-cu11
nvidia-cuda-cupti-cu11
nvidia-cuda-nvcc-cu11
nvidia-nvml-dev-cu11
nvidia-cuda-nvrtc-cu11
nvidia-nvtx-cu11
nvidia-cuda-sanitizer-api-cu11
nvidia-cublas-cu11
nvidia-cufft-cu11
nvidia-curand-cu11
nvidia-cusolver-cu11
nvidia-cusparse-cu11
nvidia-npp-cu11
nvidia-nvjpeg-cu11
```
文件中的`cu11`代表CUDA 11，具体的11.X会自动选取。如果需要手动选取特定版本，应该直接写成`cu11x`就行了。

安装指令：
```
pip install -r requirements.txt
```

一般来说，国内会自动解析到英伟达的国内下载站（developer.download.nvidia.cn），所以速度非常快。