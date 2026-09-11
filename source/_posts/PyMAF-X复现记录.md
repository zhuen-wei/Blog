---
title: PyMAF-X模型完整复现记录
author: 牧丛
excerpt: 笔者从搭建环境开始复现了PyMAF-X模型
index_img: /cover/cover6.jpg
data: 2025/9/22 13:00
categories: SMPL系列
tags:
  - SMPL
  - PyMAF-X
  - 三维人体模型重建
  - 流程记录
toc: true
math: true
---

# PyMAF-X的复现记录

创建全新环境，命名为PyMAF-X，并激活

```bash
conda create -n PyMAF-X python==3.10.18
conda activate PyMAF-X
```

结果：

```bash
Collecting package metadata (current_repodata.json): done
Solving environment: done

==> WARNING: A newer version of conda exists. <==
  current version: 4.9.2
  latest version: 25.7.0

Please update conda by running

    $ conda update -n base -c defaults conda

## Package Plan ##

  environment location: /data1/supersmpl/wzn/.conda/envs/PyMAF-X

  added / updated specs:
    - python==3.10.18

The following NEW packages will be INSTALLED:

  _libgcc_mutex      pkgs/main/linux-64::_libgcc_mutex-0.1-main
  _openmp_mutex      pkgs/main/linux-64::_openmp_mutex-5.1-1_gnu
  bzip2              pkgs/main/linux-64::bzip2-1.0.8-h5eee18b_6
  ca-certificates    pkgs/main/linux-64::ca-certificates-2025.7.15-h06a4308_0
  expat              pkgs/main/linux-64::expat-2.7.1-h6a678d5_0
  ld_impl_linux-64   pkgs/main/linux-64::ld_impl_linux-64-2.40-h12ee557_0
  libffi             pkgs/main/linux-64::libffi-3.4.4-h6a678d5_1
  libgcc-ng          pkgs/main/linux-64::libgcc-ng-11.2.0-h1234567_1
  libgomp            pkgs/main/linux-64::libgomp-11.2.0-h1234567_1
  libstdcxx-ng       pkgs/main/linux-64::libstdcxx-ng-11.2.0-h1234567_1
  libuuid            pkgs/main/linux-64::libuuid-1.41.5-h5eee18b_0
  libxcb             pkgs/main/linux-64::libxcb-1.17.0-h9b100fa_0
  ncurses            pkgs/main/linux-64::ncurses-6.5-h7934f7d_0
  openssl            pkgs/main/linux-64::openssl-3.0.17-h5eee18b_0
  pip                pkgs/main/noarch::pip-25.1-pyhc872135_2
  pthread-stubs      pkgs/main/linux-64::pthread-stubs-0.3-h0ce48e5_1
  python             pkgs/main/linux-64::python-3.10.18-h1a3bd86_0
  readline           pkgs/main/linux-64::readline-8.3-hc2a1206_0
  setuptools         pkgs/main/linux-64::setuptools-78.1.1-py310h06a4308_0
  sqlite             pkgs/main/linux-64::sqlite-3.50.2-hb25bd0a_1
  tk                 pkgs/main/linux-64::tk-8.6.15-h54e0aa7_0
  tzdata             pkgs/main/noarch::tzdata-2025b-h04d1e81_0
  wheel              pkgs/main/linux-64::wheel-0.45.1-py310h06a4308_0
  xorg-libx11        pkgs/main/linux-64::xorg-libx11-1.8.12-h9b100fa_1
  xorg-libxau        pkgs/main/linux-64::xorg-libxau-1.0.12-h9b100fa_0
  xorg-libxdmcp      pkgs/main/linux-64::xorg-libxdmcp-1.1.5-h9b100fa_0
  xorg-xorgproto     pkgs/main/linux-64::xorg-xorgproto-2024.1-h5eee18b_1
  xz                 pkgs/main/linux-64::xz-5.6.4-h5eee18b_1
  zlib               pkgs/main/linux-64::zlib-1.2.13-h5eee18b_1

Proceed ([y]/n)? y

Preparing transaction: done
Verifying transaction: done
Executing transaction: done
#
# To activate this environment, use
#
#     $ conda activate PyMAF-X
#
# To deactivate an active environment, use
#
#     $ conda deactivate
```

环境创建成功，同时主环境由base变为PyMAF-X:

原：

```bash
(base) wzn@amax:~$
```

现：

```bash
(PyMAF-X) wzn@amax:~$
```

- 安装2.3.1版本的torch和11.8版本的cuda，并安装pytorch3d（需要挂VPN）

```bash
export https_proxy=http://127.0.0.1:7890 http_proxy=http://127.0.0.1:7890 all_proxy=socks5://127.0.0.1:7891
pip install torch==2.3.1+cu118 torchvision==0.18.1+cu118 -f <https://download.pytorch.org/whl/torch_stable.html>
```

结果：

