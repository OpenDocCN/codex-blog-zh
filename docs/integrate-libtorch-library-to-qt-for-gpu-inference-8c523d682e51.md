# 将 LibTorch 库集成到 QT 中用于 GPU 推理

> 原文：<https://medium.com/codex/integrate-libtorch-library-to-qt-for-gpu-inference-8c523d682e51?source=collection_archive---------2----------------------->

Qt 是构建跨平台 GUI 应用程序的强大框架。对于生产部署场景，您需要将在 PyTorch 机器学习框架中开发的模型集成到您的 c++ QT 中。为了在 c++中加载 PyTorch 模型，您需要将您的模型转换为 Torch 脚本格式。你可以在这里找到如何将 Pytorch 模型转换成 torch 脚本[的详细步骤。](https://pytorch.org/tutorials/advanced/cpp_export.html)

# 下载 libtorch 库

进入 PyTorch 官网[链接](https://pytorch.org/)，根据安装的 OS 和 Cuda 版本选择合适的 libtorch 版本

Windows 操作系统

```
[https://download.pytorch.org/libtorch/cu111/libtorch-win-shared-with-deps-1.8.1%2Bcu111.zip](https://download.pytorch.org/libtorch/cu111/libtorch-win-shared-with-deps-1.8.1%2Bcu111.zip)
```

Linux 操作系统

```
Download here (Pre-cxx11 ABI):
[https://download.pytorch.org/libtorch/cu111/libtorch-shared-with-deps-1.8.1%2Bcu111.zip](https://download.pytorch.org/libtorch/cu111/libtorch-shared-with-deps-1.8.1%2Bcu111.zip)

Download here (cxx11 ABI):
[https://download.pytorch.org/libtorch/cu111/libtorch-cxx11-abi-shared-with-deps-1.8.1%2Bcu111.zip](https://download.pytorch.org/libtorch/cu111/libtorch-cxx11-abi-shared-with-deps-1.8.1%2Bcu111.zip)
```

# 将 libtorch 添加到 qt 项目中

这是最重要的，也是比较麻烦的。首先，打开 QT 项目”。pro "文件，并添加 libtorch 头文件目录(include)和库文件目录(lib)。需要添加基于 OS 平台的编译器和链接标志，否则 libtorch 不会针对 torch_cuda 进行链接，最终以“torch::cuda::is_available()”返回 0。Libtorch 建议使用 CMAKE 作为构建工具，但是您也可以使用 QMAKE for QT 实现同样的功能，但是应该小心使用标志。

```
CONFIG += c++11
#Include the headers
INCLUDEPATH += $$PYTORCH_DIR/include/torch/csrc/api/include \
               $$quote($$CUDA_DIR/include)
#compiler and linker flags
unix {
**QMAKE_LFLAGS += -Wl,--no-as-needed**
}
win32 {
**QMAKE_LFLAGS += -INCLUDE:?warp_size@cuda@at@@YAHXZ
QMAKE_LFLAGS += -INCLUDE:?searchsorted_cuda@native@at@@YA?AVTensor@2@AEBV32@0_N1@Z
QMAKE_LFLAGS += /machine:x64**
}# linking with libs
win32 {
LIBS += $$quote($$PYTORCH_DIR\lib\*.lib)
LIBS +=  -L$$quote($$PYTORCH_DIR\lib)LIBS += $$quote($$CUDA_DIR\lib\x64\*.lib)
LIBS += -L$$quote($$CUDA_DIR\v11.1\bin)
}
unix {
LIBS += -L$$PYTORCH_DIR/lib  -ltorch_cpu -ltorch  -lc10 -ltorch_cuda  -lc10_cuda -lcaffe2_observers  \
-lcaffe2_nvrtc -lcaffe2_detectron_ops_gpu  \
-lnvrtc-builtins -lprocess_group_agent -lshm  \
-ltensorpipe_agent -ltorch -ltorch_cuda_cpp -ltorch_cuda_cu -ltorch_global_deps}
```

# 用 LibTorch 构建 QT 应用程序

下一步是构建 QT 项目源代码，如果您的应用程序基于 QT 小部件，您将会得到关于插槽冲突编译错误的错误。这是由于 QT 和 libtorch 库之间的 slots 关键字冲突造成的，您可以通过在源代码开头添加 libtorch 头文件来避免这种情况，如下所示

```
#undef slots
#include "torch/torch.h"
#include "torch/jit.h"
#include "torch/nn.h"
#include "torch/script.h"
#define slots Q_SLOTS#include "mainwindow.h"
#include "ui_mainwindow.h"
#include <QList>
```

检查加载了 Cuda 功能的 Libtorch 及其配置

```
qDebug() << "Cuda Device Count" << torch::cuda::device_count();qDebug() << "cudnn_is_available" << torch::cuda::cudnn_is_available();qDebug() << "cuda::is_available" << torch::cuda::is_available();qDebug() << "cuda::show_config" << torch::show_config().c_str();
```

一旦应用程序执行，你会得到如下日志，如果有任何 GPU 存在

```
Cuda Device Count 1
cudnn_is_available true
cuda::is_available true
cuda::show_config PyTorch built with:
 - C++ Version: 199711
  - MSVC 192829913
  - Intel(R) Math Kernel Library Version 2020.0.2 Product Build 20200624 for Intel(R) 64 architecture applications
  - Intel(R) MKL-DNN v1.7.0 (Git Hash 7aed236906b1f7a05c0917e5257a1af05e9ff683)
  - OpenMP 2019
  - CPU capability usage: AVX2
  - CUDA Runtime 11.1
  - NVCC architecture flags: -gencode;arch=compute_37,code=sm_37;-gencode;arch=compute_50,code=sm_50;-gencode;arch=compute_60,code=sm_60;-gencode;arch=compute_61,code=sm_61;-gencode;arch=compute_70,code=sm_70;-gencode;arch=compute_75,code=sm_75;-gencode;arch=compute_80,code=sm_80;-gencode;arch=compute_86,code=sm_86;-gencode;arch=compute_37,code=compute_37
  - CuDNN 8.0.5
  - Magma 2.5.4
  - Build settings: BLAS_INFO=mkl, BUILD_TYPE=Release, CUDA_VERSION=11.1, CUDNN_VERSION=8.0.5, CXX_COMPILER=C:/w/b/windows/tmp_bin/sccache-cl.exe, CXX_FLAGS=/DWIN32 /D_WINDOWS /GR /EHsc /w /bigobj -DUSE_PTHREADPOOL -openmp:experimental -DNDEBUG -DUSE_FBGEMM -DUSE_XNNPACK, LAPACK_INFO=mkl, PERF_WITH_AVX=1, PERF_WITH_AVX2=1, PERF_WITH_AVX512=1, TORCH_VERSION=1.8.1, USE_CUDA=ON, USE_CUDNN=ON, USE_EXCEPTION_PTR=1, USE_GFLAGS=OFF, USE_GLOG=OFF, USE_MKL=ON, USE_MKLDNN=ON, USE_MPI=OFF, USE_NCCL=OFF, USE_NNPACK=OFF, USE_OPENMP=ON,
```

注意:需要用 MSVC 构建 QT 应用程序，因为 libtorch 不支持 windows 上的 MinGW。

# 加载 torchscript 模型并运行推理

在 lib torch 模块中加载 torch 脚本模型

```
torch::jit::script::Module aLibTorchModule = torch::jit::load("torch script model path");
```

选择 GPU 设备(如果可用),否则选择 CPU 设备

```
torch::DeviceType aDeviceType;
if (torch::cuda::is_available())
{
aDeviceType= torch::kCUDA;
}
else 
{
aDeviceType= torch::kCPU;
}
torch::Device aTorchDevice = torch::Device(aDeviceType);
```

将加载的模块移动到选择的设备，在我们的例子中是 GPU

```
aLibTorchModule.to(aTorchDevice);
```

现在，您可以在 libtorch 模块中执行推理，绕过模型的正确输入。如果您有一个打开的 cv::Mat 图像对象，Lib torch 提供了一个转换为输入张量的函数。

```
auto input_tensor = torch::from_blob(*data*, {1, Height, Width, 3}); // here data should opencv CV::MAT
```

有时，您可能需要根据模型预期对输入进行预处理，遵循 lib torch 函数允许您更改输入期限维度的顺序，并在进行实际推理之前对输入进行规范化。

```
input_tensor = input_tensor.permute({0, 3, 1, 2});
std::vector<double> norm_mean = {0.485, 0.456, 0.406};
std::vector<double> norm_std = {0.229, 0.224, 0.225};
input_tensor = torch::data::transforms::Normalize<>(norm_mean, norm_std)(input_tensor);
```

最后，在 libtorch 模块中执行推理，该模块是在开始时加载模型的。Libtorch Forward 方法将结果作为张量对象返回，您可以根据需要检索结果并操作结果。如果您的模型加载在 GPU 中，请确保您应该在处理推理之前将您的输入张量移动到 GPU，否则将会出现错误。

```
input_tensor = input_tensor.to(aTorchDevice);
torch::Tensor out_tensor = aLibTorchModule.forward({input_tensor}).toTensor();
```