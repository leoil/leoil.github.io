如果要写一个基于ASE的Calculator interface的NN calcultor, 需要实现以下method:

首先需要讲我们需要研究的config转换成ase Atom类的实例

```python
import numpy as np

class Calculator:
    """ASE calculator.

    A calculator should store a copy of the atoms object used for the
    last calculation.  When one of the *get_potential_energy*,
    *get_forces*, or *get_stress* methods is called, the calculator
    should check if anything has changed since the last calculation
    and only do the calculation if it's really needed.  Two sets of
    atoms are considered identical if they have the same positions,
    atomic numbers, unit cell and periodic boundary conditions."""
	
    def get_potential_energy(self, atoms=None, force_consistent=False):
        """Return total energy.

        Both the energy extrapolated to zero Kelvin and the energy
        consistent with the forces (the free energy) can be
        returned."""
        return 0.0


    def get_forces(self, atoms):
        """Return the forces."""
        return np.zeros((len(atoms), 3))


    def get_stress(self, atoms):
        """Return the stress."""
        return np.zeros(6)


    def calculation_required(self, atoms, quantities):
        """Check if a calculation is required.

        Check if the quantities in the *quantities* list have already
        been calculated for the atomic configuration *atoms*.  The
        quantities can be one or more of: 'energy', 'forces', 'stress',
        'charges' and 'magmoms'.

        This method is used to check if a quantity is available without
        further calculations.  For this reason, calculators should
        react to unknown/unsupported quantities by returning True,
        indicating that the quantity is *not* available."""
        return False
```





学习下1.x的tensorflow

模型层直接是直接的变量的组合, 没有keras和后来2.x版本的各种类, 搭积木一样的操作

一个模块的变量是写在一个variable_scope/name_scope下面的,  最后要summay也是把一组一组的模块堆在一起, 远不如keras和pytoch直观.

只能对着tensorboard一点点瞎琢磨



跑模型是用`tf.Session.run():`

有如下定义

``run(self, fetches, feed_dict=None, options=None, run_metadata=None)``

`fetches`主要指从计算图中**取回**计算结果进行放回的那些placeholder和变量

​		指明取出的值

`feed_dict`则是将对应的数据传入计算图中占位符，它是字典数据结构只在调用方法内有效

​		字典类型, 覆盖原有计算图中的tensor的值



```python
with tf.control_dependencies(
    [apply_gradient_op,
     summary_op,
     ema_op] + update_ops + dependencies):
    train_op = tf.no_op(name='train')
```

确保执行顺序