```bash
Looking in links: <https://download.pytorch.org/whl/torch_stable.html>
Collecting torch==2.3.1+cu118
  Using cached <https://download.pytorch.org/whl/cu118/torch-2.3.1%2Bcu118-cp310-cp310-linux_x86_64.whl> (839.7 MB)
Collecting torchvision==0.18.1+cu118
  Using cached <https://download.pytorch.org/whl/cu118/torchvision-0.18.1%2Bcu118-cp310-cp310-linux_x86_64.whl> (6.3 MB)
Collecting filelock (from torch==2.3.1+cu118)
  Using cached filelock-3.19.1-py3-none-any.whl.metadata (2.1 kB)
Collecting typing-extensions>=4.8.0 (from torch==2.3.1+cu118)
  Using cached typing_extensions-4.15.0-py3-none-any.whl.metadata (3.3 kB)
Collecting sympy (from torch==2.3.1+cu118)
  Using cached sympy-1.14.0-py3-none-any.whl.metadata (12 kB)
Collecting networkx (from torch==2.3.1+cu118)
  Using cached networkx-3.4.2-py3-none-any.whl.metadata (6.3 kB)
Collecting jinja2 (from torch==2.3.1+cu118)
  Using cached jinja2-3.1.6-py3-none-any.whl.metadata (2.9 kB)
Collecting fsspec (from torch==2.3.1+cu118)
  Using cached fsspec-2025.7.0-py3-none-any.whl.metadata (12 kB)
Collecting nvidia-cuda-nvrtc-cu11==11.8.89 (from torch==2.3.1+cu118)
  Using cached nvidia_cuda_nvrtc_cu11-11.8.89-py3-none-manylinux2014_x86_64.whl.metadata (1.5 kB)
Collecting nvidia-cuda-runtime-cu11==11.8.89 (from torch==2.3.1+cu118)
  Using cached nvidia_cuda_runtime_cu11-11.8.89-py3-none-manylinux2014_x86_64.whl.metadata (1.5 kB)
Collecting nvidia-cuda-cupti-cu11==11.8.87 (from torch==2.3.1+cu118)
  Using cached nvidia_cuda_cupti_cu11-11.8.87-py3-none-manylinux2014_x86_64.whl.metadata (1.6 kB)
Collecting nvidia-cudnn-cu11==8.7.0.84 (from torch==2.3.1+cu118)
  Using cached nvidia_cudnn_cu11-8.7.0.84-py3-none-manylinux1_x86_64.whl.metadata (1.6 kB)
Collecting nvidia-cublas-cu11==11.11.3.6 (from torch==2.3.1+cu118)
  Using cached nvidia_cublas_cu11-11.11.3.6-py3-none-manylinux2014_x86_64.whl.metadata (1.5 kB)
Collecting nvidia-cufft-cu11==10.9.0.58 (from torch==2.3.1+cu118)
  Using cached nvidia_cufft_cu11-10.9.0.58-py3-none-manylinux2014_x86_64.whl.metadata (1.5 kB)
Collecting nvidia-curand-cu11==10.3.0.86 (from torch==2.3.1+cu118)
  Using cached nvidia_curand_cu11-10.3.0.86-py3-none-manylinux2014_x86_64.whl.metadata (1.5 kB)
Collecting nvidia-cusolver-cu11==11.4.1.48 (from torch==2.3.1+cu118)
  Using cached nvidia_cusolver_cu11-11.4.1.48-py3-none-manylinux2014_x86_64.whl.metadata (1.6 kB)
Collecting nvidia-cusparse-cu11==11.7.5.86 (from torch==2.3.1+cu118)
  Using cached nvidia_cusparse_cu11-11.7.5.86-py3-none-manylinux2014_x86_64.whl.metadata (1.5 kB)
Collecting nvidia-nccl-cu11==2.20.5 (from torch==2.3.1+cu118)
  Using cached nvidia_nccl_cu11-2.20.5-py3-none-manylinux2014_x86_64.whl.metadata (1.8 kB)
Collecting nvidia-nvtx-cu11==11.8.86 (from torch==2.3.1+cu118)
  Using cached nvidia_nvtx_cu11-11.8.86-py3-none-manylinux2014_x86_64.whl.metadata (1.7 kB)
Collecting triton==2.3.1 (from torch==2.3.1+cu118)
  Using cached triton-2.3.1-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (1.4 kB)
Collecting numpy (from torchvision==0.18.1+cu118)
  Using cached numpy-2.2.6-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (62 kB)
Collecting pillow!=8.3.*,>=5.3.0 (from torchvision==0.18.1+cu118)
  Using cached pillow-11.3.0-cp310-cp310-manylinux_2_27_x86_64.manylinux_2_28_x86_64.whl.metadata (9.0 kB)
Collecting MarkupSafe>=2.0 (from jinja2->torch==2.3.1+cu118)
  Using cached MarkupSafe-3.0.2-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (4.0 kB)
Collecting mpmath<1.4,>=1.1.0 (from sympy->torch==2.3.1+cu118)
  Using cached mpmath-1.3.0-py3-none-any.whl.metadata (8.6 kB)
Using cached nvidia_cublas_cu11-11.11.3.6-py3-none-manylinux2014_x86_64.whl (417.9 MB)
Using cached nvidia_cuda_cupti_cu11-11.8.87-py3-none-manylinux2014_x86_64.whl (13.1 MB)
Using cached nvidia_cuda_nvrtc_cu11-11.8.89-py3-none-manylinux2014_x86_64.whl (23.2 MB)
Using cached nvidia_cuda_runtime_cu11-11.8.89-py3-none-manylinux2014_x86_64.whl (875 kB)
Using cached nvidia_cudnn_cu11-8.7.0.84-py3-none-manylinux1_x86_64.whl (728.5 MB)
Using cached nvidia_cufft_cu11-10.9.0.58-py3-none-manylinux2014_x86_64.whl (168.4 MB)
Using cached nvidia_curand_cu11-10.3.0.86-py3-none-manylinux2014_x86_64.whl (58.1 MB)
Using cached nvidia_cusolver_cu11-11.4.1.48-py3-none-manylinux2014_x86_64.whl (128.2 MB)
Using cached nvidia_cusparse_cu11-11.7.5.86-py3-none-manylinux2014_x86_64.whl (204.1 MB)
Using cached nvidia_nccl_cu11-2.20.5-py3-none-manylinux2014_x86_64.whl (142.9 MB)
Using cached nvidia_nvtx_cu11-11.8.86-py3-none-manylinux2014_x86_64.whl (99 kB)
Using cached triton-2.3.1-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (168.1 MB)
Using cached pillow-11.3.0-cp310-cp310-manylinux_2_27_x86_64.manylinux_2_28_x86_64.whl (6.6 MB)
Using cached typing_extensions-4.15.0-py3-none-any.whl (44 kB)
Using cached filelock-3.19.1-py3-none-any.whl (15 kB)
Using cached fsspec-2025.7.0-py3-none-any.whl (199 kB)
Using cached jinja2-3.1.6-py3-none-any.whl (134 kB)
Using cached MarkupSafe-3.0.2-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (20 kB)
Using cached networkx-3.4.2-py3-none-any.whl (1.7 MB)
Using cached numpy-2.2.6-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (16.8 MB)
Using cached sympy-1.14.0-py3-none-any.whl (6.3 MB)
Using cached mpmath-1.3.0-py3-none-any.whl (536 kB)
Installing collected packages: mpmath, typing-extensions, sympy, pillow, nvidia-nvtx-cu11, nvidia-nccl-cu11, nvidia-cusparse-cu11, nvidia-curand-cu11, nvidia-cufft-cu11, nvidia-cuda-runtime-cu11, nvidia-cuda-nvrtc-cu11, nvidia-cuda-cupti-cu11, nvidia-cublas-cu11, numpy, networkx, MarkupSafe, fsspec, filelock, triton, nvidia-cusolver-cu11, nvidia-cudnn-cu11, jinja2, torch, torchvision
Successfully installed MarkupSafe-3.0.2 filelock-3.19.1 fsspec-2025.7.0 jinja2-3.1.6 mpmath-1.3.0 networkx-3.4.2 numpy-2.2.6 nvidia-cublas-cu11-11.11.3.6 nvidia-cuda-cupti-cu11-11.8.87 nvidia-cuda-nvrtc-cu11-11.8.89 nvidia-cuda-runtime-cu11-11.8.89 nvidia-cudnn-cu11-8.7.0.84 nvidia-cufft-cu11-10.9.0.58 nvidia-curand-cu11-10.3.0.86 nvidia-cusolver-cu11-11.4.1.48 nvidia-cusparse-cu11-11.7.5.86 nvidia-nccl-cu11-2.20.5 nvidia-nvtx-cu11-11.8.86 pillow-11.3.0 sympy-1.14.0 torch-2.3.1+cu118 torchvision-0.18.1+cu118 triton-2.3.1 typing-extensions-4.15.0
```

