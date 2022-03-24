QUIP+GAP环境配置
<!-- TOC -->

- [1. 使用Annaconda](#1-使用annaconda)
  - [1.1. 安装](#11-安装)
- [2. Miniconda](#2-miniconda)
  - [2.1. 安装](#21-安装)
  - [2.2. 换源](#22-换源)
- [3. 安装liblas liblapack](#3-安装liblas-liblapack)
  - [3.1. 手动安装](#31-手动安装)
  - [3.2. 自动安装](#32-自动安装)
- [4. Required install](#4-required-install)
- [5. 安装QUIP](#5-安装quip)
  - [5.1. 快速安装](#51-快速安装)
- [6. 安装gap_train](#6-安装gap_train)
  - [6.1. Testing](#61-testing)
  - [6.2. 配置](#62-配置)
  - [6.3. 打包安装](#63-打包安装)
- [7. docker](#7-docker)
- [8. 集群的使用](#8-集群的使用)
  - [常用命令](#常用命令)
  - [提交任务](#提交任务)
- [9. 问题](#9-问题)
- [10. 好用的linux指令](#10-好用的linux指令)
- [11. 基于gap-train的测试](#11-基于gap-train的测试)
  - [11.1. bulk water](#111-bulk-water)
- [12. 读代码](#12-读代码)
  - [12.1. ``gap.py``](#121-gappy)
  - [12.2. ``system.py``](#122-systempy)
- [13. TIL](#13-til)

<!-- /TOC -->

记录一下QUIP包的配置过程，并一同打上GAP等拓展包。最后把docker整一整。

* 参考文档
  https://libatoms.github.io/GAP/installation.html


  7. 需要先前安装： 
    ASE(Atomic Simulation Environment): 直接pip安就行




需要安的包：
yum/手动:
* gcc
  参考：非root用户下： https://blog.csdn.net/happyeveryday62/article/details/107673477
* python/pip
  
* gfortran

* libblas-dev
* liblapack-dev
  参考链接： https://blog.csdn.net/Dorwin666/article/details/94906728
  http://blog.sciencenet.cn/blog-3233813-1001369.html

pip：
* ase
* f90wrap


# 1. 使用Annaconda
## 1.1. 安装
参考：https://www.jianshu.com/p/41d81b0f0377
去tsingha镜像站下载最新的对应版本镜像
```
wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2020.11-Linux-x86_64.sh

```

安装
```
bash Anaconda3-2020.11-Linux-x86_64.sh
```
根据提示, 进行选择, 会自动对.bashrc进行修改
把conda initialize部分添加到.zshrc中, 因为这里我没修改bash
plus.
```
If you'd prefer that conda's base environment not be activated on startup, set the auto_activate_base parameter to false: 

conda config --set auto_activate_base false
```
# 2. Miniconda
## 2.1. 安装
进入官网下载最新版本:https://link.zhihu.com/?target=https%3A//docs.conda.io/en/latest/miniconda.html%23

## 2.2. 换源
将以上配置文件写在~/.condarc中
vim ~/.condarc
中国科学技术大学 USTC Mirror
```bash
channels:
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
  - https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
  - https://mirrors.ustc.edu.cn/anaconda/cloud/conda-forge/
ssl_verify: true
```
# 3. 安装liblas liblapack
## 3.1. 手动安装 
参考链接：https://blog.csdn.net/Dorwin666/article/details/94906728
```bash
#blas库文件
sudo cp /home/liuquansheng/BLAS-3.8.0/libblas.a /usr/local/lib

#cblas库文件
sudo cp vim /home/liuquansheng/CBLAS/lib/cblas_LINUX.a /usr/local/lib/libcblas.a

#lapack complie
cd /home/liuquansheng/lapack-3.9.0
make # 编译所有的lapack文件
cd lapacke # 进入lapacke 文件夹，这个文件夹包含lapack的C语言接口文件
make # 编译lapacke
cp include/*.h /usr/local/include #将lapacke的头文件复制到系统头文件目录
cd .. #返回到 lapack-3.4.2 目录
cp *.a /usr/local/lib # 将生成的所有库文件复制到系统库目录
```
## 3.2. 自动安装
```bash
yum install lapack
yum install blas
```
# 4. Required install 
1. GPAW  
   https://wiki.fysik.dtu.dk/gpaw/install.html
   遇到问题：
   ```bash
        #include <Python.h>
              ^~~~~~~~~~
    compilation terminated.
    error: command 'gcc' failed with exit status 1
    ```
  <br>

2. DFTB  
   cmake版本太低, 选择自己安装, 一同操作之后, 即使把安装目录放到了用户`$PATH`里面, which之后还是/bin/local/cmake下的. 这个命令执行的优先级还是不太懂, 目前暂时的解决方法:
   ```bash
   alias cmake=/home/liuquansheng/Program/cmake-3.20.0-rc5/bin/cmake
   ```

   知道了, 这个和PATH的优先级有关系, 如果要想自己安装的文件被执行, 可以自行修改环境变量:
   ```bash
   export PATH=$
   ```
   
   <br>

3. GROMACS  
   这个是本来就这有的, 文章的实验里用的是2019.12的版本
   <br>
4. XTB  
   官网: https://www.chemie.uni-bonn.de/pctc/mulliken-center/software/xtb/xtb/  
   github: https://github.com/grimme-lab/xtb/
   直接使用`conda install`
5. ORCA  
  这个软件是一个用于量子化学计算的软件包.
  <br>
  安装步骤:<br>
      1.  [下载地址](https://orcaforum.kofo.mpg.de/index.php)  需要提前注册  
      Linux可下载的软件包括静态库版和动态库版(shared) 前者体积更大, 运行时间基本没啥区别  
      2. 进入解压目录, 添加`pwd`到`$PATH`和`$LD_LIBRARY_PATH`  
        运行时需要使用绝对路径, 也可`alias orca='/path/to/orca'`
      3. 安装openmpi  
        可以使用`conda install` 直接安装  
  
    参考: http://sobereva.com/451

好用的locate命令
https://www.howtoforge.com/linux-locate-command/
locate -r /testfile$

# 5. 安装QUIP
参考： https://libatoms.github.io/GAP/installation.html
## 5.1. 快速安装
安装一些前置包
```bash
$ pip install numpy ase f90wrap
```
使用`git`命令获取代码, 最近github网络又不太稳定, 先在wsl上用代理下载好, 再传到服务器上.

```bash
$ git clone --recursive https://github.com/libAtoms/QUIP.git
$ export QUIP_ARCH=linux_x86_64_gfortran
$ export QUIPPY_INSTALL_OPTS=--user  # omit for a system-wide installation
$ make config
```
Answer all the questions with their defaults (by pressing enter) for now, just to get things working.
目前无脑选择默认, 需要用到专门的功能再进行一次重新安装.
```bash
$ make
$ make install-quippy
```

重新build:
```bash
make deepclean
make
```
记得添加安装目录的系统变量中

测试是否安装成功

```bash
$ python
>>> import quippy
```

* 如果装错了
  直接使用`make distclean`将整个环境恢复到最原始的刚刚下载的状态
* 如果修改了`make config`的文件
  使用`make deepclean`, 在重新编译


出问题了！！！
1. can't open module file 'system_module.mod'
    make没有clean干净/make不能用j 并行存在的问题
2. error: ‘setjmpvalue’ undeclared
    numpy版本不匹配
    使用`pip install ./src/f90wrap` 使用本地最新的包进行一个安装
3. import失败
    直接开个issue进行了一个询问：https://github.com/libAtoms/QUIP/issues/288
    岛上的gcc版本太低，gfortran跟不上了, dalao发现一个很关键的问题: 
    > 编译python的gcc和编译quippy的gcc并不是同一个版本
    具体的原因目前还不清楚, 
    ```python
    (gap) [xxx@node01 opt]$ python -VV
    Python 3.8.8 (default, Feb 24 2021, 21:46:12) 
    [GCC 7.3.0]
    ```

<br>
晕了, dockr还是有问题, 最后还是会实验室上的服务器上装了. 这次好歹是装上了, 目前唯一的解释是: gcc版本和编译python用的版本需要一致.
```bash
quip-gap python -VV      
Python 3.7.10 (default, Feb 26 2021, 18:47:35) 
[GCC 7.3.0]
quip-gap gcc --version
gcc (GCC) 7.3.0
Copyright (C) 2017 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

```


* QUIP的参数设置
 https://github.com/libAtoms/QUIP/issues/236



# 6. 安装gap_train
```bash
git clone https://github.com/t-young31/gap-train.git
cd gap-train
conda install --file requirements.txt -c conda-forge
python setup.py install
```

## 6.1. Testing
`gaptrain` is unit tested with `pytest`, run them with
```bash
py.test
```
in the top level gap-train directory. To run the DFTB+, GAP and GROMACS tests set the `$GT_DFTB, $GT_GAP, $GT_GMX` environment variables to True as appropriate (if they're installed)

```bash
export GT_DFTB=True
export GT_ORCA=True
export GT_XTB=True
export GT_GAP=True
export GT_GMX=True
```

目前还因为某些原因通不过测试



## 6.2. 配置
因为需要调用不同的包, 要求把每个软件的安装路径提前注明

Different environments are handled with gt.GTConfig and/or environment variables. DFTB+ requires an executable path and a parameter path, which can be set (in bash) with:

```bash
export DFTB_COMMAND=/home/liuquansheng/dftbplus-20.2.1/bin/dftb+
export DFTB_PREFIX=/home/liuquansheng/workspace/quip-gap/3ob-3-1
```
相关参数在这里下载:
https://dftb.org/parameters/download/3ob/3ob-3-1-cc

GPAW similarly requires a parameter path with e.g.
```bash
export GPAW_SETUP_PATH=/home/liuquansheng/workspace/gap-train-1/gpaw/gpaw-setups-0.9.20000
```
To train GAPs requires a teach_sparse (or gap_fit) command to be available which can be set in a Python script with
```bash
gt.GTConfig.gap_fit_command = '/path/to/gap/train/executable'
```
To drive ASE dynamics using a GAP potential requires a Python with quippy installed


```bash
gt.GTConfig.gap_fit_command = ['/home/liuquansheng/workspace/QUIP/build/linux_x86_64_gfortran/gap_fit']
gt.GTConfig.quip_command = ['/home/liuquansheng/workspace/QUIP/build/linux_x86_64_gfortran/quip']
gt.GTConfig.quippy_gap_command = ['/home/liuquansheng/anaconda3/envs/gap/bin/python']
```

## 6.3. 打包安装
使用了
```
python setup.py install
```
 



# 7. docker
[基于Docker搭建Anaconda环境](https://zhuanlan.zhihu.com/p/139366067)
参考这里 wsl2配合docker好像比较好使

进行一个docker的学

* 让管理员在G101节点开一个root权限的节点, 基础镜像

  ```
  95ba85aa1443        bit:5000/ubuntu16.04_cuda10.2_devel_cudnn7                     "/bin/bash"              2 hours ago         Up 2 hours                                                 liuqs_root
  ```
<br>

* 进入容器进行配置
  ```bash
  sudo docker exec -it 95ba85aa1443 /bin/bash
  ```
  这样退出之后容器不会停止
  <br>
* 配置dockerfile过程中需要键盘输入
  https://blog.csdn.net/leon_wzm/article/details/78260795
  比如我们用sh Anaconda3-4.4.0-Linux-x86_64.sh来安装anaconda的时候，”烦人”的anaconda会问四个问题，我的回答顺序分别是Enter，yes，Enter，yes。于是你可以这样写：
  ```bash
  RUN sh -c '/bin/echo -e "\nyes\n\nyes" | sh Anaconda3-4.4.0-Linux-x86_64.sh'
  ```
  此招屡试不爽！

* Dockerfile命令
  https://www.cntofu.com/book/139/image/dockerfile/workdir.md
  * ``WORKDIR``
  需要注意, dockerfile里每一句RUN命令的执行环境根本不同, 是完全不同的两个容器, 如果需要改变之后各层的工作目录, 不要用cd, 直接用``WORKDIR \path\xxx``



* docker问题:
  1. make: warning: Clock skew detected. Your build may be incomplete.
   docker容器内的时间和host主机的不太一样, make会报错, 容器里也没有修改时间的权限
   我直接解压会有点问题
   可能的解决方法: 
  * 全部touch一遍 
      ```bash
      find ./ -type f |xargs touch
      ```
  * 在容器内git clone 
  
  * 修改容器内的时间 

  破案了: 根据
  `ls -l --time-style="+%Y-%m-%d %H:%M"`
  发现
      ```bash
      drwxr-xr-x  2 root root  4096 2021-04-06 03:10 travis
      root@95ba85aa1443:/ghome/xxx/gappack/QUIP# date
      Tue Apr  6 03:07:13 UTC 2021
      ```
  刚修改的文件时间比当前时间要快30min, 还没发现原因

* 有现成的还是用现成的吧, 直接从dockerhub里把需要的镜像pull下来. 同样的你也可以把可能会用到, 测试好的image传到dockerhub上.


# 8. 集群的使用
## 常用命令
1. `qstat -an`
2. 

<br>

## 提交任务
1. 编写pbs文件  
2. `qsub job.pbs`

# 9. 问题
* conda instll 与 pip install的区别
  <br>
  * pip instll 是专门用来安装python包的, 安装的是python wheel或source code的包. 从源代码安装需要有编译器的支持. <br>
  pip不会支持python以外的语言.
  <br>

  * conda install 
    用于安装conda package, 虽然大部分conda package是python的, 但也支持了不少非python语言写的依赖项, 如:mkl/cuda/blas等等. <br>
    conda package是编译好的二进制包, 不需要本地编译, 不会出现pip编译失败的情况, 单回使得包体积比较大.

  <br>
  conda 可以创建/管理多个python环境  
  <br><br>
  
  * conda下使用pip
  两者不完全重叠, 不是所有的包都有最新的conda版本, 这个时候就需要使用**conda中的pip**来安装包.<br>
  此时调用的pip:
    ```bash
      (gap) [xxx@node01 ~]$ which pip
    ~/workspace/miniconda3/envs/gap/bin/pip
    ```
<br>

*  Pytest的使用
  * 安装
    ```bash
    pip install pytest
    ```

* link报错
  /usr/lib64/libgfortran.so.3: version `GFORTRAN_1.4' not found (required by ./sizeof_fortran_t)


  此前安装了gfortran，查看报告错误的 libgfortran.so.3路径，是别的软件设置的LIB路径。

  搜索了一下 libgfortran.so.3，加入安装的gfortran的LIB到最前面，问题解决。
  export LD_LIBRARY_PATH=/usr/lib/gcc/i686-linux-gnu/4.6:/usr/lib/i386-linux-gnu:$LD_LIBRARY_PATH
  ————————————————
  原文链接：https://blog.csdn.net/lang999888/article/details/48715273

  https://blog.csdn.net/haolexiao/article/details/51579655

  conda 内设置环境变量
  (https://blog.csdn.net/robot8me/article/details/109471568)

  https://www.it1352.com/1907306.html



# 10. 好用的linux指令
* `ls -lh`
  显示文件信息, 包括大小
* `ll`
* `locate -r \work$`
  全字匹配
* `ls -l --time-style="+%Y-%m-%d %H:%M"`

* `where`
* `whereis`
* `which`


# 11. 基于gap-train的测试
读一读代码,  


## 11.1. bulk water
```bash
    raise GAPFailed(f'GAP train errored with:\n '
gaptrain.exceptions.GAPFailed: GAP train errored with:
 SYSTEM ABORT: Traceback (most recent call last)
File "/home/liuquansheng/workspace/QUIP/src/GAP/descriptors.f95", line 2533 kind unspecified
soap_initialise failed to parse args_str='soap sparse_method=cur_points n_sparse=500 covariance_type-
=dot_product zeta=4 atom_sigma=0.5 cutoff=3.0 delta=0.1 n_Z=1 n_species=1 species_Z={1} Z=8 n_max=6
l_max=6 n_species=2 Z=8 species_Z={8 1 }'        
```



# 12. 读代码
## 12.1. ``gap.py``
* 主要内容: `Class GAP`:
  
## 12.2. ``system.py``
* `class System`
  `@property`修饰符 Explained
  参考链接: https://www.programiz.com/python-programming/property
  **作用**: 在Object-Oriented Programming里让getter和setter用起来更简单
  * class without getter/setter
    类的属性其实是存在对象built-in的`__dict__`里面的

    ```python
    >>> human.__dict__
    {'temperature': 37}
    ```
    所以, 类似操作: 
    ```python
    human.temperture ----> human.__dict__['temperature']
    ```
    <br>
  * Using getter/setter
    ```python
    # Making Getters and Setter methods
    class Celsius:

        def __init__(self, temperature=0):
            self.set_temperature(temperature)

        def to_fahrenheit(self):
            return (self.get_temperature() * 1.8) + 32

        # getter method
        def get_temperature(self):
            return self._temperature

        # setter method
        def set_temperature(self, value):
            if value < -273.15:
                raise ValueError("Temperature below -273.15 is not possible.")
            self._temperature = value
    ```
    `_temperatue`, 加了下划线表示是private variable, 只是约定的实际上还是可以访问到的
    <br>
    要在已经写好的代码上implement getter/setter需要重复做类似的修改:
    ```python
    obj.temperature ---> obj.get_temperature
    obj.temperature=val ----> obj.set_temperature(val)
    ```
    <br>
  
  * 应用 property Class
    ```python
    # using property class
    class Celsius:
        def __init__(self, temperature=0):
            self.temperature = temperature

        def to_fahrenheit(self):
            return (self.temperature * 1.8) + 32

        # getter
        def get_temperature(self):
            print("Getting value...")
            return self._temperature

        # setter
        def set_temperature(self, value):
            print("Setting value...")
            if value < -273.15:
                raise ValueError("Temperature below -273.15 is not possible")
            self._temperature = value

        # creating a property object
        temperature = property(get_temperature, set_temperature)
    ```
    
    在类里面首先创建property object, 关联两个getter和setter的函数:
    ```python
    # creating a property object
    temperature = property(get_temperature, set_temperature)
    ```
    这样你每次在外面对temperature变量进行赋值的时候, 就会自动调用关联上的getter和setter, 不用再从头修改代码
    ```python
    # Using @property decorator
    class Celsius:
        def __init__(self, temperature=0):
            self.temperature = temperature

        def to_fahrenheit(self):
            return (self.temperature * 1.8) + 32

        @property
        def temperature(self):
            print("Getting value...")
            return self._temperature

        #声明temperature的setter
        @temperature.setter
        def temperature(self, value):
            print("Setting value...")
            if value < -273.15:
                raise ValueError("Temperature below -273 is not possible")
            self._temperature = value
    ```


  * @property Decorator
    ```python
    property(fget=None, fset=None, fdel=None, doc=None)
    ```
    甚至可以不用直接声明property对象
    

  <br>


# 13. TIL
* Doctator in Python
  https://www.programiz.com/python-programming/decorator
  <pr>
又叫做**mateprogramming**, part of the program tries to modify another part of the program at compile time.
    * everthing in Python is object(INCLUDING func)
<br>
* Docorator: a callable that returns a callable
```python
@make_pretty
def ordinary():
    print("I am ordinary")
```
两者等价

```python
def ordinary():
    print("I am ordinary")
ordinary = make_pretty(ordinary)
```

带参数的:

```python
def works_for_all(func):
    def inner(*args, **kwargs):
        print("I can decorate any function")
        return func(*args, **kwargs)
    return inner
```
可以直接:

```python
@works_for_all
def some_func(xxx):
    xxxx()
```

出错的command

```bash
/home/liuquansheng/workspace/QUIP/build/linux_x86_64_gfortran/gap_fit \
at_file=init_configs.xyz \
default_sigma={0.000316 0.100000 0.0 0.0} \
gap={
    soap \
        sparse_method=cur_points \
        n_sparse=500 \
        covariance_type=dot_product \
        zeta=4 \
        atom_sigma=0.5 \
        cutoff=3.0 \
        delta=0.1 \
        n_Z=1 \
        n_species=1 \
        species_Z={{1}} \
        Z=8 \
        n_max=6 \
        l_max=6\
} \
energy_parameter_name=dft_energy \
force_parameter_name=dft_forces \
sparse_separate_file=F \
gp_file=intra_h2o.xml\
```


查看文档d:

<details>
    <summary>
        <code>`git_fit --help`</code>
    </summary>
    <pre><code>
    (gap) [liuquansheng@node1 examples]$ gap_fit --help
libAtoms::Hello World: 08/04/2021   23:53:26
libAtoms::Hello World: git version  https://github.com/libAtoms/QUIP.git,e327f48-dirty
libAtoms::Hello World: QUIP_ARCH    linux_x86_64_gfortran
libAtoms::Hello World: compiled on  Apr  6 2021 at 18:37:29
libAtoms::Hello World: Random Seed = 86006133
libAtoms::Hello World: global verbosity = 0

Calls to system_timer will do nothing by default

atoms_filename type=STRING scalar current_value=//MANDATORY//
XYZ file with fitting configurations

at_file type=STRING scalar current_value=//MANDATORY//
XYZ file with fitting configurations

gap type=STRING scalar current_value=//MANDATORY//
Initialisation string for GAPs

e0 type=STRING scalar current_value=0.0
Atomic energy value to be subtracted from energies before fitting (and added
back on after prediction).  Specifiy a single number (used for all species) or
by species: {Ti:-150.0:O:-320...}. energy = baseline + GAP +
e0

local_property0 type=STRING scalar current_value=0.0
Local property value to be subtracted from the local property before fitting
(and added back on after prediction).  Specifiy a single number (used for all
species) or by species: {H:20.0:Cl:35.0...}.

e0_offset type=REAL scalar current_value=0.0
Offset of baseline. If zero, the offset is the average atomic energy of the inpu-
t data or the e0 specified manually.

e0_method type=STRING scalar current_value=isolated
Method to determine e0, if not explicitly specified. Possible options: isolated
(default, each atom present in the XYZ needs to have an isolated representative,
with a valid energy), average (e0 is the average of all total energies across
the XYZ)

default_kernel_regularisation type=REAL dim=4 current_value=//MANDATORY//
error in [energies forces virials hessians]

default_sigma type=REAL dim=4 current_value=//MANDATORY//
error in [energies forces virials hessians]

default_kernel_regularisation_local_property type=REAL scalar current_value=0.001
error in local_property

default_local_property_sigma type=REAL scalar current_value=0.001
error in local_property

sparse_jitter type=REAL scalar current_value=1.0e-10
Extra regulariser used to regularise the sparse covariance matrix before it is
passed to the linear solver. Use something small, it really shouldn't affect
your results, if it does, your sparse basis is still very ill-conditioned.

hessian_displacement type=REAL scalar current_value=1.0e-2
Finite displacement to use in numerical differentiation when obtaining second
derivative for the Hessian covariance

hessian_delta type=REAL scalar current_value=1.0e-2
Finite displacement to use in numerical differentiation when obtaining second
derivative for the Hessian covariance

baseline_param_filename type=STRING scalar current_value=quip_params.xml
QUIP XML file which contains a potential to subtract from data (and added back
after prediction)

core_param_file type=STRING scalar current_value=quip_params.xml
QUIP XML file which contains a potential to subtract from data (and added back
after prediction)

baseline_ip_args type=STRING scalar current_value=
 QUIP init string for a potential to subtract from data (and added back after
prediction)

core_ip_args type=STRING scalar current_value=
 QUIP init string for a potential to subtract from data (and added back after
prediction)

energy_parameter_name type=STRING scalar current_value=energy
Name of energy property in the input XYZ file that describes the data

local_property_parameter_name type=STRING scalar current_value=local_property
Name of local_property (column) in the input XYZ file that describes the data

force_parameter_name type=STRING scalar current_value=force
Name of force property (columns) in the input XYZ file that describes the data

virial_parameter_name type=STRING scalar current_value=virial
Name of virial property in the input XYZ file that describes the data

stress_parameter_name type=STRING scalar current_value=stress
Name of stress property (6-vector or 9-vector) in the input XYZ file that descri-
bes the data - stress values only used if virials are not available (opposite
sign, standard Voigt order)

hessian_parameter_name type=STRING scalar current_value=hessian
Name of hessian property (column) in the input XYZ file that describes the data

config_type_parameter_name type=STRING scalar current_value=config_type
Allows grouping on configurations into. This option is the name of the key that
indicates the configuration type in the input XYZ file. With the default, the
key-value pair config_type=blah would place that configuration into the group
blah.

kernel_regularisation_parameter_name type=STRING scalar current_value=sigma
kernel regularisation parameters for a given configuration in the database. Over-
rides the command line values (both defaults and config-type-specific values).
In the input XYZ file, it must be prepended by energy_, force_, virial_ or hessi-
an_

sigma_parameter_name type=STRING scalar current_value=sigma
kernel regularisation parameters for a given configuration in the database. Over-
rides the command line values (both defaults and config-type-specific values).
In the input XYZ file, it must be prepended by energy_, force_, virial_ or hessi-
an_

force_mask_parameter_name type=STRING scalar current_value=force_mask
To exclude forces on specific atoms from the fit. In the XYZ, it must be a logic-
al column.

parameter_name_prefix type=STRING scalar current_value=
Prefix that gets uniformly appended in front of {energy,local_property,force,vir-
ial,...}_parameter_name

config_type_kernel_regularisation type=STRING scalar current_value=
What kernel regularisation values to choose for each type of data, when the conf-
igurations are grouped into config_types. Format: {configtype1:energy:force:viri-
al:hessian:config_type2:energy:force:virial:hessian...}

config_type_sigma type=STRING scalar current_value=
What kernel regularisation values to choose for each type of data, when the conf-
igurations are grouped into config_types. Format: {configtype1:energy:force:viri-
al:hessian:config_type2:energy:force:virial:hessian...}

kernel_regularisation_is_per_atom type=LOGICAL scalar current_value=T
Interpretation of the energy and virial sigmas specified in >>default_kernel_reg-
ularisation<< and >>config_type_kernel_regularisation<<. If >>T<<, they are inte-
rpreted as per-atom errors, and the variance will be scaled according to the
number of atoms in the configuration. If >>F<< they are treated as absolute erro-
rs and no scaling is performed. NOTE: values specified on a per-configuration
basis (see >>kernel_regularisation_parameter_name<<) are always absolute, not
per-atom.

sigma_per_atom type=LOGICAL scalar current_value=T
Interpretation of the energy and virial sigmas specified in >>default_kernel_reg-
ularisation<< and >>config_type_kernel_regularisation<<. If >>T<<, they are inte-
rpreted as per-atom errors, and the variance will be scaled according to the
number of atoms in the configuration. If >>F<< they are treated as absolute erro-
rs and no scaling is performed. NOTE: values specified on a per-configuration
basis (see >>kernel_regularisation_parameter_name<<) are always absolute, not
per-atom.

do_copy_atoms_file type=LOGICAL scalar current_value=T
Copy the input XYZ file into the GAP XML file (should be set to False for NetCDF
input).

do_copy_at_file type=LOGICAL scalar current_value=T
Copy the input XYZ file into the GAP XML file (should be set to False for NetCDF
input).

sparse_separate_file type=LOGICAL scalar current_value=T
Save sparse point data in separate file in binary (use it for large datasets)

sparse_use_actual_gpcov type=LOGICAL scalar current_value=F
Use actual GP covariance for sparsification methods

gap_file type=STRING scalar current_value=gap_new.xml
Name of output XML file that will contain the fitted potential

gp_file type=STRING scalar current_value=gap_new.xml
Name of output XML file that will contain the fitted potential

verbosity type=STRING scalar current_value=NORMAL
Verbosity control. Options: NORMAL, VERBOSE, NERD, ANALYSIS.

rnd_seed type=INTEGER scalar current_value=-1
Random seed.

openmp_chunk_size type=INTEGER scalar current_value=1
Chunk size in OpenMP scheduling

do_ip_timing type=LOGICAL scalar current_value=F
To enable or not timing of the interatomic potential.

template_file type=STRING scalar current_value=template.xyz
Template XYZ file for initialising object

sparsify_only_no_fit type=LOGICAL scalar current_value=F
If true, sparsification is done, but no fitting. print the sparse index by addin-
g print_sparse_index=file.dat to the descriptor string.

missing mandatory parameter atoms_filename
missing mandatory parameter at_file
missing mandatory parameter gap
missing mandatory parameter default_kernel_regularisation
missing mandatory parameter default_sigma
gap_fit
SYSTEM ABORT: Exit: Mandatory argument(s) missing...   
    </code></pre>

</details>




<br>
终于整明白了， 在github上和作者提issue联系上了，原因是最新版本的GAP/QUIP修改了API, 添加了几个新的参数. 我和他一起identify了几个问题, 他开了一个新的branch, 用于适配最新版本的QUIP, 后面可能还会继续更新. 现在已经可以在集群上跑通了. 