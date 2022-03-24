Code review
<!-- TOC -->

- [Learn From Examples](#learn-from-examples)
- [理解几种文件的结构](#理解几种文件的结构)
  - [`.xyz`](#xyz)
  - [`.xml`](#xml)
- [`quippy`库](#quippy库)
- [`Autode`库](#autode库)
- [`gap-train`库](#gap-train库)
  - [`system.py`](#systempy)
  - [`configuration.py`](#configurationpy)
    - [`Class Configuration`](#class-configuration)
    - [`Class Configurationset`](#class-configurationset)
  - [`data.py`](#datapy)
  - [`descriptor.py`](#descriptorpy)
    - [`soap`](#soap)
    - [`soap_kernel_matrix`](#soap_kernel_matrix)
  - [`class Trajectory(gt.ConfigurationSet)`](#class-trajectorygtconfigurationset)
    - [`extract_from_ase(self, filename, init_config)`](#extract_from_aseself-filename-init_config)
  - [`activate.py`](#activatepy)
    - [`calc_error(frame, gap, method_name)`](#calc_errorframe-gap-method_name)
    - [`remove_intra(config, gap):`](#remove_intraconfig-gap)
    - [`get_active_config_diff()`](#get_active_config_diff)
    - [`get_active_config_qbc(config, gap, temp, std_e_thresh, max_time_fs)`](#get_active_config_qbcconfig-gap-temp-std_e_thresh-max_time_fs)
    - [`get_active_config_gp_var(config, gap, temp, var_e_thresh, max_time_fs)`](#get_active_config_gp_varconfig-gap-temp-var_e_thresh-max_time_fs)
    - [`get_active_configs()`](#get_active_configs)
    - [`get_init_configs(system, init_configs=None, n=10, method_name=None)`](#get_init_configssystem-init_configsnone-n10-method_namenone)
    - [`train()`](#train)
    - [`train_ii()`](#train_ii)
    - [`train_ss(system, method_name, intra_temp=1000, inter_temp=300, **kwargs)`](#train_sssystem-method_name-intra_temp1000-inter_temp300-kwargs)
  - [`md.py`](#mdpy)
    - [`simulation_steps(dt, kwargs)`](#simulation_stepsdt-kwargs)
    - [`ase_momenta_string(configuration, temp, bbond_energy, fbond_energy)`](#ase_momenta_stringconfiguration-temp-bbond_energy-fbond_energy)
    - [`run_gapmd()`](#run_gapmd)
    - [`run_mmmd(system, config, temp, dt, interval, **kwargs)`](#run_mmmdsystem-config-temp-dt-interval-kwargs)
    - [`run_dftbmd(configuration, temp, dt, interval, **kwargs)`](#run_dftbmdconfiguration-temp-dt-interval-kwargs)
  - [`gap.py`](#gappy)
    - [`predict(gap, data)`](#predictgap-data)
    - [`class GAP`](#class-gap)
    - [`class Parameters(atom_symbols)`](#class-parametersatom_symbols)
    - [`class inter_GAP(GAP)`](#class-inter_gapgap)
    - [`class intra_GAP(GAP)`](#class-intra_gapgap)
    - [`class SolventIntraGAP(IntraGAP)`](#class-solventintragapintragap)
    - [`SoluteIntraGAP(IntraGAP)`](#soluteintragapintragap)
    - [`class GAPEnsemble`](#class-gapensemble)
    - [`class AdditiveGAP`](#class-additivegap)
    - [`class IIGAP`](#class-iigap)
    - [`SSGAP(IIGAP)`](#ssgapiigap)
- [`iicalculator.py`](#iicalculatorpy)
  - [`class IICalculator`](#class-iicalculator)
    - [`__init__(self, intra, inter=None, expansion_factor=10, **kwargs)`](#__init__self-intra-internone-expansion_factor10-kwargs)
    - [`expanded_atoms(self, atoms)`](#expanded_atomsself-atoms)
    - [`calculate(self, atoms=None, properties=None, system_changes=None, **kwargs)`](#calculateself-atomsnone-propertiesnone-system_changesnone-kwargs)
  - [`class IntraCalculator(IICalculator)`](#class-intracalculatoriicalculator)
  - [`SSCalculator`](#sscalculator)
    - [`def __init__(self, solute_intra, solvent_intra, inter, expansion_factor=10, **kwargs)`](#def-__init__self-solute_intra-solvent_intra-inter-expansion_factor10-kwargs)
    - [`calculate(self, atoms=None, properties=None, system_changes=None, **kwargs)`](#calculateself-atomsnone-propertiesnone-system_changesnone-kwargs-1)
- [问题:](#问题)
- [重点](#重点)

<!-- /TOC -->
# Learn From Examples

从最简单的example出发:
```py
gt.GTConfig.n_cores = 4

# Initialise a box of water
h2o = gt.System(box_size=[10, 10, 10])
h2o.add_solvent('h2o', n=20)

# Train a intra+inter GAP and generate parameter files
# Note: train functions return a tuple of the data used to train and the
# resulting GAP
data, gap = gt.active.train_ii(h2o, method_name='dftb')

# Run molecular dynamics using the GAP
traj = gt.md.run_gapmd(configuration=h2o.random(min_dist_threshold=1.7),
                       gap=gap,
                       temp=300,
                       dt=0.5,
                       interval=5,
                       ps=1,
                       n_cores=4)

traj.save(filename='traj.xyz')
```

第一行, 指明使用的CPU核数  
之后的内容, 分为三部分:  
 1. init system  
   初始化体系, 声明一个指定大小的box, 往里面添加20个名为`h2o`的分子
    ```py
    h2o = gt.System(box_size=[10, 10, 10])
    h2o.add_solvent('h2o', n=20)
    ```
    
   <br>

 2. train GAP  
    返回训练用到的data和训练好的GAP模型  
    ```py
    data, gap = gt.active.train_ii(h2o, method_name='dftb')
    ```
    这里用到的函数`train_ii` 是用来训练inter + intra GAP的  
   <br>

 3. MD
    将训练好的GAP作为参数, 应用到md过程中
    ```py
    traj = gt.md.run_gapmd(configuration=h2o.random(min_dist_threshold=1.7),
                        gap=gap,
                        temp=300,
                        dt=0.5,
                        interval=5,
                        ps=1,
                        n_cores=4)
    ```

   <br>

下面从上面使用到的函数涉及的源码入手:

# 理解几种文件的结构
## `.xyz`

```
  1 60
  2 Lattice="10.000000 0.000000 0.000000 0.000000 10.000000 0.000000 0.000000 0.000000 10.000000" Properties=species:S:1:pos:R:3:dft_forces:R:3 dft_energy=-0.03749413
  3 O 1.12333 2.88194 6.06331 -0.45443 0.17905 -0.35582

```

第1行: 总原子数


第2行:   

* `Lattice` : components of the lattice vectors  
  
  由三个向量可以确定所有原子所在的box, 对于orthorhombic box而言, 可以直接取三个向量的diagonal elements

    ```python
    self.box = gt.Box(size=[components[0],
                        components[4],
                        components[8]])

    ```

*   Properties

第三行 : 原子信息

|原子名称    | 原子坐标(x\y\z)        | 对应的梯度[力] (x\y\z)    |
|:--------: | :---------------:     |:------------------:      |
|  O        |1.12333 2.88194 6.06331| -0.45443 0.17905 -0.35582|

  
一个`.xyz`文件中可一存在多组这样的原子信息(frame)
  
<br>

## `.xml`

GAP模型的参数


# `quippy`库

* Potential objects
相关源码: http://libatoms.github.io/QUIP/_modules/quippy/potential.html#Potential

是`ase.calculators.calculator.Calculator`的子类

```python
class quippy.potential.Potential(args_str='', pot1=None, pot2=None, 
                                param_str=None, param_filename=None, 
                                atoms=None, calculation_always_required=False, 
                                calc_args=None, add_arrays=None, add_info=None, **kwargs)
```
一个势对象代表一个原子间势、一个紧密结合(Tight Binding)模型或一个用于进行计算的外部代码的接口。

它由描述势的类型的`args_str`和给出参数的`XML`格式的字符串`param_str`初始化。

`args_str`: 
    `IP` ---> Interatomic Potential
    `IP GAP` ----> GAP Interatomic Potential Model

每一种potential对应的xml文件, 可以在这个仓库里找到: https://github.com/libAtoms/QUIP/tree/public/share/Parameters

<br>

# `Autode`库
`gaptrain`里面所有用到的分子, 都是使用`autode`库里的`Species`类, 直接调用了很多这个类的方法, 在读懂源码之前需要好好理解这个类提供的一些功能和方法



# `gap-train`库
## `system.py`
* System类
   在一个周期性的立方体盒子内的分子/离子/原子的可变集合，可以随机化坐标来产生不同的构型
    ```
       ________________
     / |       Zn2+   /|
    /________________/ |
    |  |             | |
    |  |  H2O   H2O  | |
    |  |             | |
    |  |_____________|_|
    | /    H2O       | /
    |________________|/
    ``` 

    <br>

    * `__add__(self, other)`  
        要判断添加的`other`是不是一个合理的Species/System
        ```python
        isinstance(other, Species):
        ```  
        **Speicies**(autode.species.species):   
        molecular species. A collection of atoms with a charge and spin multiplicity in a solvent (None is gas phase) 

        `self.moleculars`里面每加入的一个`molecular`都是一个`Species`类, 可以调用他的所有方法.

    * `random()`  
  
        随机一个box内的分子位置, 这里使用到了一个box类, 定义在`box.py`中.   
        该类具有两种随机位置的方法:  

        1. grid_rand : 随机的位置 落在格点上  
        2. rand : 随机位置(可能小数)    
   
        内含的`random_rotate()`调用`box`类的两种方法
      
        本方法可以根据这个方法对每个分子的位置进行随机初始化  

        流程:   
         1. 随机rotate分子  
         2. 反复随机tranlate分子的位置, 直到分子之间的最小距离大于阈值(这样分子才合理,不重叠)/超出最大随机次数  

        每一个translate之后, 都记录这个分子的坐标组(cordinates)
        
    <br>        

    * grid()  
  
        在evenly spaced grid上面生成molecular.

        先使用了这样的一种方法来计算, x,y,z(`box.size`)三个轴方向上包括的分子数量:
        $$Num_x = \sqrt[3]{\frac{x^2 n}{yz}}$$

        之后, 根据$Num_x, Num_y, Num_z$, 将box分割成一系列的`grids_points`(cell)

        再对每一个`molecular`, 随机分配一个cell(一组`grid_point`), 进行一个随机旋转: 

        它包含一个`random_rotate()`操作: 
        1. 先trans到中心点(molecular.centroid())
        2. 进行一个随机的rotate
        3. 再trans到原来的vec上

        旋转的停止条件, 随机出*合理* 的位置 **(距离其它分子的距离大于预设的最小阈值**) , 或者超过随机次数了, 还没有随机到合理的位置

        每得到一组合理的位置, 便将坐标添加到set里

        这个分子随机完以后, 这一个cell便不再使用(`grid_points.pop(points_idx)`)

    <br>

    * `add_solvent(self, solvent_name, n)`
  
        往system里加水
        这里使用了Solvent类里面的, `get_solvent()`方法定位solvent里包含的molecular:
        
        具体的操作是在solvent_lib里查表, 具体的位置是在当前目录下的`solvent_lib`文件夹下, 应该是从**gromacs**里面的一系列文件(以`.itp`结尾)复制过来的, 可以自己`locate`看一看

        之后使用`add_molecular`添加分子

    <br>

    * `add_molecules(self, molecule, n=1)`

        把一组相同的molecular加到system里面
        
        具体操作就是反复向`molecules`这个list里加指定的`molecule(Species)`

    `System`类里面还有几个计算@property(属性, 不可修改)的方法: 
    * density
    * charge
    * spin multiplicity(自旋多重性)

<br>

* `MM System`类(继承自`System`)   

    一个可以simulated with molecular mechanics的System

    拥有一个`generate_topology()`方法, 可以给系统生成一个GROMACXS topology

<br>

## `configuration.py`

### `Class Configuration`

由一组适合运行DFT或GAP的原子组成的配置, 可以用来set `self.energy`和`self.forces`

初始化: 
    `atoms`将所有`molecule`里面的`atom`加到`self.atom`里面

* `ase_atoms()`

    根据init的Configuration, 初始化ase的Atoms

* `add_perturbation()`
    给Config里的每一个atom加一个扰动. 直接translate

<br>

* `warp()`
    使用递归, 把所有原子的坐标限制在box里面
    ```python
    # Atom is not in the upper right octant of 3D space
    if atom.coord[i] < 0:
        atom.coord[i] += self.box.size[i]

    # Atom is further than the upper right quadrant
    if atom.coord[i] > self.box.size[i]:
        atom.coord[i] -= self.box.size[i]
    ```
<br>

* `set_autos()`
    
    设置好`self.atoms`<----`autode.atoms.Atom`

    * 可以直接读`xyz`文件
        
        用到了`audode`包里面的`xyz_file_to_atoms`方法

    * 从list of atoms里读

使用quippy作为驱动程序运行GAP计算，quippy是F90 QUIP代码的一个wrapper，用于使用GAP评估力和能量

<br>

* `run_dftb`
  
    直接使用了`run_dftb(configuration, max_force, traj_name=None):`方法

    在给定的config上跑DFTB+, `configuration.energy`和`configuration.forces`被设置为TB-DFT level上计算得到的值.

    用到的是`ase`包里的 `Dftb`类, 使用`BFGS`进行优化, 具体的流程为: 
    1.  定义方法:
   
        ```python
        ase_atoms = configuration.ase_atoms()
        dftb = DFTB(atoms=ase_atoms,
            kpts=(1, 1, 1),
            Hamiltonian_Charge=configuration.charge)
        ```

    2.  set
   
        ```python
        ase_atoms.set_calculator(dftb)
        ```
    

    3.  run

        ```python
        minimisation = BFGS(ase_atoms, trajectory=traj_name)
        minimisation.run(fmax=float(max_force))
        ```

    4. set force/energy
        ```python
        configuration.energy = ase_atoms.get_potential_energy()
        configuration.forces = ase_atoms.get_forces()

        ```

    下面的方法也参考了类似的流程进行不同包/库的调用

<br>

* `run_gap`

    主要是用训练好的GAP模型, 针对数据进行预测

    `calculator.py`里的`run_gap()`方法

    写一段`quippy`可以调用的python脚本`gap.py`

    使用`Popen`执行该脚本

    config\力\能量被保存在`config.xyz`/`energy.txt`/`force.txt`文件里面, 需要读取并赋值


    ```python
    import quippy
    import numpy as np
    from ase.io import read, write
    from ase.optimize import BFGS
    from ase.io.trajectory import Trajectory
    system = read("config.xyz")
    system.cell = [{a}, {b}, {c}]
    system.pbc = True
    system.center()

    #ase_gap_potential
    pot = quippy.potential.Potential("IP GAP", param_filename = "xxxx.xml")

    system.set_calculator(pot)

    #{min_section}
    traj = Trajectory('{traj_name}', 'w', system)
    dyn = BFGS(system)
    dyn.attach(traj.write, interval=1)
    dyn.run(fmax={float(max_force)})

    np.savetxt("energy.txt",\
            np.array([system.get_potential_energy()]))
    np.savetxt("forces.txt", system.get_forces())
    write("config.xyz", system)

    ```

<br>

* `run_gapw`
    
    用到了`calculator.py`里的`run_gpaw(configuration, max_force)`方法

    使用GPAW运行一个周期性的DFT计算。将设置`configuration.energy`和`configuration.forces`为它们在400eV/PBE理论水平上的**DFT**计算值

    直接调用的是gpaw的python包, 定义`dft`方法, 之后使用ase的方法进行最小化计算

<br>

* `run_cp2k`
    
    同样是写一堆脚本, 保存为`cp2k.inp`文件

    调用时使用了, `subprocess`的`Popen`, 因为需要输出, 使用了`Popen.communicate()`方法
    ```python
    calc = Popen(GTConfig.cp2k_command + ['-o', 'cp2k.out',  'cp2k.inp'],
                 shell=False)
    calc.communicate()
    ```

    读取`cp2k.out`文件, 设置能量和力

<br>

* `run_orca`与`run_xtb`
  
  这个是使用了`run_autode(configuration, max_force=None, method=None, n_cores=1)`的方法, 由`ase`实现对这两种方法的支持

  1. 定义
    ```python
    species = Species(name=configuration.name,
                      atoms=configuration.atoms,
                      charge=configuration.charge,
                      mult=configuration.mult)

    # allow for an ORCA calculation to have non-default keywords.. not the
    # cleanest implementation..
    kwds = GTConfig.orca_keywords if method.name == 'orca' else method.keywords.grad
    calc = Calculation(name='tmp',
                       molecule=species,
                       method=method,
                       keywords=kwds,
                       n_cores=n_cores)
    ```

  2. run

    ```python
    calc.run()
    ```

  3. set

    ```python
    configuration.forces = -ha_to_ev * calc.get_gradients()
    configuration.energy = ha_to_ev * calc.get_energy()
    configuration.partial_charges = calc.get_atomic_charges()
    ```
<br>



* `load()`
  
    从`.xyz`文件里面, 加载configration
    
    可以直接从已经读取的list里面读config

<br>

* `save()`

    将能量和力保存到`.xyz`文件里面

<br>

### `Class Configurationset`

Set of configurations
用于操作一组configuration

主要方法: 

* `load(self, filename=None, system=None, box=None, charge=None, mult=None):`

    输入`system`的可以是System/也可以是Config, 只用于初始化`box` `charge` `mult`几个参数

    需要从`.xyz`文件里, 逐个读取每个config, 使用上面的loda方法, 逐个新建构型.

* `save()`

    保存一系列Config到`.xyz`文件里

<br>

下面是一系列实现并行化的方法, 可以在ConfigSet上, 实现并行化计算

* `_run_parallel_method(self, function, max_force, **kwargs)`
  
  使用了`from multiprocessing import Pool`
  
    ```python
    with Pool(processes=gt.GTConfig.n_cores) as pool:

        # Apply the method to each configuration in this set
        for i, config in enumerate(self._list):
            result = pool.apply_async(func=function,
                                        args=(config, max_force),
                                        kwds=kwargs)
            results.append(result)

        # Reset all the configurations in this set with updated energy
        # and forces (each with .true)
        for i, result in enumerate(results):
            self._list[i] = result.get(timeout=None)

    ```
    主要看下`pool.apply_async`这个方法: 
    
    非阻塞异步,不会等待子进程执行完毕, 主进程会继续执行, 他会根据系统调度来进行进程切换

    ```python
    pool.apply_async(func=function,
                    args=(config, max_force),
                    kwds=kwargs)
    ```
    每调用一次, 就调用对应的计算方法(`gaptrain.calculators`里面定义的`run_xxx`)创建一个新的子进程, 并不会阻塞主进程的运行. 

    因为进程是移步运行的, **结果不能保证按顺序保存**, 这块更新config result的代码是否合理还需要进一步确认.


    参考: https://blog.csdn.net/weixin_37111106/article/details/85122988

    使用到了`with`

   
    with 语句实质是上下文管理, 如果出现异常, 还是可以进行清理操作:
    
    1. 上下文管理协议。包含方法`__enter__()` 和 `__exit__()`，支持该协议对象要实现这两个方法。
   
    2. 上下文管理器，定义执行`with`语句时要建立的运行时上下文，负责执行with语句块上下文中的进入与退出操作。
    
    3. 进入上下文的时候执行`__enter__`方法，如果设置`as var`语句，`var`变量接受`__enter__()`方法返回值。
    
    4. 如果运行时发生了异常，就退出上下文管理器。调用管理器`__exit__`方法。
   
<br>

`_run_parallel_method`方法配合`gaptrain.calculators`里面的几个`run_xxx`方法, 可以很好的实现, 进程的并行化运行.

<br>

* `truncate(self, n, method='random', **kwargs`)
  
  根据一定方法, 调整ConfigSet的元素

  1. random
  2. ensemble: 去除GAP集成模型下, error最大的几个config
  3. higher
  4. cur :** CUR matrix decompositions**, `用soap`
  5. cur_k: `用soap_kernel_matrix`

* `remove_xxx()`
  
  一组可以remove特定构型的方法:

  1. `remove_firstN`
  2. `remove_random`
  3. `remove_above_e` : 设立能量阈值, 去除大于屿阈值的
  4. `remove_above_f` : 力的阈值
  5. `remove_highest_e`: 去除能量最大的n个

<br>

## `data.py`

* `class Data(ConfigurationSet)`
  
  是ConfigurationSet的子类, 模型训练使用到的数据, 都是以这个形式实现的

  单独定义了几个获取config里面能量/力($|F|, (F_x, F_y, F_z)$)的方法

  可以对一个ConfigSet里面各种原子的能量和力绘制直方图


## `descriptor.py`

### `soap`

使用GAP, 从一系列(set)Configuration中去除intarmolecular enargy/force 

    ```python
    """
    Create a SOAP vector using dscribe (https://github.com/SINGROUP/dscribe)
    for a set of configurations

    soap(config)           -> [[v0, v1, ..]]
    soap(config1, config2) -> [[v0, v1, ..], [u0, u1, ..]]
    soap(configset)        -> [[v0, v1, ..], ..]

    ---------------------------------------------------------------------------
    :param args: (gaptrain.configurations.Configuration) or
                    (gaptrain.configurations.ConfigurationSet)

    :return: (np.ndarray) shape = (len(args), n) where n is the length of the
                SOAP descriptor
    """
    ```


使用的是 Descirbe(https://github.com/SINGROUP/dscribe) 包里面提供的SOAP实现

输入可以是 `config` 也可以是 `configSet`

返回一组`SOAP`向量


### `soap_kernel_matrix`

l
## `class Trajectory(gt.ConfigurationSet)`
是继承自`ConfigurationSet`的子类

下面实现的几个方法基本都是从不同的文件里面读取出frame, 再将frame转成config, 组成configset

MD trajectory frames

### `extract_from_ase(self, filename, init_config)`

    使用 `ase_traj.Trajectory` 提供初始化方法, 从`.traj`文件中, 加载`trajectory`对象.

    之后iter through traj里面所有的frame, 初始化一堆`config`, 加到`_list`里面.

* 


<br>

## `activate.py`

一系列基于主动学习的训练方法:
### `calc_error(frame, gap, method_name)`
    
    使用`gap`和`method_name`模型, 对 `frame`这个config, run 一遍single point evaluation, 计算得到误差.

<br>

### `remove_intra(config, gap):`


使用一个GAP(IIGAP/SSGAP)模型, 从ConfigSet里面移除每个config的intramolecular energy/force

具体方法: 
* 根据IIGAP的intra gap模型, 使用config的数据跑一遍预测, 得到`intra_energy/intra_force`  

    ```python
    config.energy -= i_config.energy
    config.forces -= i_config.forces
    ```

<br>

### `get_active_config_diff()`

**比较规则**: 单独一个GAP, 跑一段时间的GAP预测, 和另一种方法的预测结果如果差距过大, 返回对应的帧

```python
def get_active_config_diff(config, gap, temp, e_thresh, max_time_fs,
                        ref_method_name='dftb', curr_time_fs=0, n_calls=0,
                        extra_time_fs=0):

```
给定一个Config，在`temp`温度下, 用GAP运行MD，直到预测值和真实值(使用`ref_method_name`的计算结果作为参考值)之间的绝对误差超过一个阈值

**返回**: 一个需要加入到train set, 进行重新训练的**Config**(超出误差阈值的config)




关键代码: 
* 每次运行的时间:
    ```python
    md_time_fs = 2 + n_calls**3 + float(extra_time_fs)
    ```

* MD部分

    ```python
    gap_traj = gt.md.run_gapmd(config,
                            gap=gap,
                            temp=float(temp),
                            dt=0.5,
                            interval=4,
                            fs=md_time_fs,
                            n_cores=1)
    ```

* 计算error

    ```python
    error = calc_error(frame=gap_traj[-1], gap=gap, method_name=ref_method_name)
    ```

如果: 
1. `error > 100* e_thresh`

    返回第一帧`gap_traj[0]`

2. `error > 10 * e_thresh`

    往前找10帧, 直到遇到error小于`10 * e_thresh`的frame, 返回这一帧

3. `error > e_thresh`

    直接返回最后一帧` gap_traj[-1]`

4. `error < e_thresh`

    更新时间
    ```python
    curr_time_fs += md_time_fs
    ```

    继续递归调用该函数, 往下模拟

    ```python
    return get_active_config_diff(config, gap, temp, e_thresh, max_time_fs,
                                curr_time_fs=curr_time_fs,
                                ref_method_name=ref_method_name,
                                n_calls=n_calls+1)
    ```
    

<br>

### `get_active_config_qbc(config, gap, temp, std_e_thresh, max_time_fs)`

**比较规则**: 集成的gaps, 预测同一帧的能量, 看方差是否大于阈值


生成一个active config，即通过主动学习需要添加到训练集中的config，使用query-by-committee模型，其中**不同模型**之间的预测的差值（标准偏差）超过阈值（`std_e_thresh`）

这里的`gap`是`gt.GAPEnsemble`:  

> An 'ensemble' of GAPs trained on the same/similar data to make predictions with


用`gaps[0]`跑`2 + n_iters**3`ps的MD

```python
gap_traj = gt.md.run_gapmd(config,
                            gap=gap.gaps[0],
                            temp=float(temp),
                            dt=0.5,
                            interval=4,
                            fs=2 + n_iters**3,
                            n_cores=1)

```
取last 10%的frame, 进行query-by-committee.

对每一帧, 使用gaps里的不同GAP模型, 计算一个`frame.energy`, 统计所有`gaps`的预测结果的std, 如果大于阈值, 则将这一帧返回, 加入到training set里

```python
    for frame in gap_traj[::max(1, len(gap_traj)//10)]:
        pred_es = []
        # Calculated predicted energies in serial for all the gaps
        for single_gap in gap.gaps:
            frame.run_gap(gap=single_gap, n_cores=1)
            pred_es.append(frame.energy)

        # and return the frame if the standard deviation in the predictions
        # is larger than a threshold
        std_e = np.std(np.array(pred_es))
        if std_e > std_e_thresh:
            return frame
```

<br>

### `get_active_config_gp_var(config, gap, temp, var_e_thresh, max_time_fs)`

**比较规则**: quip算出来的gap variance

```bash
quip    calc_args=local_gap_variance   E=T  F=T             atoms_filename=tmp_traj.xyz    param_filename={gap_name}.xml
```
`quip.out`文件里包括了predicted atomic variance

```python
def run_quip():
    """Use QUIP on a set of configurations to predict the variance"""
    with open('quip.out', 'w') as tmp_out_file:
        subprocess = Popen(gt.GTConfig.quip_command +
                            ['calc_args=local_gap_variance', 'E=T', 'F=T',
                            'atoms_filename=tmp_traj.xyz',
                            f'param_filename={gap_name}.xml'],
                            shell=False, stdout=tmp_out_file, stderr=PIPE)
        subprocess.wait()

    return None
```


通过使用训练好的GAP模型, 计算Config上预测值的方差，生成一个active config(frame)

使用`gt.calculator.get_gp_var_quip_out(configuration, out_filename='quip.out')`, 从`quip.out`文件中读取每个`xyz section`, 读取`predicted atomic variance`

>     给定一个QUIP输出文件，提取输出文件中每一组atoms的原子方差的numpy数组

```python
gap_traj = gt.md.run_gapmd(config,
                            gap=gap,
                            temp=float(temp),
                            dt=0.5,
                            interval=max(1, (2 + n_iters**3)//20),
                            fs=2 + n_iters**3,
                            n_cores=1)
curr_time += 2 + n_iters**3

gap_traj.save('tmp_traj.xyz')
run_quip()
gp_vars = get_gp_var_quip_out(configuration=config)
```

遍历所有frame, 如果这个frame的var大于阈值, 则将这一帧返回, 加入traing set

<br>

### `get_active_configs()`

```python
def get_active_configs(config, gap, ref_method_name, method='diff',
                       max_time_fs=1000, n_configs=10, temp=300, e_thresh=0.1,
                       min_time_fs=0):

```
在线并行处理, 得到`n_configs`个active config

得到并行的标准:

1. `diff`
   
2. `qbc`
   需要通过`gap`初始化一个`gapEnsemble`
   还得给每个gap训练

3. `gp_var` 

最后得到一组筛选后待加入training set的configSet

<br>

### `get_init_configs(system, init_configs=None, n=10, method_name=None)`

生成一组初始config，用于主动学习

如果没有`init_config`就随机初始化一些(满足各原子间距离最大), 得到一个`ideal_dist`, 逐步减小`dist`, 随机化配置, 直到只能随机出10种不同的config, 则停止

```python
p_acc, dist = 0, ideal_dist+0.2

while p_acc < 0.1:
    n_generated_configs = 0
    dist -= 0.2                 # Reduce the minimum distance requirement

    for _ in range(10):
        _ = system.random(min_dist_threshold=dist)
        n_generated_configs += 1

    p_acc = n_generated_configs / 10
```
使用`init_configs += system.random(min_dist_threshold=dist, with_intra=True)`, 初始化n个config

跑一下计算, 得到几个初始配置的能量

```python
method = getattr(init_configs, f'parallel_{method_name.lower()}')
method()
```

<br>


### `train()`

```python
def train(system,
          method_name,
          gap=None,
          max_time_active_fs=1000,
          min_time_active_fs=0,
          n_configs_iter=10,
          temp=300,
          active_e_thresh=None,
          active_method='diff',
          max_energy_threshold=None,
          validate=False,
          tau=None,
          tau_max=None,
          val_interval=None,
          max_active_iters=50,
          n_init_configs=10,
          init_configs=None,
          remove_intra_init_configs=True,
          fix_init_config=False):
```

相关参数的解释:


```python
    """
    Train a system using active learning, by propagating dynamics using ML
    driven molecular dynamics (MD) and adding configurations where the error
    is above a threshold. Loop looks something like

    Generate configurations -> train a GAP -> run GAP-MD -> frames with error
                                   ^                               |
                                   |________ calc true  ___________


    Active learning will loop until either (1) the iteration > max_active_iters
    or (2) no configurations are found to add or (3) if calculated τ = max(τ)
    where the loop will break out

    --------------------------------------------------------------------------
    :param system: (gt.system.System)

    :param method_name: (str) Name of a method to use as the ground truth e.g.
                        dftb, orca, gpaw

    :param gap: (gt.gap.GAP) GAP to train with the active learnt data, if
                None then one will be initialised by placing SOAPs on each
                heavy atom and defining the 'other' atom types included in the
                neighbour density by their proximity. Distance cutoffs default
                to 3.5 Å for all atoms

    :param max_time_active_fs: (float) Maximum propagation time in the active
                               learning loop. Default = 1 ps

    :param min_time_active_fs: (float) Minimum propagation time for an
                               active learnt configuration. Will be updated
                               so the error is only calculated where the GAP
                               is unlikely to be accurate

    :param n_configs_iter: (int) Number of configurations to generate per
                           active learning cycle

    :param temp: (float) Temperature in K to propagate active learning at -
                 higher is better for stability but requires more training


    :param active_method: (str) Method used to generate active learnt
                          configurations. One of ['diff', 'qbc', 'gp_var']

    :param active_e_thresh: (float) Threshold in eV (E_t) above which a
                            configuration is added to the potential. If None
                            then will use 1 kcal mol-1 molecule-1

                            1. active_method='diff': |E_0 - E_GAP| > E_t

                            2. active_method='qbc': σ(E_GAP1, E_GAP2...) > E_t

                            3. active_method='gp_var': σ^2_GAP(predicted) > E_t

    :param max_energy_threshold: (float) Maximum relative energy threshold for
                                 configurations to be added to the training
                                 data

    :param **validate**: (bool) Whether or not to validate the potential during
                     the training. Will, by default run a τ calculation with
                     an interval max_time_active_fs / 100, so that a maximum of
                     50 calculations are run and a maximum time of
                     max(τ) = 5 x max_time_active_fs

    :param tau: (gt.loss.Tau) A instance of the τ error metric, unused if no
                validation is performed. Otherwise

    :param tau_max: (float | None) Maximum τ_acc in fs if float, will break out
                    of the active learning loop if this value is reached. If
                    None then won't break out

    :param val_interval: (int) Interval in the active training loop at which to
                         run the validation. Defaults to max_active_iters // 10
                         if validation is requested

    :param max_active_iters: (int) Maximum number of active learning
                             iterations to perform. Will break if we hit the
                             early stopping criteria

    :param n_init_configs: (int) Number of initial configurations to generate,
                           will be ignored if init_configs is not None

    :param init_configs: (gt.ConfigurationSet) A set of configurations from
                         which to start the active learning from

    :param remove_intra_init_configs: (bool) Whether the intramolecular
                                      component of the energy/force needs to
                                      be removed prior to training with
                                      init_configs. only applies for IIGAP
                                      and init_configs != None

    :param fix_init_config: (bool) Always start from the same initial
                            configuration for the active learning loop, if
                            False then the minimum energy structure is used.
                            Useful for TS learning, where dynamics should be
                            propagated from a saddle point not the minimum
    :return:
    """
```


流程: 

1. 调用`get_init_configs`初始化 initial traing set

2. 如果是`IIGAP`或`SSGAP`, **去除**intra potential

3. 初始化 累计误差(`τ metric`) `tau`

4. 使用`init_config` 训练gap : 调用`gt.GAP.train()`方法(`gap_fit` in command line)

5. 设置好`active_e_thresh`

6. 进行 **active learning loop** 

    `init_config`是`train_data.energies()`里最小的那个`config`

    ```python
    min_idx = int(np.argmin(train_data.energies()))
    init_config = train_data[min_idx]
    ```

    调用`get_active_configs` 得到 筛选后待加入的 `active_config`

    把新得到的config加入到train_gap中, 作为返回的一部分
    
    结束条件: 

    1. 如果找不到新的`active_config`, 计算一些tau, 结束active learning loop
        ```python
        logger.info(f'All active configurations reached t = '
                    f'{min_time_active_fs} fs before an error exceeded the '
                    f'threshold of {active_e_thresh:.3f} eV')
        ```

    2. 如果`validata`, 每隔`val_interval`计算一遍`tau`, 如果大于阈值, 结束active learning loop

        ```python
        logger.info('Reached the maximum tau. Active learning = DONE')
        ```
<br>

### `train_ii()`

从一个system里面训练出intra+intermolecular GAP 

返回的是训练用到的数据(intra + inter), GAP模型

**先决条件** : 只有**一种分子**
> Can only train an inter+intra for a single bulk  molecular species

1. 创建一个只有monomer的一个system

    ```python
    molecule = system.molecules[0]
    intra_system = gt.System(box_size=system.box.size)
    intra_system.add_molecules(molecule)
    ```
2. 使用这个intra_system通过主动学习得到`intra_data`
   
```python
    gap = gt.GAP(name=f'intra_{molecule.name}', system=intra_system)
    intra_data, _ = train(intra_system,
                          method_name=method_name,
                          gap=gap,
                          temp=intra_temp,
                          **kwargs)
```
3. 初始化`intra_gap`(指定分子)和`inter_gap`
```python
    intra_gap = gt.gap.IntraGAP(name=f'intra_{molecule.name}',
                                system=system,
                                molecule=molecule)

    inter_gap = gt.InterGAP(name=f'inter_{molecule.name}',
                            system=system)
```
4. 训练`gt.IIGAP(intra_gap, inter_gap)`, 同时得到`inter_data`
   
```python
inter_data, gap = gt.active.train(system,
                                    method_name=method_name,
                                    temp=inter_temp,
                                    gap=gt.IIGAP(intra_gap, inter_gap),
                                    **kwargs)
```

### `train_ss(system, method_name, intra_temp=1000, inter_temp=300, **kwargs)`

`system`里面有两种分子, 少的是溶质, 多的是溶剂

* intra部分
  
    分别训练solute和solvent的intramolecular部分/dataset

    方法: 创建一个只有1个分子的system, 
    ```python
    intra_system = gt.System(box_size=system.box.size)
    intra_system.add_molecules(molecule)
    ```
    
    使用主动学习得到mol_data

    ```python
    mol_data, _ = gt.active.train(intra_system,
                                    gap=gt.GAP(name=f'intra_{molecule.name}',
                                                system=intra_system),
                                    method_name=method_name,
                                    temp=intra_temp,
                                    **kwargs)
    ```

* inter部分

    使用完整的系统建立溶剂和溶液的GAP + inter_GAP

    ```python
    solv_gap = gt.gap.SolventIntraGAP(name=f'intra_{solv.name}', system=system)
    solute_gap = gt.gap.SoluteIntraGAP(name=f'intra_{solute.name}',
                                        system=system, molecule=solute)
    inter_gap = gt.InterGAP(name='inter', system=system
    ```

    训练`intermolecular`部分

    ```python
    inter_data, gap = gt.active.train(system,
                                      method_name=method_name,
                                      gap=gt.gap.SSGAP(solute_intra=solute_gap,
                                                       solvent_intra=solv_gap,
                                                       inter=inter_gap),
                                      temp=inter_temp,
                                      **kwargs)
    ```

<br>

## `md.py`

### `simulation_steps(dt, kwargs)`

`dt`: timesetps

`kwargs`: 跑了多长时间

计算一下, 一共跑了多少步

<br>

### `ase_momenta_string(configuration, temp, bbond_energy, fbond_energy)`

Generate a string to set the initial momenta

加一些和momenta有关的内容

需要自己声明: 
* `bbond_energy`: breaking bond energy

* `fbond_erergy`: fomring bond energy

```bash
MaxwellBoltzmannDistribution(system, {temp} * units.kB)



```

<br>

### `run_gapmd()`

```python
@work_in_tmp_dir(copied_exts=['.xml'])
def run_gapmd(configuration, gap, temp, dt, interval, bbond_energy=None,
              fbond_energy=None, init_temp=None, **kwargs):
```

写一个脚本, 主要都是使用`ase.md.langevin`来跑MD: 

```python
from __future__ import print_function
import quippy
import numpy as np
from ase.io import read, write
from ase.io.trajectory import Trajectory
from ase.md.velocitydistribution import MaxwellBoltzmannDistribution
from ase import units
from ase.md.langevin import Langevin
from ase.md.verlet import VelocityVerlet
system = read("config.xyz")
system.cell = [{a}, {b}, {c}]
system.pbc = True
system.center()

#{gap.ase_gap_potential_str()}
pot = quippy.potential.Potentia}("IP GAP", param_filename="{self.name}.xml")

system.set_calculator(pot)

#ase_momenta_string(configuration, init_temp, bbond_energy, fbond_energy)


traj = Trajectory("tmp.traj", 'w', system)
energy_file = open("tmp_energies.txt", "w")

def print_energy(atoms=system):
    energy_file.write(str(atoms.get_potential_energy())+"\\n")

#dyn = {dynamics_string()}
dyn = Langevin(system, {dt:.1f} * units.fs, {temp} * units.kB, 0.02)

dyn.attach(print_energy, interval={interval})
dyn.attach(traj.write, interval={interval})
dyn.run(steps={n_steps})
energy_file.close()
```

<br>

### `run_mmmd(system, config, temp, dt, interval, **kwargs)`

使用GROMACS跑MD

生成`min.mdp`和`nvt.mdp`文件

使用`POpen`在终端运行`gmx`, Run gmx minimisation and nvt simulations


<br>

### `run_dftbmd(configuration, temp, dt, interval, **kwargs)`

eg.  
    在一个系统上运行ab-initio分子动力学。运行一个10ps的模拟，时间步长为0.5fs，每10步保存300K。

```python
run_dftbmd(config, temp=300, dt=0.5, interval=10, ps=10)
```

需要自己配置下`dftb_in.hsd`文件

直接`Popen`, 命令行运行`DFTB_COMMAND`, 完成模拟

<br>

## `gap.py`

### `predict(gap, data)`
  
  `data`是`configrationSet`的子类, 直接调用`predictions.parallel_gap(gap)`方法, 进行并行预测计算

### `class GAP`

所有 GAP 的父类, 需要用`system`来完成对GAP的初始化

实际上gap类实例化的并不是一个模型, 只是存储相关参数的类(分子种类/`.xml`文件位置等)
  
* `ase_gap_potential_str()`
  
    读取存有GAP模型参数的`***.xml`文件, 在命令行调用`gap_fit`训练得到gap模型之后, 会在本地存一个`***.xml`文件. 这个文件可以用于初始化`quippy.Potential.potential`实例
    
    要有这么一句: 

    ```python
    #ase_gap_potential
    pot = quippy.potential.Potential("IP GAP", param_filename = "xxxx.xml")
    
    system.set_calculator(pot)

    ```
    可以看到, `.xml`文件是用来设置`quippy.potential.Potential`的一个参数

    关于`quippy.potential.Potential`  http://libatoms.github.io/QUIP/potential.html

    <br>

* `train_command()`
    
    还是编写一段str, 使用`POpen`, 在命令行调用`gap_fit`后面需要有的参数

<br>

* `plot_correlation`

* `train(data)`
  
  在给定的data上train GAP.

    ```python
    p = Popen(GTConfig.gap_fit_command + self.train_command(),
            shell=False, stdout=PIPE, stderr=PIPE)
    out, err = p.communicate()
    ```

* `predict()`


### `class Parameters(atom_symbols)`

Parameters for a GAP potential

`atom_symbols`是System里的所有出现的原子

* `n_sparses()`

* `_set_pairs()`

* `_set_soap()`

    基础参数
    
    ```python
    gap_default_soap_params = {'cutoff': 3.0,           # Å
                                'n_sparse': 500,
                                'order': 6,
                                'sigma_at': 0.5,         # Å
                                'delta': 0.1             # eV
                                }
    ```
    `'others'`字段: 根据提供的`atom_symbol`添加所有可能的原子对组合(除与自己)

    ```python
    params["other"] = [s for s in set(atom_symbols)
                        if s+symbol not in added_pairs
                        and symbol+s not in added_pairs]
    ```
    主要作用设置`gap_fit`参数: 
    ```python
    n_species={len(soap["other"])
    ```
    不给 H 加到 SOAP 里面 还得仔细看看



<br>


### `class inter_GAP(GAP)`


### `class intra_GAP(GAP)`

继承自GAP类, 需要使用一个config

相较于父类, 多了一个`_set_mol_idxs`方法, 用于初始化时

* `__init__(self, name, system, molecule)`

    需要用一个`system`来初始化, 同时还有指明`molecule`

    使用``_set_mol_idxs``来标记特定的`molecular`在`system`所有atom里的`idx`

    
* `ase_gap_potential_str`
  
  写新的供`quippy`调用, 用于MD预测模拟的脚本,  重载了父类的方法, 作用是定制一个适合的caculator.

  此时的`run_gap`编写的脚本还包括了`iicaculator`的内容, 变成了: 

    ```python
    import quippy
    import numpy as np
    from ase.io import read, write
    from ase.optimize import BFGS
    from ase.io.trajectory import Trajectory
    system = read("config.xyz")
    system.cell = [{a}, {b}, {c}]
    system.pbc = True
    system.center()

    #ase_gap_potential
    
    #code from iicalculator

    #pot = quippy.potential.Potential("IP GAP", param_filename = "xxxx.xml")
    
    intra_gap = quippy.potential.Potential("IP GAP", param_filename="{self.name}.xml")
    intra_gap.mol_idxs = {self.mol_idxs}
    pot = IntraCalculator(intra_gap)

    system.set_calculator(pot)

    #{min_section}
    traj = Trajectory('{traj_name}', 'w', system)
    dyn = BFGS(system)
    dyn.attach(traj.write, interval=1)
    dyn.run(fmax={float(max_force)})

    np.savetxt("energy.txt",\
            np.array([system.get_potential_energy()]))
    np.savetxt("forces.txt", system.get_forces())
    write("config.xyz", system)

    ```
<br>

### `class SolventIntraGAP(IntraGAP)`

    继承自`IntraGAP`, 重载函数`_set_mol_idxs`

    将`self.mol_idx`设置为system里数量最多的分子(溶剂)的下标

<br>

### `SoluteIntraGAP(IntraGAP)`

    pass

<br>

### `class GAPEnsemble`

允许通过sub-sampling进行误差估计的, GAP模型的集成

* 初始化: 
  
  可以使用`system`或`gap`来初始化多个GAP.
    ```python
    #init with gt.system
    self.gaps = [GAP(f'{name}_{i}', system) for i in range(int(n))]

    #init with gt.gap
    self.gaps = [deepcopy(gap) for _ in range(int(n))]
    self.training_data = gap.training_data
    ```
* `predict_energy_error()`

    计算不同的预测能量直接的standard deviation

    并行, 对每一个gap针对每一个config进行预测, 得到预测的结果

    ```python
    with Pool(processes=GTConfig.n_cores) as pool:

        # Apply the method to each configuration in this set
        for i, config in enumerate(configs):
            for j, gap in enumerate(self.gaps):
                result = pool.apply_async(func=run_gap,
                                            args=(config, None, gap))
                results[i, j] = result
    ```

* `sub_sampled_data(self, data, gap=None, random=True)`

    使用采样后的部分训练集, 完成gap的训练

    调用`config`里面的`remove_random`方法, 从整个训练集里面采样出一些东西出来
    
* `train(self, data=None, sub_sample=False)`

    每个gap, 可以使用不同的`sub_sampled_data`进行训练

    使用`gap_fit`完成训练

    ```python
    gap.train(data=training_data)
    ```

<br>

### `class AdditiveGAP`

GAP结果是两个GAP计算值的和

```python
pot = {potential_class}("Sum", pot1=pot1, pot2=pot2)
```

<br>

### `class IIGAP`

Inter+intra GAP where the inter is evaluated in a different box

Collective GAP comprised of inter and intra components

有`self.inter`/`self.intra`, 两个属性, 需要使用两个GAP来同时初始化, 作为参数初始化`IICalculator`


* `train(data)`

    >Training the intermolecular component of the potential. Expecting data that is free from intra energy and force

    要求**先**要训练`intra_gap`, 即要有`xxx.intra.xml`文件存在

* `ase_gap_potential_str(calc_str='IICalculator(intra_gap, inter_gap)')
 
    同样还是`ase`调用时需要使用到的一些脚本

    `iicalculator.py`

    里面有继承自`ase.calculators.calculator`的一些子类, 用于计算`contribution`: 


<br>

### `SSGAP(IIGAP)`

* 训练:

    需要先训练好 solute intra GAP --->`{self.solute_intra.name}.xml`

* 预测的脚本

```python

solute_gap = quippy.potential.Potential("IP GAP", param_filename="{self.solute_intra.name}.xml")

solute_gap.mol_idxs = {self.solute_intra.mol_idxs}

inter_gap = quippy.potential.Potential("IP GAP", param_filename="{self.inter.name}.xml")

intra_gap = quippy.potential.Potential("IP GAP", param_filename="{self.intra.name}.xml")

intra_gap.mol_idxs = {self.intra.mol_ixs}        

pot = SSCalculator(solute_gap, intra_gap, inter_gap)
```




<br>

# `iicalculator.py`


## `class IICalculator`
集合了两种ASE里`ase.calculators.calculator`的`Calculator`类, 用于分别计算intra和inter的贡献. 

* intra部分: 

    初始化**分子索引矩阵**, 统一将分子间距离展开(原子将从0开始被传递给expanded_atoms索引)

    

> The intramolecular term is evaluated by expanding all the intermolecular distances uniformly by initialise a matrix of molecular indexes, as the atoms will be passed to expanded_atoms indexed from 0 then we need to make sure the lowest value in the array is zero (the first atom index)

### `__init__(self, intra, inter=None, expansion_factor=10, **kwargs)`

调用父类的初始函数, 设置必要的属性

需要`intra`和`inter`来初始化

将`mol_idxs`设置为`intra.mol_idxs - min(self.mol_idxs)`, 即从零开始

<br>

### `expanded_atoms(self, atoms)`

扩大intra里, 同一类原子的box, **所有**的分子坐标也以**质心**为原点, 按照**同样的比例扩大**

$$ (\frac{diag_{now}}{diag_{pre}} - 1) * mass$$

<br>

### `calculate(self, atoms=None, properties=None, system_changes=None, **kwargs)`

分开计算`inter`和`intra`, `intra`的atom坐标进行相应的调整(等比例缩放)

```python
intra_atoms = self.expanded_atoms(atoms)
intra_atoms.set_calculator(self.intra)

inter_atoms = atoms.copy()
inter_atoms.set_calculator(self.inter)
```
    
计算能量和里, 用到了`ase`里`atom`类的`get_potential_energy`和`get_forces`类, 使用attach的calculator计算.

<br>

## `class IntraCalculator(IICalculator)`

只计算intra-molecular部分的能量

具体: 只取出感兴趣的那一类atom, 缩放, 计算能量和力

```python
# Create a new set of atoms, which may not include every atom
atoms_subset = Atoms(numbers=[atoms.numbers[i] for i in mol_idxs],
                        positions=atoms.positions[mol_idxs],
                        pbc=True,
                        cell=atoms.cell)

intra_atoms = self.expanded_atoms(atoms_subset)
intra_atoms.set_calculator(self.intra)
```

<br>

## `SSCalculator`

> Combination of three ASE calculators to calculate the total energy and forces for a solute solvent system where the solute is described with one GAP ASE calculator as are the intramolecular modes of the  solvent, the remaining interactions are calcd with a separate GAP

三种ASE calculator的组合来计算溶质溶剂系统的总能量和力，其中溶质用一个GAP ASE calculator描述，溶剂的intramolecular部分也是如此，其余的相互作用用一个单独的GAP计算

### `def __init__(self, solute_intra, solvent_intra, inter, expansion_factor=10, **kwargs)`

* 分别设置好
      1. `self.solvent_idxs`
   
      2. `self.solute_idxs`
   

###  `calculate(self, atoms=None, properties=None, system_changes=None, **kwargs)`

1. 设置`atoms`系统

    ```python
    solute_atoms = Atoms(numbers=[atoms.numbers[i] for i in self.solute_idxs],
                            positions=atoms.positions[self.solute_idxs],
                            pbc=True,
                            cell=atoms.cell)

    solvent_atoms = Atoms(numbers=[atoms.numbers[i] for i in self.solvent_idxs],
                            positions=atoms.positions[self.solvent_idxs],
                            pbc=True,
                            cell=atoms.cell)
    ```

2. set calculator

```python
solute_atoms.set_calculator(self.solute_intra)

solv_intra_atoms = self.expanded_atoms(solvent_atoms)
solv_intra_atoms.set_calculator(self.intra)

inter_atoms = atoms.copy()
inter_atoms.set_calculator(self.inter)
```

3. get results

分成三份贡献: inter_atoms/ solvent_intra/ solute_intra

```python
self.results["energy"] = (inter_atoms.get_potential_energy() +
                            solv_intra_atoms.get_potential_energy() +
                            solute_atoms.get_potential_energy())
self.results["free_energy"] = self.results["energy"]

forces = inter_atoms.get_forces()
forces[self.solute_idxs] += solute_atoms.get_forces()
forces[self.solvent_idxs] += solv_intra_atoms.get_forces()

self.results["forces"] = force

```



<br>

# 问题:

1. 反复出现的`max_force`
>  :param max_force:   
> (float) Maximum force on an atom within aconfiguration. If None then only a **single point energy evaluation** is performed

这个指的是个什么, `single point energy evaluation`又是什么

2. ase相关的一些类和方法, 包括autode里面很多方法还是基于ase里面继承来的
   
    * potential: 定义在quippy里, 是`ase.calculators.calculator.Calculato`的子类

    以支持 interatomic GAP 在内的一些计算模型

    初始化方法: 
    
    ```python
    pot = quippy.potential.Potential("IP GAP", param_filename = "xxxx.xml")
    ```
   
    * caculator: 一个黑盒
        
      * 输入: ase的Atoms实例(包括atom位置和数量)
  
      * 输出: 这个结构的能量和力

        > For ASE, a calculator is a black box that can take atomic numbers and atomic positions from an Atoms object and calculate the energy and forces and sometimes also stresses.

      * 使用方法: attach a calculator object to your atoms object:

        ```python
        atoms = read('molecule.xyz')

        from ase.calculators.abinit import Abinit

        calc = Abinit(...)
        atoms.calc = calc
        e = atoms.get_potential_energy()
        
        ```

<br>

1. quip的一些方法

比如计算gap的方差
```python
def run_quip():
    """Use QUIP on a set of configurations to predict the variance"""
    with open('quip.out', 'w') as tmp_out_file:
        subprocess = Popen(gt.GTConfig.quip_command +
                            ['calc_args=local_gap_variance', 'E=T', 'F=T',
                            'atoms_filename=tmp_traj.xyz',
                            f'param_filename={gap_name}.xml'],
                            shell=False, stdout=tmp_out_file, stderr=PIPE)
        subprocess.wait()

    return None
```


# 重点

* `xml`文件是用于存GAP模型参数的, GAP类的属性只包括分子的类型, 数量, 储存`.xml`文件的地址等一些简单的信息, 主要是通过`quippy`读取`.xml`文件或者在命令行里跑

* 
  * 训练
    
    通过写脚本, 运行命令行的`gap_fit`指令
  
  * 预测 <br><br>
   
   1. 通过`run_gap`写`gap.py`的python脚本, 调用`quippy`, 设置好pot(attach calculator)
   2. 通过它调用`ase`里`Atoms`类, 使用`xxx.get_potential_energy()`和`xxx.get_potential_forces()`完成力和能量的计算
   3. 将预测的结果存在文件里

    ```python
    #根据使用的GAP类型进行对应的调整, 最少有xml文件的位置
    gap.ase_gap_potential_str()

    system.set_calculator(pot)

    system.get_potential_energy()
    system.get_forces()
    ```

  * 分子动力学

    使用ase的MD方法

    参考[`run_gapmd()`](#run_gapmd)方法

<br>

* 主动学习

参考一下几种主动学习添加config的标准

1. 与其它类方法比较误差
     
  使用下列方法中的一种与GAP, MD一段时间之后的结果进行对比 

`('dftb', 'gpaw', 'orca', 'xtb', 'cp2k')`


 [`get_active_config_diff()`](#get_active_config_diff)

2. 集成GAPs, 预测同一帧, 比较预测结果的标准差(query by committee)
 [`get_active_config_qbc(config, gap, temp, std_e_thresh, max_time_fs)`](#get_active_config_qbcconfig-gap-temp-std_e_thresh-max_time_fs)

3. gp预测方差
   quip的输出里包括了 每一组原子的predicted atomic variance
   


 [`get_active_config_gp_var(config, gap, temp, var_e_thresh, max_time_fs)`](#get_active_config_gp_varconfig-gap-temp-var_e_thresh-max_time_fs)


 [`get_active_configs()`](#get_active_configs)

<br>

* train_ii 
  
  从一个system里训练出intra+inter GAP, 同时得到(intra_data和inter_data)

  intra_data是在一个只有一种分子(monomer)的system里, 训练gap得到的

<br>

* intra 力/能量的计算(预测)
  
  单独给intra的calculator进行了调整, 针对所研究的**特定分子**, 扩大box, 将原子位置以质心为原点, 按比例扩大