这段时间2min左右

检验安装结果：

```bash
pip list
Package                  Version
------------------------ ------------
filelock                 3.19.1
fsspec                   2025.7.0
Jinja2                   3.1.6
MarkupSafe               3.0.2
mpmath                   1.3.0
networkx                 3.4.2
numpy                    2.2.6
nvidia-cublas-cu11       11.11.3.6
nvidia-cuda-cupti-cu11   11.8.87
nvidia-cuda-nvrtc-cu11   11.8.89
nvidia-cuda-runtime-cu11 11.8.89
nvidia-cudnn-cu11        8.7.0.84
nvidia-cufft-cu11        10.9.0.58
nvidia-curand-cu11       10.3.0.86
nvidia-cusolver-cu11     11.4.1.48
nvidia-cusparse-cu11     11.7.5.86
nvidia-nccl-cu11         2.20.5
nvidia-nvtx-cu11         11.8.86
pillow                   11.3.0
pip                      25.1
setuptools               78.1.1
sympy                    1.14.0
torch                    2.3.1+cu118
torchvision              0.18.1+cu118
triton                   2.3.1
typing_extensions        4.15.0
wheel                    0.45.1
```

安装pytorch3d:

```bash
pip install "git+https://github.com/facebookresearch/pytorch3d.git"
```

结果：

```bash
Collecting git+https://github.com/facebookresearch/pytorch3d.git
  Cloning <https://github.com/facebookresearch/pytorch3d.git> to /tmp/pip-req-build-u5th6tzj
  Running command git clone --filter=blob:none --quiet <https://github.com/facebookresearch/pytorch3d.git> /tmp/pip-req-build-u5th6tzj
  Resolved <https://github.com/facebookresearch/pytorch3d.git> to commit dd068703d1182256c7bacf3eb6014f24c1190369
  Preparing metadata (setup.py) ... done
Collecting iopath (from pytorch3d==0.7.8)
  Using cached iopath-0.1.10-py3-none-any.whl
Collecting tqdm (from iopath->pytorch3d==0.7.8)
  Using cached tqdm-4.67.1-py3-none-any.whl.metadata (57 kB)
Requirement already satisfied: typing_extensions in ./.conda/envs/PyMAF-X/lib/python3.10/site-packages (from iopath->pytorch3d==0.7.8) (4.15.0)
Collecting portalocker (from iopath->pytorch3d==0.7.8)
  Using cached portalocker-3.2.0-py3-none-any.whl.metadata (8.7 kB)
Using cached portalocker-3.2.0-py3-none-any.whl (22 kB)
Using cached tqdm-4.67.1-py3-none-any.whl (78 kB)
Building wheels for collected packages: pytorch3d
  DEPRECATION: Building 'pytorch3d' using the legacy setup.py bdist_wheel mechanism, which will be removed in a future version. pip 25.3 will enforce this behaviour change. A possible replacement is to use the standardized build interface by setting the `--use-pep517` option, (possibly combined with `--no-build-isolation`), or adding a `pyproject.toml` file to the source tree of 'pytorch3d'. Discussion can be found at <https://github.com/pypa/pip/issues/6334>
  Building wheel for pytorch3d (setup.py) ... done
  Created wheel for pytorch3d: filename=pytorch3d-0.7.8-cp310-cp310-linux_x86_64.whl size=6288053 sha256=1badce2ff7365823c8370b0872effed5dca2a9bb6803604d8e6cecfa134fd42e
  Stored in directory: /tmp/pip-ephem-wheel-cache-a3e47h4t/wheels/dd/74/cc/b9266c863f19026f796e59a04e1cd9eb3754474a52ce1b66ce
Successfully built pytorch3d
Installing collected packages: tqdm, portalocker, iopath, pytorch3d
Successfully installed iopath-0.1.10 portalocker-3.2.0 pytorch3d-0.7.8 tqdm-4.67.1
```

此过程大概2min左右

P.S：Python，torch和cuda的版本都需要和上述一致，一开始我选择的python版本为3.8就会不兼容，会发生错误

下载PyMAF-X文件夹，安装requirement.txt里面的安装包

在bash中使用如下代码：

```bash
git clone <https://github.com/HongwenZhang/PyMAF-X.git>
```

结果：

```bash
Cloning into 'PyMAF-X'...
remote: Enumerating objects: 424, done.
remote: Counting objects: 100% (424/424), done.
remote: Compressing objects: 100% (202/202), done.
remote: Total 424 (delta 238), reused 396 (delta 212), pack-reused 0 (from 0)
Receiving objects: 100% (424/424), 7.25 MiB | 4.80 MiB/s, done.
Resolving deltas: 100% (238/238), done.
```

此时下载的文件里面是没有data和example的，文件配置如下：

![](/picture/PyMAF-X1.jpg)

- 通过https://pan.bnu.edu.cn/l/81f0v6下载data文件中的share_data.zip，下载下来解压之后里面就是example和data两个文件夹（里面的文件已经给你全部配好了，不需要额外操作）

将下载的两个文件转移到PyMAF-X文件夹中，获得的目录如下：

![](/picture/PyMAF-X2.jpg)

运行下面的代码：

```bash
pip install -r requirements.txt
```

出现错误：

```bash
ERROR: Could not open requirements file: [Errno 2] No such file or directory: 'requirements.txt'
```

错误原因：目录位置不对，应该将bash的位置移动到PyMAF-X文件夹下

解决代码：

```bash
cd PyMAF-X/
```

效果：

```bash
(PyMAF-X) wzn@amax:~$ cd PyMAF-X/
(PyMAF-X) wzn@amax:~/PyMAF-X$ 
```

再次尝试安装requirements.txt

出现错误：

