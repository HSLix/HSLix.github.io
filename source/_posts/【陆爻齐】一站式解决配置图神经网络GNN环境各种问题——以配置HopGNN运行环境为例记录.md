---
title: 【深度学习】一站式解决配置图神经网络 GNN 环境各种问题——以配置 HopGNN 运行环境为例记录
tags: [Python, 机器学习, 深度学习]
index_img: /img/index/fluid_default.png
date: 2024-03-25 14:06:00
hide: false
---
# 前言
<a id="section1"></a>
当这篇文章发布时，估计成品论文已经出来了，不过本文的目的是以一个 python 菜鸟和科研新手的角度，记录配置环境时发现和解决问题的记录，希望能帮到未来的自己和遇到类似报错的你。

本文大致分为三个板块，第一个 [前言](#section1)，第二个是 [配置环境的具体记录（含各种报错）](#section2)，第三个则是 [记录我总结的配置相关环境的办法](#section3)。

**注意**
陆爻齐在后续配置其它环境时也碰到了其它的问题，相关内容会记录到 [补充内容](#section4)。

# 配置环境的具体记录
<a id="section2"></a>
## 官方提示与配置设备
配环境，首先肯定先看官方怎么说
[GitHub地址](https://github.com/JC-202/HopGNN)
无requirement，其中的Dependencies中内容是
* python 3.7.3
* pytorch 1.10.1
* dgl 0.6.0
* ogb 1.2.3
* torch-geometric 2.0.3

而程序通过bash启动
```
bash scripts/citation.sh
bash scripts/heterophily.sh
bash scripts/large.sh 
```


笔者采用anaconda3来配置环境，由于设备配有独立显卡，故决定下载cuda利用上gpu。

## 配置过程
### bash配置
第一个问题是安装的bash识别不到conda，而window的cmd可以，报错如下
`bash: conda: command not found`
故参考文章CSDN博客顺利解决：[windows 中， bash: conda: command not found（已解决）](https://blog.csdn.net/Vertira/article/details/136271584)

但是，虽然能识别 conda 指令，却不能在打开终端后直接运行 conda acivate 命令，必须用 source activate 环境来启动，后面才能直接激活环境。不过并没有太大影响，故按下不表。

### 环境配置
conda python3.7.3
pytorch 1.10.1在torch官网下
dgl，ogb，和torch-geometric都在aconda搜索下载
均为对应版本

然后运行
bash scripts/citation.sh
提示缺少模块einops，conda install后，发现版本0.7.0不匹配，故降级0.6.0
然后提示缺少模块scikit-learn，conda install 1.0.2解决
接着提示缺少模块pandas，conda install 1.3.5
提示缺少tqdm，conda install 4.64.1

现在不缺模块了，报错如下
<details> <summary>ErrorReport</summary>
$ bash scripts/citation.sh
Traceback (most recent call last):
  File "train.py", line 8, in <module>
    from datasets.dataloader import load_data
  File "D:\Code\HopGNN\datasets\dataloader.py", line 5, in <module>
    from ogb.nodeproppred import PygNodePropPredDataset
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\ogb\nodeproppred\__init__.py", line 10, in <module>
    from .dataset_dgl import DglNodePropPredDataset
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\ogb\nodeproppred\dataset_dgl.py", line 6, in <module>
    from dgl.data.utils import load_graphs, save_graphs, Subset
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\__init__.py", line 13, in <module>
    from .backend import load_backend, backend_name
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\backend\__init__.py", line 95, in <module>
    load_backend(get_preferred_backend())
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\backend\__init__.py", line 41, in load_backend
    from .._ffi.base import load_tensor_adapter # imports DGL C library
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\_ffi\base.py", line 44, in <module>
    _LIB, _LIB_NAME, _DIR_NAME = _load_lib()
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\_ffi\base.py", line 33, in _load_lib
    lib_path = libinfo.find_lib_path()
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\_ffi\libinfo.py", line 83, in find_lib_path
    raise RuntimeError(message)
RuntimeError: Cannot find the files.
List of candidates:
D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\libdgl.dll
D:\anaconda3\envs\hopgnn\lib\build\libdgl.dll
D:\anaconda3\envs\hopgnn\lib\build\Release\libdgl.dll
D:\anaconda3\envs\hopgnn\lib\lib\libdgl.dll
D:\anaconda3\envs\hopgnn\libdgl.dll
D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\dgl.dll
D:\anaconda3\envs\hopgnn\lib\build\dgl.dll
D:\anaconda3\envs\hopgnn\lib\build\Release\dgl.dll
D:\anaconda3\envs\hopgnn\lib\lib\dgl.dll
D:\anaconda3\envs\hopgnn\dgl.dll
Traceback (most recent call last):
  File "train.py", line 8, in <module>
    from datasets.dataloader import load_data
  File "D:\Code\HopGNN\datasets\dataloader.py", line 5, in <module>
    from ogb.nodeproppred import PygNodePropPredDataset
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\ogb\nodeproppred\__init__.py", line 10, in <module>
    from .dataset_dgl import DglNodePropPredDataset
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\ogb\nodeproppred\dataset_dgl.py", line 6, in <module>
    from dgl.data.utils import load_graphs, save_graphs, Subset
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\__init__.py", line 13, in <module>
    from .backend import load_backend, backend_name
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\backend\__init__.py", line 95, in <module>
    load_backend(get_preferred_backend())
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\backend\__init__.py", line 41, in load_backend
    from .._ffi.base import load_tensor_adapter # imports DGL C library
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\_ffi\base.py", line 44, in <module>
    _LIB, _LIB_NAME, _DIR_NAME = _load_lib()
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\_ffi\base.py", line 33, in _load_lib
    lib_path = libinfo.find_lib_path()
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\_ffi\libinfo.py", line 83, in find_lib_path
    raise RuntimeError(message)
RuntimeError: Cannot find the files.
List of candidates:
D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\libdgl.dll
D:\anaconda3\envs\hopgnn\lib\build\libdgl.dll
D:\anaconda3\envs\hopgnn\lib\build\Release\libdgl.dll
D:\anaconda3\envs\hopgnn\lib\lib\libdgl.dll
D:\anaconda3\envs\hopgnn\libdgl.dll
D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\dgl.dll
D:\anaconda3\envs\hopgnn\lib\build\dgl.dll
D:\anaconda3\envs\hopgnn\lib\build\Release\dgl.dll
D:\anaconda3\envs\hopgnn\lib\lib\dgl.dll
D:\anaconda3\envs\hopgnn\dgl.dll
Traceback (most recent call last):
  File "train.py", line 8, in <module>
    from datasets.dataloader import load_data
  File "D:\Code\HopGNN\datasets\dataloader.py", line 5, in <module>
    from ogb.nodeproppred import PygNodePropPredDataset
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\ogb\nodeproppred\__init__.py", line 10, in <module>
    from .dataset_dgl import DglNodePropPredDataset
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\ogb\nodeproppred\dataset_dgl.py", line 6, in <module>
    from dgl.data.utils import load_graphs, save_graphs, Subset
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\__init__.py", line 13, in <module>
    from .backend import load_backend, backend_name
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\backend\__init__.py", line 95, in <module>
    load_backend(get_preferred_backend())
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\backend\__init__.py", line 41, in load_backend
    from .._ffi.base import load_tensor_adapter # imports DGL C library
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\_ffi\base.py", line 44, in <module>
    _LIB, _LIB_NAME, _DIR_NAME = _load_lib()
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\_ffi\base.py", line 33, in _load_lib
    lib_path = libinfo.find_lib_path()
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\_ffi\libinfo.py", line 83, in find_lib_path
    raise RuntimeError(message)
RuntimeError: Cannot find the files.
List of candidates:
D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\libdgl.dll
D:\anaconda3\envs\hopgnn\lib\build\libdgl.dll
D:\anaconda3\envs\hopgnn\lib\build\Release\libdgl.dll
D:\anaconda3\envs\hopgnn\lib\lib\libdgl.dll
D:\anaconda3\envs\hopgnn\libdgl.dll
D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\dgl.dll
D:\anaconda3\envs\hopgnn\lib\build\dgl.dll
D:\anaconda3\envs\hopgnn\lib\build\Release\dgl.dll
D:\anaconda3\envs\hopgnn\lib\lib\dgl.dll
D:\anaconda3\envs\hopgnn\dgl.dll
</details>
经分析，推测为dgl版本不匹配，更新为最新版仍不匹配，经搜索发现可发现解决方案无用

所以，我将除torch外的部分包都卸载，用pip重新安装最新版（~~虽然犯了conda和pip混用的忌，但木得办法~~）
pip将torch换为1.13，ogb换为1.3.6 dgl换为2.0.0 torch-geometric换为2.3.1
然后出现了新的报错

<details><summary>ErrorReport</summary>
$ bash scripts/citation.sh
Traceback (most recent call last):
  File "train.py", line 8, in <module>
    from datasets.dataloader import load_data
  File "D:\Code\HopGNN\datasets\dataloader.py", line 8, in <module>
    from dgl.data import *
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\__init__.py", line 16, in <module>
    from . import (
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\dataloading\__init__.py", line 13, in <module>
    from .dataloader import *
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\dataloading\dataloader.py", line 27, in <module>
    from ..distributed import DistGraph
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\distributed\__init__.py", line 5, in <module>
    from .dist_graph import DistGraph, DistGraphServer, edge_split, node_split
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\distributed\dist_graph.py", line 37, in <module>
    from .partition import (
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\distributed\partition.py", line 15, in <module>
    from ..data.utils import load_graphs, load_tensors, save_graphs, save_tensors
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\data\__init__.py", line 7, in <module>
    from . import citation_graph as citegrh
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\data\citation_graph.py", line 20, in <module>
    from .dgl_dataset import DGLBuiltinDataset
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\data\dgl_dataset.py", line 12, in <module>
    from .utils import download, extract_archive, get_download_dir, makedirs
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\data\utils.py", line 14, in <module>
    import requests
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\requests\__init__.py", line 43, in <module>
    import urllib3
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\urllib3\__init__.py", line 42, in <module>
    "urllib3 v2.0 only supports OpenSSL 1.1.1+, currently "
ImportError: urllib3 v2.0 only supports OpenSSL 1.1.1+, currently the 'ssl' module is compiled with 'OpenSSL 1.1.0i  14 Aug 2018'. See: https://github.com/urllib3/urllib3/issues/2168
Traceback (most recent call last):
  File "train.py", line 8, in <module>
    from datasets.dataloader import load_data
  File "D:\Code\HopGNN\datasets\dataloader.py", line 8, in <module>
    from dgl.data import *
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\__init__.py", line 16, in <module>
    from . import (
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\dataloading\__init__.py", line 13, in <module>
    from .dataloader import *
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\dataloading\dataloader.py", line 27, in <module>
    from ..distributed import DistGraph
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\distributed\__init__.py", line 5, in <module>
    from .dist_graph import DistGraph, DistGraphServer, edge_split, node_split
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\distributed\dist_graph.py", line 37, in <module>
    from .partition import (
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\distributed\partition.py", line 15, in <module>
    from ..data.utils import load_graphs, load_tensors, save_graphs, save_tensors
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\data\__init__.py", line 7, in <module>
    from . import citation_graph as citegrh
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\data\citation_graph.py", line 20, in <module>
    from .dgl_dataset import DGLBuiltinDataset
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\data\dgl_dataset.py", line 12, in <module>
    from .utils import download, extract_archive, get_download_dir, makedirs
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\data\utils.py", line 14, in <module>
    import requests
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\requests\__init__.py", line 43, in <module>
    import urllib3
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\urllib3\__init__.py", line 42, in <module>
    "urllib3 v2.0 only supports OpenSSL 1.1.1+, currently "
ImportError: urllib3 v2.0 only supports OpenSSL 1.1.1+, currently the 'ssl' module is compiled with 'OpenSSL 1.1.0i  14 Aug 2018'. See: https://github.com/urllib3/urllib3/issues/2168
Traceback (most recent call last):
  File "train.py", line 8, in <module>
    from datasets.dataloader import load_data
  File "D:\Code\HopGNN\datasets\dataloader.py", line 8, in <module>
    from dgl.data import *
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\__init__.py", line 16, in <module>
    from . import (
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\dataloading\__init__.py", line 13, in <module>
    from .dataloader import *
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\dataloading\dataloader.py", line 27, in <module>
    from ..distributed import DistGraph
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\distributed\__init__.py", line 5, in <module>
    from .dist_graph import DistGraph, DistGraphServer, edge_split, node_split
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\distributed\dist_graph.py", line 37, in <module>
    from .partition import (
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\distributed\partition.py", line 15, in <module>
    from ..data.utils import load_graphs, load_tensors, save_graphs, save_tensors
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\data\__init__.py", line 7, in <module>
    from . import citation_graph as citegrh
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\data\citation_graph.py", line 20, in <module>
    from .dgl_dataset import DGLBuiltinDataset
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\data\dgl_dataset.py", line 12, in <module>
    from .utils import download, extract_archive, get_download_dir, makedirs
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\dgl\data\utils.py", line 14, in <module>
    import requests
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\requests\__init__.py", line 43, in <module>
    import urllib3
  File "D:\anaconda3\envs\hopgnn\lib\site-packages\urllib3\__init__.py", line 42, in <module>
    "urllib3 v2.0 only supports OpenSSL 1.1.1+, currently "
ImportError: urllib3 v2.0 only supports OpenSSL 1.1.1+, currently the 'ssl' module is compiled with 'OpenSSL 1.1.0i  14 Aug 2018'. See: https://github.com/urllib3/urllib3/issues/2168
</details>

经查找，执行pip install urllib3==1.26.15，解决
然后又出现`ModuleNotFoundError: No module named 'torch_sparse'`，故pip安装torchsparse
`ModuleNotFoundError: No module named 'torch_scatter'` 故pip安装torchscatter
`ModuleNotFoundError: No module named 'google_drive_downloader'` 故pip安装

再次运行，这次报错`AssertionError: Torch not compiled with CUDA enabled`
然后发现torch版本不对，发现下的是cpu版本，故删除重下，pytroch1.13.1 cuda11.7 cudacnn8.9.7参照适配表应不冲突

于是在配置正确的torch sca什么的之类后，成功跑通前两个，但第三个出现错误如下
`ValueError: Cannot load file containing pickled data when allow_pickle=False`

经把对应地方 allow pickle变true后，错误变成了
`OSError Failed to interpret file as a pickle`

经查询没有什么可用的办法，于是接下来八小时尝试了配置与要求环境一摸一样的环境，配置python版本更低的环境（参照一些github issue的回答，回退版本可能解决），但还是无果

中间出现报错
`RuntimeError: Not compiled with CUDA support`
参考博客解决[RuntimeError: Not compiled with CUDA support此类错误解决方案](https://blog.csdn.net/qq_44501429/article/details/129178459)

在经过学长解放思想，指点后，秉承能跑起来的就是好环境的原则，将python3.7更为python3.8，如此，便能下载更新的pytorch2.*版本。

但仍没有突破，于是查到之前的错误可能是下载文件不完整，于是删掉库重新clone并下载文件后，又出现新的错误报告
`AttributeError: module numpy has no attribute int`
参考文章 [AttributeError: module numpy has no attribute int .报错解决方案](https://blog.csdn.net/weixin_46669612/article/details/129624331)
经查询，numpy回退1.22.2后解决，这下彻底解决

同时又用之前的python3.7试着跑第三条，仍然报错，说明还是要结合python3.8以上版本的这个环境

# 配置环境参考流程（运用Nvidia GPU，pytorch、pyg的环境）
<a id="section3"></a>

首先是确认，设备有 nvidia 显卡
然后查看是否满足配置 CUDA 的环境，nvidia 控制面板-系统信息-组件中的 3d 设置右侧产品名称的右侧有 CUDA 后面一串数字，这就是自己电脑支持的 CUDA 版本，一般选择配套的，作者有时候会选择略低于这个的版本，不能高于。具体情况视情况而定。
接着结合自己的需求和 [pytorch与cuda版本对应关系汇总](https://blog.csdn.net/u011489887/article/details/135250561) 来确定要下载的CUDA版本和pytorch版本
再到 [CUDA官网](https://developer.nvidia.com/cuda-toolkit-archive) 下载cuda版本，安装用自定义，一般只用下CUDA即可，安装后配置环境变量并测试。此处安装程序开始选择的文件夹是临时安装文件夹，安装完成后会自行删除。
再到下载cuDNN，根据对应版本在 [cuDNN官网](https://developer.nvidia.com/rdp/cudnn-archive) 下载即可（第一次下载需要注册账号），下载后将文件夹的bin，include，lib三个文件夹“替换”（直接拷贝即可）CUDA安装位置内的同名文件夹，再配置对应环境变量后验证
下面到 [pytorch官网](https://pytorch.org/get-started/previous-versions/#conda) 下载对应 torch 版本，也可以自行在这个[网站](http://download.pytorch.org/whl/torch_stable.html)下载（笔者偏向使用后者）
最后，如果要用到 pyg，就可以参考自己的 pytorch 版本和 CUDA 版本，在 [该网站](https://data.pyg.org/whl/) 按需下载库，然后直接 pip install torch-geometric。（应该 pyg 下最新就行）
PS：cp38 代表适配 python 3.8

对于多个 CUDA 版本的管理，笔者参考 [这篇博客](https://blog.csdn.net/qq_50677040/article/details/132131346)

参考博客如下，后面再用我自己的话做个综合 
[pytorch1.13安装](https://blog.csdn.net/by99031433/article/details/127721199) 
[window11系统CUDA、cuDNN 安装以及环境变量配置](https://blog.csdn.net/shdabai/article/details/131248257)

# 补充内容
<a id="section4"></a>
## dgl
dgl 也分 cpu 和 gpu 版本，可以到 [官网](https://www.dgl.ai/pages/start.html) 找到对应的下载方式，当然也有类似 torch 的离线下载方式，在这个 [网站](https://data.dgl.ai/wheels/repo.html)。**注意**，该网站对 cuda 的 dgl 版本较为落后，最新不过 1.0.0，想要更新的 cuda 版本需要略微改动网址，该网址为 https://data.dgl.ai/wheels/repo.html ，只要改成 https://data.dgl.ai/wheels/cu116/repo.html 就能获取到 1.0.0 以上的 cuda11.6 的 dgl，你学废了吗：）

## 报错 Device API gpu is not enabled. Please install the cuda version of dgl
可以尝试回退dgl对应的cuda版本，参照[该文章](https://zhuanlan.zhihu.com/p/434494642)cuda11.3装 dgl-cuda113 报错，可以装 dgl-cuda110 试试

## 报错 RuntimeError: Could not infer dtype of numpy.int64
如果最后一条语句来自 dgl，很可能是因为 numpy 版本过新，即 numpy 的 2.* 版本，回退到 1.* 的版本即可解决

参考 [GitHub](https://github.com/DLR-RM/stable-baselines3/issues/1960)

## 报错 AttributeError: module 'dgl.function' has no attribute 'copy_src'
该报错是因为 dgl 版本过新，该函数自 1.* 版本开始就删掉了，回退之前版本即可解决，比如 0.9.1

参考 [GitHub](https://github.com/XingyanLiu/CAME/issues/24)


## 多个 CUDA 可能发生的环境 CUDA 不变化问题
通过修改环境变量 Path 几个 CUDA 的顺序确实能改变在命令行窗口输入`nvcc -V`显示的版本号，不过，一旦 conda create 了一个环境，那么环境里的 CUDA 就固定了，不可更换（至少此时笔者还不知如何更换）

## 离线 pip 安装 torch 问题报错
```
WARNING: Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError(SSLError(1, '[SSL: KRB5_S_TKT_NYV] unexpected eof while reading (_ssl.c:1091)'))': /simple/typing-extensions/
WARNING: Retrying (Retry(total=3, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError(SSLError(1, '[SSL: KRB5_S_TKT_NYV] unexpected eof while reading (_ssl.c:1091)'))': /simple/typing-extensions/
WARNING: Retrying (Retry(total=2, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError(SSLError(1, '[SSL: KRB5_S_TKT_NYV] unexpected eof while reading (_ssl.c:1091)'))': /simple/typing-extensions/
WARNING: Retrying (Retry(total=1, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError(SSLError(1, '[SSL: KRB5_S_TKT_NYV] unexpected eof while reading (_ssl.c:1091)'))': /simple/typing-extensions/
WARNING: Retrying (Retry(total=0, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError(SSLError(1, '[SSL: KRB5_S_TKT_NYV] unexpected eof while reading (_ssl.c:1091)'))': /simple/typing-extensions/
Could not fetch URL https://pypi.org/simple/typing-extensions/: There was a problem confirming the ssl certificate: HTTPSConnectionPool(host='pypi.org', port=443): Max retries exceeded with url: /simple/typing-extensions/ (Caused by SSLError(SSLError(1, '[SSL: KRB5_S_TKT_NYV] unexpected eof while reading (_ssl.c:1091)'))) - skipping
INFO: pip is looking at multiple versions of torch to determine which version is compatible with other requirements. This could take a while.
ERROR: Could not find a version that satisfies the requirement typing-extensions (from torch) (from versions: none)
ERROR: No matching distribution found for typing-extensions
```
很显然，这是网络问题，笔者在关闭梯子后问题消失

# 小结
小小 Python 环境配得汗流浃背，本以为此前经历了 LALC 的磨练能顺利些，不曾想如此“有意思”，希望本文的记录有所帮助

