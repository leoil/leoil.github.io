[TOC]



# 每天一点C的知识



* `char *a[10];` :    array of `10` pointers to `char`

* `char (*a)[10];`  :   pointer to array of `10` `char`'s

* ```cpp
  PairDeepMD::PairDeepMD(LAMMPS *lmp) : Pair(lmp)
  ```

  ## 构造函数

  调用父类的构造函数

  一个类的构造函数可以调用多个类的构造函数:

  ```cpp
  ComputeDeeptensorAtom::ComputeDeeptensorAtom(LAMMPS *lmp, int narg, char **arg) :
    Compute(lmp, narg, arg),
    dp(lmp),
    tensor(nullptr)
       
  ```

  * 构造函数的初始值列表
  * 默认实参
  * 委托构造函数(C++11): 用所属类的其它构造函数执行自己的初始过程

  

  ## 抽象类

  至少有一个virtual 函数, -->可以看成接口

  继承的子类需要override

  * 派生类必须对在其内部对所有重新定义的虚函数进行声明. 

    可以在这些函数前加上`virtual` 

    或 (C++11)允许派生类显式的注明它对基类里定义的哪个`virtual`函数进行了改写, 后面加上`override`, 此时可以只实现基类的一部分纯虚函数(如果这些虚函数在基类里已经定义(实现)过了).

    声明与定义的区别: 

    ```cpp
    virtual void compute(int, int) = 0;	//声明--->必须要在之后定义;
    virtual void compute_inner() {}		//定义
    ```
  
    
  
  * 使用基类的引用(指针)调用一个虚函数时, 会执行动态绑定
  
  
  
   * 派生类与基类: protected/private
  
     1. private成员只能被本类成员（类内）和友元访问，不能被派生类访问；
  
     2. protected成员可以被派生类访问。
  
  * 派生类的构造函数
  
    必须使用基类的构造函数来初始化继承来的基类部分--->在初始化列表中, 先调用基类的构造函数
  
    ```cpp
    class Bulk_quota: public Quota {
        Bulk_quota(const std::string& book, double p, std::size_t qty, double disc):
                        Quota(book, p), min_qty(qty), discount(disc){
                            ...
                        }
    };
    ```
  
  * 直接基类/间接基类
  
  * 动态绑定(虚函数)/ 类型转换(基类/派生类) 
  
    多态: 动态绑定--当且仅当通过指针或引用调用虚函数时, 才会在**运行**时动态解析.
  
    ​		如果通过对象对函数调用, 因为对象的类型时明确的, 函数调用会在**编译**时被绑定到对应的函数上.
  
  ## 命名空间
  
  用来避免太多的重名函数出现
  
  每个namespace都是一个作用域
  
  可以不连续
  
  * 定义类\声明作为类接口的函数/对象---> 放在头文件里
  
  * 成员的定义: 放在源文件里      
  
  * 使用namespace成员: 
  
    * using declaration(声明)
  
      一次引入一个memeber
  
      引入的名字作用域与using语句本身相同, 类似于在当前域里创建了一个别名
  
    * using directive(指示)
  
      使用后该namespace里所有的名字都是可见的了.
  
      可能存在二义性问题:(书P705)
  
      ```cpp
      int j;
      int f(){
          using namespace blip;
          blip::j;
          ::j;
      }
      ```
  
    * 头文件中的using
  
      会将namespace里的所有名字注入include了该header的文件中
    
    
    
    ## `template<typename VT>`
    
    
    
    
  
  ![image.png](http://ww1.sinaimg.cn/large/002WGNPWly1gu02y1j4abj617w0lwatj02.jpg)

所以理论上来说, 只要是继承于Pair Class (一个虚拟类/接口, 很多virtual需要自己实现) 的子类, 都可以被当成是一个pair style, 把几个关键的函数重新实现一遍即可



# `pair_deepmd.cpp`

构建了`PairDeepMD` 类, 继承`lammps`的`Pair`类

```cpp
namespace LAMMPS_NS {

class PairDeepMD : public Pair {
 public:
  PairDeepMD(class LAMMPS *);
  virtual ~PairDeepMD();
  virtual void compute(int, int);
  virtual void *extract(const char *, int &);
  void settings(int, char **);
  virtual void coeff(int, char **);
  void init_style();
  virtual void write_restart(FILE *);
  virtual void read_restart(FILE *);
  double init_one(int i, int j);
  int pack_reverse_comm(int, int, double *);
  void unpack_reverse_comm(int, int *, double *);
  void print_summary(const std::string pre) const;
  int get_node_rank();
  std::string get_file_content(const std::string & model);
  std::vector<std::string> get_file_content(const std::vector<std::string> & models);
 protected:  
  virtual void allocate();
  double **scale;

private:  
  deepmd::DeepPot deep_pot;
  deepmd::DeepPotModelDevi deep_pot_model_devi;
  unsigned numb_models;
  double cutoff;
  int numb_types;
  std::vector<std::vector<double > > all_force;
  std::ofstream fp;
  int out_freq;
  std::string out_file;
  int dim_fparam;
  int dim_aparam;
  int out_each;
  int out_rel;
  int out_rel_v;
  bool single_model;
  bool multi_models_mod_devi;
  bool multi_models_no_mod_devi;
  bool is_restart;
#ifdef HIGH_PREC
  std::vector<double > fparam;
  std::vector<double > aparam;
  double eps;
  double eps_v;
#else
  std::vector<float > fparam;
  std::vector<float > aparam;
  float eps;
  float eps_v;
#endif
  void make_ttm_aparam(
#ifdef HIGH_PREC
      std::vector<double > & dparam
#else
      std::vector<float > & dparam
#endif
      );
  bool do_ttm;
  std::string ttm_fix_id;
  int *counts,*displacements;
  tagint *tagsend, *tagrecv;
  double *stdfsend, *stdfrecv;
};

}
```

各个函数:

## `stringCmp()`

比较两字符串, 作为sort的cmp函数



## `get_node_rank()`

使用到了`MPI_Comm_rank`函数: 

```cpp

int PairDeepMD::get_node_rank() {
    char host_name[MPI_MAX_PROCESSOR_NAME];
    memset(host_name, '\0', sizeof(char) * MPI_MAX_PROCESSOR_NAME);
    char (*host_names)[MPI_MAX_PROCESSOR_NAME];
    int n, namelen, color, rank, nprocs, myrank;
    size_t bytes;
    MPI_Comm nodeComm;
    
    //rank = 0---> 进程编号
    //rank == 0 root进程, 之后创建的进程rank++
    MPI_Comm_rank(MPI_COMM_WORLD, &rank);
    //nprocs = 1 ---->进程数
    MPI_Comm_size(MPI_COMM_WORLD, &nprocs);
    //host_name = "localhost"
    MPI_Get_processor_name(host_name,&namelen);

    bytes = nprocs * sizeof(char[MPI_MAX_PROCESSOR_NAME]);
    host_names = (char (*)[MPI_MAX_PROCESSOR_NAME]) malloc(bytes);
    for (int ii = 0; ii < nprocs; ii++) {
        memset(host_names[ii], '\0', sizeof(char) * MPI_MAX_PROCESSOR_NAME);
    }
    
    strcpy(host_names[rank], host_name);
	
    //Bcast向所有进程广播host_names[n]的值
    for (n=0; n<nprocs; n++)
        MPI_Bcast(&(host_names[n]),MPI_MAX_PROCESSOR_NAME, MPI_CHAR, n, MPI_COMM_WORLD);
    qsort(host_names, nprocs,  sizeof(char[MPI_MAX_PROCESSOR_NAME]), stringCmp);

    color = 0;
    for (n=0; n<nprocs-1; n++)
    {
        if(strcmp(host_name, host_names[n]) == 0)
        {
            break;
        }
        if(strcmp(host_names[n], host_names[n+1]))
        {
            color++;
        }
    }

    MPI_Comm_split(MPI_COMM_WORLD, color, 0, &nodeComm);
    MPI_Comm_rank(nodeComm, &myrank);

    MPI_Barrier(MPI_COMM_WORLD);
    int looprank=myrank;
    // printf (" Assigning device %d  to process on node %s rank %d, OK\n",looprank,  host_name, rank );
    free(host_names);
    return looprank;
}
```

给host_names分配名字空间, 将`host_names[rank] = host_name`

这一段是mpi编程的内容, rank = 0

* MPI相关知识

  1. `MPI_COMM_WORLD`

     communicator-->通信器: 一组可以互相发送消息的进程集合

     该变量包含程序中所有的MPI进程

     进程号是在通信器被创建时被赋值的(同一进程, 不同Communicator进程号不同)

  2. `MPI_Comm_rank`

     获得进程号

  3. `MPI_Comm_size`

     任务由多少个进程来进行并行计算

     

  4. `MPI_Bcast`

     ```cpp
     int MPI_Bcast(
     	void * data_p;
     	int count;
     	MPI_Datatype datatype;
     	int source_proc;
     	MPI_Comm comm;
     );
     ```

     广播发生时, 一个进程会把同一份数据(data_p)传给同一comm里的所有进程.

     root节点调用时, data里存的值会被发送到各个节点, 当子进程调用的同一`MPI_Bcast`函数时, data会被赋予从root进程接受到的值

  5. `MPI_Comm_split`

     ```cpp
     MPI_Comm_split(
         MPI_Comm comm, //原来的整个Comm
         int color,	//color用不同process的被分成一个color, 
         int key,	//确定每个
         MPI_Comm* newcomm)	//分配好的新Comm
     ```

     同一color值的进程会被分配给同一个Comm, 

     

     ````cpp
     for (n=0; n<nprocs-1; n++)
     {
         if(strcmp(host_name, host_names[n]) == 0)
         {
             break;
         }
         if(strcmp(host_names[n], host_names[n+1]))
         {
             color++;
         }
     }
     ```
     
     * `MPI_Barrier(MPI_COMM_WORLD);`
     
       阻塞
     
     

  ## `std::string PairDeepMD::get_file_content(const std::string & model)`

  有一个多态操作, `std::vector<std::string> PairDeepMD::get_file_content(const std::vector<std::string> & models) `读一个list的model里的数据

  读取model name对应的model文件, 以string的形式保存, 广播给其它的进程

  

  ```cpp
  
  std::string PairDeepMD::get_file_content(const std::string & model) {
    int myrank = 0, root = 0;
    MPI_Comm_rank(MPI_COMM_WORLD, &myrank);
    int nchar = 0;
    std::string file_content;
    //由root进程 获取model(string)的大小
    if (myrank == root) {
      deepmd::check_status(tensorflow::ReadFileToString(tensorflow::Env::Default(), model, &file_content));
      nchar = file_content.size();
    }
    //广播消息
    MPI_Bcast(&nchar, 1, MPI_INT, root, MPI_COMM_WORLD);  
    char * buff = (char *)malloc(sizeof(char) * nchar);  
    
    //root进程分配buff空间
    if (myrank == root) {  
      memcpy(buff, file_content.c_str(), sizeof(char) * nchar);
    }
    //广播消息buff的值
    MPI_Bcast(buff, nchar, MPI_CHAR, root, MPI_COMM_WORLD);
    file_content.resize(nchar);
    for (unsigned ii = 0; ii < nchar; ++ii) {
      file_content[ii] = buff[ii];
    }
    free(buff);
    return file_content;
  }
  ```

  包括这么一段, root进程调用tensorflow里的方法进行读取, 并将得到的结果动态的传到其它进程

  `check_status`是看有没有读进去(返回值)

  ```cpp
  deepmd::check_status(tensorflow::ReadFileToString(tensorflow::Env::Default(), model, &file_content));
  ```

这个函数的具体用法: 

在`env.cc`里面可以找到, 根据`fname`把`model`以`string`的形式读到`data`里

`Status ReadFileToString(Env* env, const string& fname, string* data)`

```cpp
Status ReadFileToString(Env* env, const string& fname, string* data) {
  uint64 file_size;
  Status s = env->GetFileSize(fname, &file_size);
  if (!s.ok()) {
    return s;
  }
  std::unique_ptr<RandomAccessFile> file;
  s = env->NewRandomAccessFile(fname, &file);
  if (!s.ok()) {
    return s;
  }
  data->resize(file_size);
  char* p = &*data->begin();
  StringPiece result;
  s = file->Read(0, file_size, &result, p);
  if (!s.ok()) {
    data->clear();
  } else if (result.size() != file_size) {
    s = errors::Aborted("File ", fname, " changed while reading: ", file_size,
                        " vs. ", result.size());
    data->clear();
  } else if (result.data() == p) {
    // Data is already in the correct location
  } else {
    memmove(p, result.data(), result.size());
  }
  return s;
}
```

## `PairDeepMD::PairDeepMD(LAMMPS *lmp) ` 构造函数







好像必须要写的几个进程: 

## `void PairDeepMD::compute(int eflag, int vflag)`

绕不过去的, 这个是最主要的函数

1. ```cpp
    int nlocal,nghost;            // # of owned and ghost atoms on this proc
   ```

2. lmp_list
   
   这个list是个啥 一直没找到
   
   ```cpp
   if(do ghost)
   	deepmd::InputNlist lmp_list (list->inum, list->ilist, list->numneigh, list->firstneigh);
   ```
   
3. 计算

    ```cpp
    //do ghost
    deep_pot.compute (dener_, dforce_, dvirial_, dcoord_, dtype, dbox_, nghost, lmp_list, ago, fparam, daparam);
    ```

    传进去的内容, 基本是`vector`数组

    `dener_, dforce_, dvirial_`这三个是需要存结果进去的

    `dcoord_, dtype, dbox_, nghost, lmp_list, ago, fparam, daparam`这些都是有值存进去的`vector`

    `fparm/dparam`意义暂时未知

    



设置他需要的参数
## `void PairDeepMD::settings(int narg, char **arg)`

使用`DeepPot.cc`里定义的`DeepPot`类的`init`来初始化模型

如果有多模型, 再初始化下`DeepPotModelDevi`类下的实例, 用于

主要是根据调用pair_style时后面跟着的args进行一个parse. 根据里面的值设置pair_deepmd类的对应的值

支持多模型

基本都是比较常规的东西, 需要记录一下的:

* pair类下

  ```cpp
  //def: Pair.h --> Class Pair
    int comm_forward;              // size of forward communication (0 if none)
    int comm_reverse;              // size of reverse communication (0 if none)
    int comm_reverse_off;          // size of reverse comm even if newton off
  //
  comm_reverse = numb_models * 3;
  all_force.resize(numb_models);
  ```

* 获取当前的进程号

  ```cpp
  //class LAMMPS_NS::LAMMPS类下的:
  //	class Comm *comm;              // inter-processor communication
  
  comm->me == 0
  ```

  











# `DeepPot.cc`

和模型有关的类

在定义时就有的变量: 

```cpp
  tensorflow::Session* session;
  int num_intra_nthreads, num_inter_nthreads;
  tensorflow::GraphDef graph_def;
```

## `init`

```cpp
DeepPot::init (const std::string & model, const int & gpu_rank, const std::string & file_content)
```

```cpp
  SessionOptions options;
  get_env_nthreads(num_intra_nthreads, num_inter_nthreads);
  options.config.set_inter_op_parallelism_threads(num_inter_nthreads);
  options.config.set_intra_op_parallelism_threads(num_intra_nthreads);
```

*  从pb文件里恢复模型

  model是文件名

  > ```cpp
  > Status ReadBinaryProto(Env* env, const string& fname,
  >                        protobuf::MessageLite* proto)
  > ```

```cpp
if(file_content.size() == 0)
    check_status (ReadBinaryProto(Env::Default(), model, &graph_def));
else
    graph_def.ParseFromString(file_content);
```

* Session是从哪来的?

```cpp
 check_status (NewSession(options, &session));
 check_status (session->Create(graph_def));
```



## `void DeepPot::compute`---> with lmp_list



```cpp
DeepPot::
compute (ENERGYTYPE &			dener,
	 std::vector<VALUETYPE> &	dforce_,
	 std::vector<VALUETYPE> &	dvirial,
	 const std::vector<VALUETYPE> &	dcoord_,
	 const std::vector<int> &	datype_,
	 const std::vector<VALUETYPE> &	dbox, 
	 const int			nghost,
	 const InputNlist &		lmp_list,
	 const int&			ago,
	 const std::vector<VALUETYPE> &	fparam,
	 const std::vector<VALUETYPE> &	aparam_)
```

* `fwd_map, bkw_map`

  ```cpp
  //def
  std::vector<int> datype, fwd_map, bkw_map;
  int nghost_real;
  
  select_real_atoms(fwd_map, bkw_map, nghost_real, dcoord_, datype_, nghost, ntypes);
  ```

  涉及的函数

  * `select_by_type`

    ```cpp
    deepmd::select_by_type(fwd_map, bkw_map, nghost_real, dcoord_, datype_, nghost, sel_type);
    ```

    调用给`fwd_map`和`bkw_map`赋值, 大小与所有原子数一致

    ```cpp
    fwd_map.resize(nall);
    bkw_map.clear();
    bkw_map.reserve(nall); 
    ```

    对于每个原子, 如果它的类型[1,3,5,7,9] <  类型种类(5) 

    ​	加入到`bkw_map`里,

    ​		如果ii < local_atom数:

    ​			nloc_real += 1;

    ​		如果大于: 

    ​			nghost_real += 1;

    ​	fwd_map[ii] = 当前real的个数

    如果找不到

    ​	fwd_map[ii] = -1;

    

    判断是否real:  id (atom_type[ii]) < ntypes,

    fwd_map: real_atom的idx 或 -1

    bkw_map: real_atom的个数

  `select_real_atoms`

* 根据bk 和 fwd map调整能量和力的size, 只计算一部分

  ```cpp
   // resize to nall_real
  dcoord.resize(bkw_map.size() * 3);
  datype.resize(bkw_map.size());
  // fwd map
  select_map<VALUETYPE>(dcoord, dcoord_, fwd_map, 3);
  select_map<int>(datype, datype_, fwd_map, 1);
  ```

* 

## `static void run_model()`

调用Session跑模型



[tf.Session.run doc](https://www.tensorflow.org/versions/r1.15/api_docs/python/tf/Session#run)



```cpp
  check_status (session->Run(input_tensors, 
			    {"o_energy", "o_force", "o_atom_energy", "o_atom_virial"}, 
			    {}, 
			    &output_tensors));
  
```

> ```cpp
> Status Run(
>   const FeedType & inputs,
>   const std::vector< Output > & fetch_outputs,
>   const std::vector< Operation > & run_outputs,
>   std::vector< Tensor > *outputs
> ) const 
> ```
>
> Evaluate the tensors in `fetch_outputs `.
>
> The values are returned as `Tensor `objects in `outputs `. The number and order of `outputs `will match `fetch_outputs `.



# `common.cc`

## `session_get_scalar`

模板类

```cpp
template<typename VT>
VT
deepmd::
session_get_scalar(Session* session, const std::string name_, const std::string scope) 
{
  std::string name = name_;
  if (scope != "") {
    name = scope + "/" + name;
  }
  std::vector<Tensor> output_tensors;
  deepmd::check_status (session->Run(std::vector<std::pair<std::string, Tensor>> ({}), 
			    {name.c_str()}, 
			    {}, 
			    &output_tensors));
  Tensor output_rc = output_tensors[0];
  auto orc = output_rc.flat <VT> ();
  return orc(0);
}
```

跑一遍session

```cpp
  deepmd::check_status (session->Run(std::vector<std::pair<std::string, Tensor>> ({}), 
			    {name.c_str()}, 
			    {}, 
			    &output_tensors));
```



## `session_input_tensors`

根据`dcoord_`等数据, 构建一个`input_tensors,`, 类似于`feed_dict`

```cpp
session_input_tensors (
    std::vector<std::pair<std::string, Tensor>> & input_tensors,
    const std::vector<deepmd::VALUETYPE> &	dcoord_,
    const int &					ntypes,
    const std::vector<int> &			datype_,
    const std::vector<deepmd::VALUETYPE> &	dbox,		    
    InputNlist &				dlist, 
    const std::vector<deepmd::VALUETYPE> &	fparam_,
    const std::vector<deepmd::VALUETYPE> &	aparam_,
    const deepmd::AtomMap<deepmd::VALUETYPE>&	atommap,
    const int					nghost,
    const int					ago,
    const std::string				scope)
```



# `neighbor_list.cc`



# 修改Physnet代码

## 明确inference阶段, 模型的输入

最简单的:

```cppp
feed_dict = {i_Z: atoms.get_atomic_numbers(), i_R: atoms.get_positions(), 
			i_idx_i: idx_i, i_idx_j: idx_j, 
			i_offsets: offsets} 
```

最朴素的`idx_i/idx_j` : 每个`i,j` 1-1匹配成对
```cpp
count = 0
for i in range(N):
    for j in range(N):
        if i != j:
            idx_i[count] = i
            idx_j[count] = j
            count += 1
```
高级版: 从Neighborhood list里推理得到

```cpp
sr_idx_i, sr_idx_j, sr_S = neighbor_list('ijS', atoms, self.sr_cutoff)
```







## 保存/读取/inference pb file

deepmd里, 训练完的模型是pb格式的, 与checkpoint的保存方式不同, pb形式是去持久化的.

原来的Physnet里, 从输入到输出energy和force还有charge, 需要在NNCalculator里面再继续处理下才能得到, 之前保存的checkpoint包括了训练是的计算图.

如果要在c++里调用pb, 需要将模型转换.

读取出之前的sess, 再加上新的操作

参考这篇文章

[TensorFlow 保存模型为 PB 文件](https://zhuanlan.zhihu.com/p/32887066)

* 保存pb

  ```cpp
  #save pb
  #convert_variables_to_constants 需要指定output_node_names，list()，可以多个
  constant_graph = graph_util.convert_variables_to_constants(self.sess, self.sess.graph_def, ['result/charge','result/energy','result/forces'])
      # 写入序列化的 PB 文件
      with tf.gfile.FastGFile('model.pb', mode='wb') as f:
  f.write(constant_graph.SerializeToString())    
  ```

  需要注意这里传入的`output_node_names`可以为一个list, 如果名字是定义再`name_scpoe`下面, 需要加上这一部分

  如果不确定名字可以通过`var.op.name`查看, `var.name`好像不行

* 保存checkpoint

  ```cpp
  saver = tf.train.Saver(max_to_keep=50)
      log_dir = '.'
      step_checkpoint = os.path.join(log_dir,  'model.ckpt')
      step = 1234
      saver.save(self.sess, step_checkpoint, global_step=step)     
      
  ```

* pb模型的恢复, 给输入得到输出

  ```cpp
  from tensorflow.python.platform import gfile
  
  sess = tf.Session()
  with gfile.FastGFile('model.pb', 'rb') as f:
      graph_def = tf.GraphDef()
      graph_def.ParseFromString(f.read())
      sess.graph.as_default()
      tf.import_graph_def(graph_def, name='') # 导入计算图
  
  # 需要有一个初始化的过程    
  sess.run(tf.global_variables_initializer())
  
  #定义input的名称---作为feed_list的key        
  i_Q_tot      = np.array(1*[charge])
  i_Z          = sess.graph.get_tensor_by_name("Z:0") 
  i_R          = sess.graph.get_tensor_by_name("R:0")        
  i_idx_i      = sess.graph.get_tensor_by_name("idx_i:0") 
  i_idx_j      = sess.graph.get_tensor_by_name("idx_j:0") 
  i_offsets    = sess.graph.get_tensor_by_name("offsets:0") 
  i_sr_idx_i   = sess.graph.get_tensor_by_name("sr_idx_i:0") 
  i_sr_idx_j   = sess.graph.get_tensor_by_name("sr_idx_j:0") 
  i_sr_offsets = sess.graph.get_tensor_by_name("sr_offsets:0") 
  
  #计算得到feed_list的val        
  N = len(atoms)
  idx_i = np.zeros([N*(N-1)], dtype=int)
  idx_j = np.zeros([N*(N-1)], dtype=int)
  offsets = np.zeros([N*(N-1),3], dtype=float)
  count = 0
  for i in range(N):
      for j in range(N):
          if i != j:
              idx_i[count] = i
              idx_j[count] = j
              count += 1
  
  feed_dict = {i_Z: atoms.get_atomic_numbers(), i_R: atoms.get_positions(), i_idx_i: idx_i, i_idx_j: idx_j, i_offsets: offsets} 
                     
  定义output的tensor, run一遍需要得到的
  charge_res = sess.graph.get_tensor_by_name('result/charge:0')
  energy_res = sess.graph.get_tensor_by_name('result/energy:0')
  forces_res = sess.graph.get_tensor_by_name('result/forces:0')
  
  energy, forces = sess.run([energy_res, forces_res],  feed_dict=feed_dict)
  # print(charge)
  print(energy)
  print(forces)                
  ```
  
  * 踩坑记录
  
    不要陷入jupyter陷阱中
  
    如果一个tensor之前没有命名, 可以使用`tf.identify`
  
    chage没改过来, 整错了
  
    分为三步:
  
    1. 保存
    2. 读取
    3. 推理
  
  ![image.png](http://ww1.sinaimg.cn/large/002WGNPWly1gu2lky8g35j61o014xwm002.jpg)

# neighbor_list的处理



ase里对neighbor_list的处理: [doc](https://wiki.fysik.dtu.dk/ase/ase/neighborlist.html?highlight=neighbor_list#ase.neighborlist.neighbor_list)

```python
ase.neighborlist.neighbor_list(quantities, a, cutoff, self_interaction=False, max_nbins=1000000.0)

    quantities: str
        Quantities to compute by the neighbor list algorithm. Each character
        in this string defines a quantity. They are returned in a tuple of
        the same order. Possible quantities are

            * 'i' : first atom index
            * 'j' : second atom index
            * 'd' : absolute distance
            * 'D' : distance vector
            * 'S' : shift vector (number of cell boundaries crossed by the bond
              between atom i and j). With the shift vector S, the
              distances D between atoms can be computed from:
              D = positions[j]-positions[i]+S.dot(cell)
            
    Returns:

    	i, j, ... : array
        Tuple with arrays for each quantity specified above. Indices in `i`
        are returned in ascending order 0..len(a)-1, but the order of (i,j)
        pairs is not guaranteed.            
```

physnet使用的是`ijS`, 即:

1. i,j 为原子对的下标

2. S为Shifted_vector, 因为开启了pbc环境, 需要计算得到每个原子是否需要便宜

   新的坐标:

   sr_offsets = np.dot(sr_S, atoms.get_cell())



## Physnet 是如何处理 NeighborList的



`NeuralNetwork`

```cpp

    def atomic_properties(self, Z, R, idx_i, idx_j, offsets=None, sr_idx_i=None, sr_idx_j=None, sr_offsets=None):
        with tf.name_scope("atomic_properties"):
            #calculate distances (for long range interaction)
            Dij_lr = self.calculate_interatomic_distances(R, idx_i, idx_j, offsets=offsets)
            #optionally, it is possible to calculate separate distances for short range interactions (computational efficiency)
            if sr_idx_i is not None and sr_idx_j is not None:
                Dij_sr = self.calculate_interatomic_distances(R, sr_idx_i, sr_idx_j, offsets=sr_offsets)
            else:
                sr_idx_i = idx_i
                sr_idx_j = idx_j
                Dij_sr = Dij_lr

            #calculate radial basis function expansion
            rbf = self.rbf_layer(Dij_sr)

            #initialize feature vectors according to embeddings for nuclear charges
            x = tf.gather(self.embeddings, Z)

            #apply blocks
            Ea = 0 #atomic energy 
            Qa = 0 #atomic charge
            nhloss = 0 #non-hierarchicality loss
            for i in range(self.num_blocks):
                x = self.interaction_block[i](x, rbf, sr_idx_i, sr_idx_j)
                out = self.output_block[i](x)
                Ea += out[:,0]
                Qa += out[:,1]
                #compute non-hierarchicality loss
                out2 = out**2
                if i > 0:
                    nhloss += tf.reduce_mean(out2/(out2 + lastout2 + 1e-7))
                lastout2 = out2

            #apply scaling/shifting
            Ea = tf.gather(self.Escale, Z) * Ea + tf.gather(self.Eshift, Z) + 0*tf.reduce_sum(R, -1) #last term necessary to guarantee no "None" in force evaluation
            Qa = tf.gather(self.Qscale, Z) * Qa + tf.gather(self.Qshift, Z)
        return Ea, Qa, Dij_lr, nhloss
```

# 网络结构



![image.png](http://tva1.sinaimg.cn/large/002WGNPWly1gup842sxsuj61es0rgguf02.jpg)

![image.png](http://tva1.sinaimg.cn/large/002WGNPWly1gursq4k6ixj60zr0h2afz02.jpg)

``tf.gather(self.densej(xa), idxj)``: 按照`idx_j`的顺序, 从``dense_j``取出对应的隐藏层

`tf.segment_sum(g*tf.gather(self.dense_j(xa), idx_j), idx_i)` 按照`idx_i`把对应的分量加在一起

`RBF Layer`: 和距离向量大小(`F = len(idx_i)`)相同的一个权重矩阵

`embedding`	`[95 x F]`默认128

```cpp
self._embeddings = tf.Variable(tf.random_uniform([95, self.F], minval=-np.sqrt(3), maxval=np.sqrt(3), seed=seed, dtype=dtype), name="embeddings", dtype=dtype)
```



```python
def calculate_interatomic_distances(self, R, idx_i, idx_j, offsets=None):
    #calculate interatomic distances
    Ri = tf.gather(R, idx_i) # F
    Rj = tf.gather(R, idx_j)
    if offsets is not None:
        Rj += offsets
    Dij = tf.sqrt(tf.nn.relu(tf.reduce_sum((Ri-Rj)**2, -1))) 
    return Dij # D = len(idx_j)


self._rbf_layer = RBFLayer(K, sr_cut, scope="rbf_layer")
rbf = self.rbf_layer(Dij_sr) #  D * K
g = self.k2f(rbf)	#  D * F

#RBFLayer.py
def cutoff_fn(self, D):
    x = D/self.cutoff
    x3 = x**3
    x4 = x3*x                                   
    x5 = x4*x
    return tf.where(x < 1, 1 - 6*x5 + 15*x4 - 10*x3, tf.zeros_like(x))

def __call__(self, D):
    D = tf.expand_dims(D, -1) #necessary for proper broadcasting behaviour
    rbf = self.cutoff_fn(D)*tf.exp(-self.widths*(tf.exp(-D)-self.centers)**2)
    return rbf
```

$x_i$ $x_j$,两部分的计算

```python
xi = self.dense_i(xa)
xj = tf.segment_sum(g*tf.gather(self.dense_j(xa), idx_j), idx_i)
```

注意`x_j`:

```python
//g-[F,F]
tmp = g * tf.gather(self.dense_j(xa), idx_j)
tf.segment_sum(tmp, idx_i)
```



# 找下DeepMD是怎么调用这几个函数的



# 问题



> As mentioned in the developer guide, during runtime at each time step the `PairNNP::compute` method is called, and lists of atoms and their neighbors are provided. If the simulation is run in parallel, each MPI process is handling only a group of “local” atoms for which the contributions to potential energy and atomic forces are calculated. They are accompanied by surrounding“ghost”atoms which are required for the interaction computation but are local to other processes.
>
> 正如开发者指南中提到的，在运行时，在每个时间步调用 `PairNNP::compute` 方法，并提供原子及其邻居的列表。如果模拟并行运行，则每个 MPI  过程仅处理一组“local” atoms，计算其对势能和原子力的贡献。它们伴随着周围的“ghost” atoms，这些原子是交互计算所必需的，但对于其他进程来说是local的。

ghost再lammps里是如何处理的.



看起来模拟的过程中, 如果并行运行, 每个进程的输入仅仅是一部分`neigher list`, 每个进程只算一部分贡献值. 

physnet是否支持, 输入一部分neighbor_list, 把各部分计算结果加回去会不会等于原来的结果.

正常网络应该也不会说支持, 先输入一部分的list, 加起来等于总的吧



果然, deepmd里默认的lmp也并没有使用它

![image.png](http://ww1.sinaimg.cn/large/002WGNPWly1gu5tngn1dpj60ox0c47cp02.jpg)



新的问题, 有没有可能就是physnet算起来慢呢