```bash
Requirement already satisfied: numpy in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from -r requirements.txt (line 1)) (2.2.6)
Collecting scikit-image (from -r requirements.txt (line 2))
  Using cached scikit_image-0.25.2-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (14 kB)
Collecting scipy (from -r requirements.txt (line 3))
  Using cached scipy-1.15.3-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (61 kB)
Collecting sklearn (from -r requirements.txt (line 4))
  Using cached sklearn-0.0.post12.tar.gz (2.6 kB)
  Preparing metadata (setup.py) ... error
  error: subprocess-exited-with-error
  
  × python setup.py egg_info did not run successfully.
  │ exit code: 1
  ╰─> [15 lines of output]
      The 'sklearn' PyPI package is deprecated, use 'scikit-learn'
      rather than 'sklearn' for pip commands.
      
      Here is how to fix this error in the main use cases:
      - use 'pip install scikit-learn' rather than 'pip install sklearn'
      - replace 'sklearn' by 'scikit-learn' in your pip requirements files
        (requirements.txt, setup.py, setup.cfg, Pipfile, etc ...)
      - if the 'sklearn' package is used by one of your dependencies,
        it would be great if you take some time to track which package uses
        'sklearn' instead of 'scikit-learn' and report it to their issue tracker
      - as a last resort, set the environment variable
        SKLEARN_ALLOW_DEPRECATED_SKLEARN_PACKAGE_INSTALL=True to avoid this error
      
      More information is available at
      <https://github.com/scikit-learn/sklearn-pypi-package>
      [end of output]
  
  note: This error originates from a subprocess, and is likely not a problem with pip.
error: metadata-generation-failed

× Encountered error while generating package metadata.
╰─> See above for output.

note: This is an issue with the package mentioned above, not pip.
hint: See above for details.
(PyMAF-X) wzn@amax:~/PyMAF-X$ pip install -r requirements.txt
Requirement already satisfied: numpy in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from -r requirements.txt (line 1)) (2.2.6)
Collecting scikit-image (from -r requirements.txt (line 2))
  Using cached scikit_image-0.25.2-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (14 kB)
Collecting scipy (from -r requirements.txt (line 3))
  Using cached scipy-1.15.3-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (61 kB)
Collecting sklearn (from -r requirements.txt (line 4))
  Using cached sklearn-0.0.post12.tar.gz (2.6 kB)
  Preparing metadata (setup.py) ... error
  error: subprocess-exited-with-error
  
  × python setup.py egg_info did not run successfully.
  │ exit code: 1
  ╰─> [15 lines of output]
      The 'sklearn' PyPI package is deprecated, use 'scikit-learn'
      rather than 'sklearn' for pip commands.
      
      Here is how to fix this error in the main use cases:
      - use 'pip install scikit-learn' rather than 'pip install sklearn'
      - replace 'sklearn' by 'scikit-learn' in your pip requirements files
        (requirements.txt, setup.py, setup.cfg, Pipfile, etc ...)
      - if the 'sklearn' package is used by one of your dependencies,
        it would be great if you take some time to track which package uses
        'sklearn' instead of 'scikit-learn' and report it to their issue tracker
      - as a last resort, set the environment variable
        SKLEARN_ALLOW_DEPRECATED_SKLEARN_PACKAGE_INSTALL=True to avoid this error
      
      More information is available at
      <https://github.com/scikit-learn/sklearn-pypi-package>
      [end of output]
  
  note: This error originates from a subprocess, and is likely not a problem with pip.
error: metadata-generation-failed

× Encountered error while generating package metadata.
╰─> See above for output.

note: This is an issue with the package mentioned above, not pip.
hint: See above for details.
```

解决方法：将requirement.txt文件里面的sklearn换成scikit-learn

再次运行安装代码，再次出现错误：

```bash
Collecting openpifpaf==0.13.1 (from -r requirements.txt (line 26))
  Using cached openpifpaf-0.13.1.tar.gz (223 kB)
  Installing build dependencies ... error
  error: subprocess-exited-with-error
  
  × pip subprocess to install build dependencies did not run successfully.
  │ exit code: 1
  ╰─> [4 lines of output]
      Collecting setuptools
        Using cached setuptools-80.9.0-py3-none-any.whl.metadata (6.6 kB)
      ERROR: Could not find a version that satisfies the requirement torch==1.9.0 (from versions: 1.11.0, 1.12.0, 1.12.1, 1.13.0, 1.13.1, 2.0.0, 2.0.1, 2.1.0, 2.1.1, 2.1.2, 2.2.0, 2.2.1, 2.2.2, 2.3.0, 2.3.1, 2.4.0, 2.4.1, 2.5.0, 2.5.1, 2.6.0, 2.7.0, 2.7.1, 2.8.0)
      ERROR: No matching distribution found for torch==1.9.0
      [end of output]
  
  note: This error originates from a subprocess, and is likely not a problem with pip.
error: subprocess-exited-with-error

× pip subprocess to install build dependencies did not run successfully.
│ exit code: 1
╰─> See above for output.

note: This error originates from a subprocess, and is likely not a problem with pip.
```

解决方法：在requirement.txt文件中注释掉openpifpaf

再次尝试安装

成功，结果如下：

```bash
Requirement already satisfied: numpy in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from -r requirements.txt (line 1)) (2.2.6)
Collecting scikit-image (from -r requirements.txt (line 2))
  Using cached scikit_image-0.25.2-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (14 kB)
Collecting scipy (from -r requirements.txt (line 3))
  Using cached scipy-1.15.3-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (61 kB)
Collecting scikit-learn (from -r requirements.txt (line 4))
  Using cached scikit_learn-1.7.1-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.whl.metadata (11 kB)
Collecting smplx==0.1.28 (from -r requirements.txt (line 5))
  Using cached smplx-0.1.28-py3-none-any.whl.metadata (10 kB)
Requirement already satisfied: tqdm in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from -r requirements.txt (line 6)) (4.67.1)
Collecting yacs (from -r requirements.txt (line 7))
  Using cached yacs-0.1.8-py3-none-any.whl.metadata (639 bytes)
Collecting numba (from -r requirements.txt (line 8))
  Using cached numba-0.61.2-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.whl.metadata (2.8 kB)
Collecting opencv-python (from -r requirements.txt (line 9))
  Using cached opencv_python-4.12.0.88-cp37-abi3-manylinux2014_x86_64.manylinux_2_17_x86_64.whl.metadata (19 kB)
Collecting tensorboardx (from -r requirements.txt (line 10))
  Using cached tensorboardx-2.6.4-py3-none-any.whl.metadata (6.2 kB)
Collecting filterpy (from -r requirements.txt (line 11))
  Using cached filterpy-1.4.5-py3-none-any.whl
Collecting cython (from -r requirements.txt (line 12))
  Using cached cython-3.1.3-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl.metadata (4.7 kB)
Collecting chumpy (from -r requirements.txt (line 13))
  Using cached chumpy-0.70-py3-none-any.whl
Requirement already satisfied: Pillow in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from -r requirements.txt (line 14)) (11.3.0)
Collecting trimesh (from -r requirements.txt (line 15))
  Using cached trimesh-4.7.4-py3-none-any.whl.metadata (18 kB)
Collecting pyrender (from -r requirements.txt (line 16))
  Using cached pyrender-0.1.45-py3-none-any.whl.metadata (1.5 kB)
Collecting matplotlib (from -r requirements.txt (line 17))
  Using cached matplotlib-3.10.6-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.whl.metadata (11 kB)
Collecting json_tricks (from -r requirements.txt (line 18))
  Using cached json_tricks-3.17.3-py2.py3-none-any.whl.metadata (16 kB)
Collecting torchgeometry (from -r requirements.txt (line 19))
  Using cached torchgeometry-0.1.2-py2.py3-none-any.whl.metadata (2.9 kB)
Collecting einops (from -r requirements.txt (line 20))
  Using cached einops-0.8.1-py3-none-any.whl.metadata (13 kB)
Collecting joblib (from -r requirements.txt (line 21))
  Using cached joblib-1.5.2-py3-none-any.whl.metadata (5.6 kB)
Collecting boto3 (from -r requirements.txt (line 22))
  Using cached boto3-1.40.21-py3-none-any.whl.metadata (6.7 kB)
Collecting requests (from -r requirements.txt (line 23))
  Using cached requests-2.32.5-py3-none-any.whl.metadata (4.9 kB)
Collecting easydict (from -r requirements.txt (line 24))
  Using cached easydict-1.13-py3-none-any.whl.metadata (4.2 kB)
Collecting pycocotools (from -r requirements.txt (line 25))
  Using cached pycocotools-2.0.10-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (1.3 kB)
Collecting kornia (from -r requirements.txt (line 27))
  Using cached kornia-0.8.1-py2.py3-none-any.whl.metadata (17 kB)
Requirement already satisfied: torch>=1.0.1.post2 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from smplx==0.1.28->-r requirements.txt (line 5)) (2.3.1+cu118)
Requirement already satisfied: networkx>=3.0 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from scikit-image->-r requirements.txt (line 2)) (3.4.2)
Collecting imageio!=2.35.0,>=2.33 (from scikit-image->-r requirements.txt (line 2))
  Using cached imageio-2.37.0-py3-none-any.whl.metadata (5.2 kB)
Collecting tifffile>=2022.8.12 (from scikit-image->-r requirements.txt (line 2))
  Using cached tifffile-2025.5.10-py3-none-any.whl.metadata (31 kB)
Collecting packaging>=21 (from scikit-image->-r requirements.txt (line 2))
  Using cached packaging-25.0-py3-none-any.whl.metadata (3.3 kB)
Collecting lazy-loader>=0.4 (from scikit-image->-r requirements.txt (line 2))
  Using cached lazy_loader-0.4-py3-none-any.whl.metadata (7.6 kB)
Collecting threadpoolctl>=3.1.0 (from scikit-learn->-r requirements.txt (line 4))
  Using cached threadpoolctl-3.6.0-py3-none-any.whl.metadata (13 kB)
Collecting PyYAML (from yacs->-r requirements.txt (line 7))
  Using cached PyYAML-6.0.2-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (2.1 kB)
Collecting llvmlite<0.45,>=0.44.0dev0 (from numba->-r requirements.txt (line 8))
  Using cached llvmlite-0.44.0-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (4.8 kB)
Collecting protobuf>=3.20 (from tensorboardx->-r requirements.txt (line 10))
  Using cached protobuf-6.32.0-cp39-abi3-manylinux2014_x86_64.whl.metadata (593 bytes)
Collecting six>=1.11.0 (from chumpy->-r requirements.txt (line 13))
  Using cached six-1.17.0-py2.py3-none-any.whl.metadata (1.7 kB)
Collecting freetype-py (from pyrender->-r requirements.txt (line 16))
  Using cached freetype_py-2.5.1-py3-none-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_12_x86_64.manylinux2010_x86_64.whl.metadata (6.3 kB)
Collecting pyglet>=1.4.10 (from pyrender->-r requirements.txt (line 16))
  Using cached pyglet-2.1.8-py3-none-any.whl.metadata (7.7 kB)
Collecting PyOpenGL==3.1.0 (from pyrender->-r requirements.txt (line 16))
  Using cached pyopengl-3.1.0-py3-none-any.whl
Collecting contourpy>=1.0.1 (from matplotlib->-r requirements.txt (line 17))
  Using cached contourpy-1.3.2-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (5.5 kB)
Collecting cycler>=0.10 (from matplotlib->-r requirements.txt (line 17))
  Using cached cycler-0.12.1-py3-none-any.whl.metadata (3.8 kB)
Collecting fonttools>=4.22.0 (from matplotlib->-r requirements.txt (line 17))
  WARNING: Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by 'ProxyError('Cannot connect to proxy.', ConnectionResetError(104, 'Connection reset by peer'))': /packages/31/ed/abed08178e06fab3513b845c045cb09145c877d50121668add2f308a6c19/fonttools-4.59.2-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.whl.metadata
  Downloading fonttools-4.59.2-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.whl.metadata (109 kB)
Collecting kiwisolver>=1.3.1 (from matplotlib->-r requirements.txt (line 17))
  Using cached kiwisolver-1.4.9-cp310-cp310-manylinux_2_12_x86_64.manylinux2010_x86_64.whl.metadata (6.3 kB)
Collecting pyparsing>=2.3.1 (from matplotlib->-r requirements.txt (line 17))
  Using cached pyparsing-3.2.3-py3-none-any.whl.metadata (5.0 kB)
Collecting python-dateutil>=2.7 (from matplotlib->-r requirements.txt (line 17))
  Using cached python_dateutil-2.9.0.post0-py2.py3-none-any.whl.metadata (8.4 kB)
Collecting botocore<1.41.0,>=1.40.21 (from boto3->-r requirements.txt (line 22))
  Downloading botocore-1.40.21-py3-none-any.whl.metadata (5.7 kB)
Collecting jmespath<2.0.0,>=0.7.1 (from boto3->-r requirements.txt (line 22))
  Using cached jmespath-1.0.1-py3-none-any.whl.metadata (7.6 kB)
Collecting s3transfer<0.14.0,>=0.13.0 (from boto3->-r requirements.txt (line 22))
  Using cached s3transfer-0.13.1-py3-none-any.whl.metadata (1.7 kB)
Collecting urllib3!=2.2.0,<3,>=1.25.4 (from botocore<1.41.0,>=1.40.21->boto3->-r requirements.txt (line 22))
  Using cached urllib3-2.5.0-py3-none-any.whl.metadata (6.5 kB)
Collecting charset_normalizer<4,>=2 (from requests->-r requirements.txt (line 23))
  Using cached charset_normalizer-3.4.3-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl.metadata (36 kB)
Collecting idna<4,>=2.5 (from requests->-r requirements.txt (line 23))
  Using cached idna-3.10-py3-none-any.whl.metadata (10 kB)
Collecting certifi>=2017.4.17 (from requests->-r requirements.txt (line 23))
  Using cached certifi-2025.8.3-py3-none-any.whl.metadata (2.4 kB)
Collecting kornia_rs>=0.1.9 (from kornia->-r requirements.txt (line 27))
  Using cached kornia_rs-0.1.9-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (11 kB)
Requirement already satisfied: filelock in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (3.19.1)
Requirement already satisfied: typing-extensions>=4.8.0 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (4.15.0)
Requirement already satisfied: sympy in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (1.14.0)
Requirement already satisfied: jinja2 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (3.1.6)
Requirement already satisfied: fsspec in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (2025.7.0)
Requirement already satisfied: nvidia-cuda-nvrtc-cu11==11.8.89 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (11.8.89)
Requirement already satisfied: nvidia-cuda-runtime-cu11==11.8.89 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (11.8.89)
Requirement already satisfied: nvidia-cuda-cupti-cu11==11.8.87 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (11.8.87)
Requirement already satisfied: nvidia-cudnn-cu11==8.7.0.84 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (8.7.0.84)
Requirement already satisfied: nvidia-cublas-cu11==11.11.3.6 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (11.11.3.6)
Requirement already satisfied: nvidia-cufft-cu11==10.9.0.58 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (10.9.0.58)
Requirement already satisfied: nvidia-curand-cu11==10.3.0.86 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (10.3.0.86)
Requirement already satisfied: nvidia-cusolver-cu11==11.4.1.48 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (11.4.1.48)
Requirement already satisfied: nvidia-cusparse-cu11==11.7.5.86 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (11.7.5.86)
Requirement already satisfied: nvidia-nccl-cu11==2.20.5 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (2.20.5)
Requirement already satisfied: nvidia-nvtx-cu11==11.8.86 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (11.8.86)
Requirement already satisfied: triton==2.3.1 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (2.3.1)
Requirement already satisfied: MarkupSafe>=2.0 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from jinja2->torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (3.0.2)
Requirement already satisfied: mpmath<1.4,>=1.1.0 in /data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages (from sympy->torch>=1.0.1.post2->smplx==0.1.28->-r requirements.txt (line 5)) (1.3.0)
Using cached smplx-0.1.28-py3-none-any.whl (29 kB)
Using cached scikit_image-0.25.2-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (14.8 MB)
Using cached scipy-1.15.3-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (37.7 MB)
Using cached scikit_learn-1.7.1-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.whl (9.7 MB)
Using cached yacs-0.1.8-py3-none-any.whl (14 kB)
Using cached numba-0.61.2-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.whl (3.8 MB)
Using cached llvmlite-0.44.0-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (42.4 MB)
Using cached opencv_python-4.12.0.88-cp37-abi3-manylinux2014_x86_64.manylinux_2_17_x86_64.whl (67.0 MB)
Using cached tensorboardx-2.6.4-py3-none-any.whl (87 kB)
Using cached cython-3.1.3-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl (3.4 MB)
Using cached trimesh-4.7.4-py3-none-any.whl (709 kB)
Using cached pyrender-0.1.45-py3-none-any.whl (1.2 MB)
Downloading matplotlib-3.10.6-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.whl (8.7 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 8.7/8.7 MB 5.0 MB/s eta 0:00:00
Using cached json_tricks-3.17.3-py2.py3-none-any.whl (27 kB)
Using cached torchgeometry-0.1.2-py2.py3-none-any.whl (42 kB)
Using cached einops-0.8.1-py3-none-any.whl (64 kB)
Using cached joblib-1.5.2-py3-none-any.whl (308 kB)
Downloading boto3-1.40.21-py3-none-any.whl (139 kB)
Downloading botocore-1.40.21-py3-none-any.whl (14.0 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 14.0/14.0 MB 4.8 MB/s eta 0:00:00
Using cached jmespath-1.0.1-py3-none-any.whl (20 kB)
Using cached python_dateutil-2.9.0.post0-py2.py3-none-any.whl (229 kB)
Using cached s3transfer-0.13.1-py3-none-any.whl (85 kB)
Using cached urllib3-2.5.0-py3-none-any.whl (129 kB)
Using cached requests-2.32.5-py3-none-any.whl (64 kB)
Using cached charset_normalizer-3.4.3-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.manylinux_2_28_x86_64.whl (152 kB)
Using cached idna-3.10-py3-none-any.whl (70 kB)
Using cached easydict-1.13-py3-none-any.whl (6.8 kB)
Using cached pycocotools-2.0.10-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (455 kB)
Using cached kornia-0.8.1-py2.py3-none-any.whl (1.1 MB)
Using cached certifi-2025.8.3-py3-none-any.whl (161 kB)
Using cached contourpy-1.3.2-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (325 kB)
Using cached cycler-0.12.1-py3-none-any.whl (8.3 kB)
Downloading fonttools-4.59.2-cp310-cp310-manylinux2014_x86_64.manylinux_2_17_x86_64.whl (4.8 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 4.8/4.8 MB 4.5 MB/s eta 0:00:00
Using cached imageio-2.37.0-py3-none-any.whl (315 kB)
Using cached kiwisolver-1.4.9-cp310-cp310-manylinux_2_12_x86_64.manylinux2010_x86_64.whl (1.6 MB)
Using cached kornia_rs-0.1.9-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (2.8 MB)
Using cached lazy_loader-0.4-py3-none-any.whl (12 kB)
Using cached packaging-25.0-py3-none-any.whl (66 kB)
Using cached protobuf-6.32.0-cp39-abi3-manylinux2014_x86_64.whl (322 kB)
Using cached pyglet-2.1.8-py3-none-any.whl (1.0 MB)
Using cached pyparsing-3.2.3-py3-none-any.whl (111 kB)
Using cached six-1.17.0-py2.py3-none-any.whl (11 kB)
Using cached threadpoolctl-3.6.0-py3-none-any.whl (18 kB)
Using cached tifffile-2025.5.10-py3-none-any.whl (226 kB)
Using cached freetype_py-2.5.1-py3-none-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_12_x86_64.manylinux2010_x86_64.whl (1.0 MB)
Using cached PyYAML-6.0.2-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (751 kB)
Installing collected packages: PyOpenGL, json_tricks, easydict, urllib3, trimesh, tifffile, threadpoolctl, six, scipy, PyYAML, pyparsing, pyglet, pycocotools, protobuf, packaging, opencv-python, llvmlite, kornia_rs, kiwisolver, joblib, jmespath, imageio, idna, freetype-py, fonttools, einops, cython, cycler, contourpy, charset_normalizer, certifi, yacs, tensorboardx, scikit-learn, requests, python-dateutil, pyrender, numba, lazy-loader, chumpy, torchgeometry, smplx, scikit-image, matplotlib, kornia, botocore, s3transfer, filterpy, boto3
Successfully installed PyOpenGL-3.1.0 PyYAML-6.0.2 boto3-1.40.21 botocore-1.40.21 certifi-2025.8.3 charset_normalizer-3.4.3 chumpy-0.70 contourpy-1.3.2 cycler-0.12.1 cython-3.1.3 easydict-1.13 einops-0.8.1 filterpy-1.4.5 fonttools-4.59.2 freetype-py-2.5.1 idna-3.10 imageio-2.37.0 jmespath-1.0.1 joblib-1.5.2 json_tricks-3.17.3 kiwisolver-1.4.9 kornia-0.8.1 kornia_rs-0.1.9 lazy-loader-0.4 llvmlite-0.44.0 matplotlib-3.10.6 numba-0.61.2 opencv-python-4.12.0.88 packaging-25.0 protobuf-6.32.0 pycocotools-2.0.10 pyglet-2.1.8 pyparsing-3.2.3 pyrender-0.1.45 python-dateutil-2.9.0.post0 requests-2.32.5 s3transfer-0.13.1 scikit-image-0.25.2 scikit-learn-1.7.1 scipy-1.15.3 six-1.17.0 smplx-0.1.28 tensorboardx-2.6.4 threadpoolctl-3.6.0 tifffile-2025.5.10 torchgeometry-0.1.2 trimesh-4.7.4 urllib3-2.5.0 yacs-0.1.8
```

安装过程1min左右，中间一段warning可以不用管

- 安装fetch_data.sh里面的剩余文件

运行代码：

```bash
bash fetch_data.sh
```

安装时间不到1min

```bash
--2025-08-30 20:12:48--  <http://visiondata.cis.upenn.edu/spin/data.tar.gz>
Connecting to 127.0.0.1:7890... connected.
Proxy request sent, awaiting response... 301 Moved Permanently
Location: <https://visiondata.cis.upenn.edu/spin/data.tar.gz> [following]
--2025-08-30 20:12:49--  <https://visiondata.cis.upenn.edu/spin/data.tar.gz>
Connecting to 127.0.0.1:7890... connected.
Proxy request sent, awaiting response... 200 OK
Length: 14960640 (14M) [application/x-gzip]
Saving to: ‘data.tar.gz’

data.tar.gz                             100%[===============================================================================>]  14.27M  2.25MB/s    in 6.6s    

2025-08-30 20:12:57 (2.16 MB/s) - ‘data.tar.gz’ saved [14960640/14960640]

data/
data/J_regressor_h36m.npy
data/cube_parts.npy
data/train.h5
data/vertex_texture.npy
data/smpl_mean_params.npz
data/J_regressor_extra.npy
data/gmm_08.pkl
--2025-08-30 20:12:57--  <https://github.com/nkolot/GraphCMR/raw/master/data/mesh_downsampling.npz>
Connecting to 127.0.0.1:7890... connected.
Proxy request sent, awaiting response... 302 Found
Location: <https://raw.githubusercontent.com/nkolot/GraphCMR/master/data/mesh_downsampling.npz> [following]
--2025-08-30 20:12:58--  <https://raw.githubusercontent.com/nkolot/GraphCMR/master/data/mesh_downsampling.npz>
Connecting to 127.0.0.1:7890... connected.
Proxy request sent, awaiting response... 200 OK
Length: 1720359 (1.6M) [application/octet-stream]
Saving to: ‘data/smpl_downsampling.npz’

data/smpl_downsampling.npz              100%[===============================================================================>]   1.64M  3.96MB/s    in 0.4s    

2025-08-30 20:12:59 (3.96 MB/s) - ‘data/smpl_downsampling.npz’ saved [1720359/1720359]

--2025-08-30 20:12:59--  <https://github.com/microsoft/MeshGraphormer/raw/main/src/modeling/data/mano_downsampling.npz>
Connecting to 127.0.0.1:7890... connected.
Proxy request sent, awaiting response... 302 Found
Location: <https://raw.githubusercontent.com/microsoft/MeshGraphormer/main/src/modeling/data/mano_downsampling.npz> [following]
--2025-08-30 20:13:00--  <https://raw.githubusercontent.com/microsoft/MeshGraphormer/main/src/modeling/data/mano_downsampling.npz>
Connecting to 127.0.0.1:7890... connected.
Proxy request sent, awaiting response... 200 OK
Length: 176509 (172K) [application/octet-stream]
Saving to: ‘data/mano_downsampling.npz’

data/mano_downsampling.npz              100%[===============================================================================>] 172.37K   111KB/s    in 1.6s    

2025-08-30 20:13:02 (111 KB/s) - ‘data/mano_downsampling.npz’ saved [176509/176509]
```

- 尝试图形输入，并检查结果

运行以下代码：

```bash
xvfb-run -a python -m apps.demo_smplx --image_folder examples/coco_images --detection_threshold 0.3 --pretrained_model data/pretrained_model/PyMAF-X_model_checkpoint_v1.1.pt --misc TRAIN.BHF_MODE full_body MODEL.PyMAF.HAND_VIS_TH 0.1
```

出现问题：

```bash
Traceback (most recent call last):
  File "/data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/runpy.py", line 196, in _run_module_as_main
    return _run_code(code, main_globals, None,
  File "/data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/runpy.py", line 86, in _run_code
    exec(code, run_globals)
  File "/data1/supersmpl/wzn/PyMAF-X/apps/demo_smplx.py", line 42, in <module>
    from models import hmr, pymaf_net
  File "/data1/supersmpl/wzn/PyMAF-X/models/__init__.py", line 2, in <module>
    from .pymaf_net import pymaf_net
  File "/data1/supersmpl/wzn/PyMAF-X/models/pymaf_net.py", line 17, in <module>
    from .maf_extractor import MAF_Extractor, Mesh_Sampler
  File "/data1/supersmpl/wzn/PyMAF-X/models/maf_extractor.py", line 6, in <module>
    from numpy.lib.twodim_base import triu_indices_from
ModuleNotFoundError: No module named 'numpy.lib.twodim_base'
```

这里应该是numpy版本问题，选择numpy版本为1.23.0

```bash
pip install numpy==1.23.0
```

安装成功：

```bash
Collecting numpy==1.23.0
  Using cached numpy-1.23.0-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (2.2 kB)
Using cached numpy-1.23.0-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (17.0 MB)
Installing collected packages: numpy
  Attempting uninstall: numpy
    Found existing installation: numpy 2.2.6
    Uninstalling numpy-2.2.6:
      Successfully uninstalled numpy-2.2.6
ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
numba 0.61.2 requires numpy<2.3,>=1.24, but you have numpy 1.23.0 which is incompatible.
opencv-python 4.12.0.88 requires numpy<2.3.0,>=2; python_version >= "3.9", but you have numpy 1.23.0 which is incompatible.
scikit-image 0.25.2 requires numpy>=1.24, but you have numpy 1.23.0 which is incompatible.
scipy 1.15.3 requires numpy<2.5,>=1.23.5, but you have numpy 1.23.0 which is incompatible.
Successfully installed numpy-1.23.0
```

中间有error错误报信，不用管它，再次运行指令，出现错误：

```bash
/data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages/skimage/transform/_warps.py:2: UserWarning: A NumPy version >=1.23.5 and <2.5.0 is required for this version of SciPy (detected version 1.23.0)
  from scipy import ndimage as ndi
/data1/supersmpl/wzn/PyMAF-X/models/hr_module.py:481: SyntaxWarning: "is" with a literal. Did you mean "=="?
  or self.pretrained_layers[0] is '*':
INFO:OpenGL.acceleratesupport:No OpenGL_accelerate module loaded: No module named 'OpenGL_accelerate'
Traceback (most recent call last):
  File "/data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/runpy.py", line 196, in _run_module_as_main
    return _run_code(code, main_globals, None,
  File "/data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/runpy.py", line 86, in _run_code
    exec(code, run_globals)
  File "/data1/supersmpl/wzn/PyMAF-X/apps/demo_smplx.py", line 56, in <module>
    from openpifpaf import decoder as ppdecoder
ModuleNotFoundError: No module named 'openpifpaf'
```

两个错误，一个是没有一个叫’OpenGL_accelerate’的模板，解决方法：

运行代码：

```bash
pip install OpenGL_accelerate
```

将demo_smplx.py文件的第21行，train.py文件的第2行，renderer.py文件的第227，229和230行注释掉，第228行不能注释，但是注意缩进

还有就是缺少openpifpaf的模板问题，解决办法：

```bash
python setup.py install
```

注意，这里面的setup.py已经将其中三行关于torch,torchvision和numpy的版本限定代码注释掉之后的结果。

再次运行代码：

```bash
/data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages/skimage/transform/_warps.py:2: UserWarning: A NumPy version >=1.23.5 and <2.5.0 is required for this version of SciPy (detected version 1.23.0)
  from scipy import ndimage as ndi
INFO:OpenGL.acceleratesupport:OpenGL_accelerate module loaded
INFO:OpenGL.arrays.arraydatatype:Using accelerated ArrayDatatype
initializing openpifpaf
Running demo...
Input video number of frames 2
INFO:openpifpaf.predictor:Using multiple GPUs: 2
INFO:openpifpaf.decoder.factory:No specific decoder requested. Using the first one from:
  --decoder=cifcaf:0
  --decoder=posesimilarity:0
Use any of the above arguments to select one or multiple decoders and to suppress this message.
INFO:openpifpaf.predictor:neural network device: cuda (CUDA available: True, count: 2)
Running openpifpaf for person detection...
  0%|                                                                                                                                     | 0/2 [00:00<?, ?it/s]/data1/supersmpl/share/openpifpaf/src/openpifpaf/csrc/src/cif_hr.cpp:102: UserInfo: resizing cifhr buffer
/data1/supersmpl/share/openpifpaf/src/openpifpaf/csrc/src/occupancy.cpp:53: UserInfo: resizing occupancy buffer
INFO:openpifpaf.decoder.cifcaf:annotations 116: [130, 129, 127, 106, 100, 63, 31, 17, 13, 12, 9, 8, 2, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
INFO:openpifpaf.predictor:batch 0: examples/coco_images/COCO_val2014_000000004700.jpg
 50%|██████████████████████████████████████████████████████████████▌                                                              | 1/2 [00:00<00:00,  1.27it/s]INFO:openpifpaf.decoder.cifcaf:annotations 12: [133, 133, 93, 1, 0, 0, 0, 0, 0, 0, 0, 0]
INFO:openpifpaf.predictor:batch 1: examples/coco_images/COCO_val2014_000000477655.jpg
100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 2/2 [00:00<00:00,  2.15it/s]
WARNING: You are using a SMPL model, with only 10 shape coefficients.
WARNING: You are using a SMPL model, with only 10 shape coefficients.
WARNING: You are using a MANO model, with only 10 shape coefficients.
/data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages/smplx/body_models.py:1967: UserWarning: Creating a tensor from a list of numpy.ndarrays is extremely slow. Please consider converting the list to a single numpy.ndarray with numpy.array() before converting to a tensor. (Triggered internally at ../torch/csrc/utils/tensor_new.cpp:274.)
  dynamic_lmk_b_coords = torch.tensor(
WARNING: You are using a SMPL model, with only 10 shape coefficients.
WARNING: You are using a SMPL model, with only 10 shape coefficients.
/data1/supersmpl/wzn/.conda/envs/PyMAF-X/lib/python3.10/site-packages/torch/functional.py:512: UserWarning: torch.meshgrid: in an upcoming release, it will be required to pass the indexing argument. (Triggered internally at ../aten/src/ATen/native/TensorShape.cpp:3587.)
  return _VF.meshgrid(tensors, **kwargs)  # type: ignore[attr-defined]
/data1/supersmpl/wzn/PyMAF-X/models/maf_extractor.py:60: UserWarning: torch.sparse.SparseTensor(indices, values, shape, *, device=) is deprecated.  Please use torch.sparse_coo_tensor(indices, values, shape, dtype=, device=). (Triggered internally at ../torch/csrc/utils/tensor_new.cpp:621.)
  ptD.append(torch.sparse.FloatTensor(i, v, d.shape))
WARNING: You are using a SMPL model, with only 10 shape coefficients.
WARNING: You are using a SMPL model, with only 10 shape coefficients.
WARNING: You are using a SMPL model, with only 10 shape coefficients.
Loading pretrained weights from "data/pretrained_model/PyMAF-X_model_checkpoint_v1.1.pt"
loaded checkpoint: data/pretrained_model/PyMAF-X_model_checkpoint_v1.1.pt
Running reconstruction on each tracklet...
100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 2/2 [00:02<00:00,  1.30s/it]
Total time spent for reconstruction: 11.21 seconds (including model loading time).
Saving output results to "output/coco_images/output.pkl".
WARNING: You are using a SMPL model, with only 10 shape coefficients.
WARNING: You are using a MANO model, with only 10 shape coefficients.
Rendering results, writing frames to output/coco_images/coco_images_output
100%|█████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 2/2 [00:02<00:00,  1.28s/it]
================= END ==============
```

复现成功

- 结果记录

原图片：

![](/picture/PyMAF-X3.jpg)

重建后：

全身复现结果：

![](/picture/PyMAF-X4.jpg)

手臂复现结果：

![](/picture/PyMAF-X5.jpg)
