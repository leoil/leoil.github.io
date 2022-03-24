# 基础知识

## 常见对象类型

> VO 是最终返给前端的 ，  DTO是在请求交互过程中传递的    这样就清晰了   POJO   就是表映射成的最干净的对象   

* PO(Persistant Object) 持久对象


     用于表示数据库中的一条记录映射成的 java 对象。PO 仅仅用于表示数据，没有任何数据操作。通常遵守 Java Bean 的规范，拥有 getter/setter 方法。

   * DTO(Data Transfer Object) 数据传输对象
     前端调用时传输；也可理解成“上层”调用时传输;
     比如我们一张表有100个字段，那么对应的PO就有100个属性。但是我们界面上只要显示10个字段，客户端用WEB service来获取数据，没有必要把整个PO对象传递到客户端，这时我们就可以用只有这10个属性的DTO来传递结果到客户端，这样也不会暴露服务端表结构.到达客户端以后，如果用这个对象来对应界面显示，那此时它的身份就转为VO.

     用于表示一个数据传输对象。DTO 通常用于不同服务或服务不同分层之间的数据传输。DTO 与 VO 概念相似，并且通常情况下字段也基本一致。但 DTO 与 VO 又有一些不同，这个不同主要是设计理念上的，比如 API 服务需要使用的 DTO 就可能与 VO 存在差异。通常遵守 Java Bean 的规范，拥有 getter/setter 方法

   * BO

   * VO(Value Object) 表现对象
     前端界面展示；value object值对象；ViewObject表现层对象；主要对应界面显示的数据对象。对于一个WEB页面，或者SWT、SWING的一个界面，用一个VO对象对应整个界面的值；对于Android而言即是activity或view中的数据元素。

     用于表示一个与前端进行交互的 java 对象。有的朋友也许有疑问，这里可不可以使用 PO 传递数据？实际上，这里的 VO 只包含前端需要展示的数据即可，对于前端不需要的数据，比如数据创建和修改的时间等字段，出于减少传输数据量大小和保护数据库结构不外泄的目的，不应该在 VO 中体现出来。通常遵守 Java Bean 的规范，拥有 getter/setter 方法。

     >
     >
     >PO：persistent object 持久对象
     >
     >- 有时也被称为Data对象，对应数据库中的entity，可以简单认为一个PO对应数据库中的一条记录。
     >- 在Mybatis持久化框架中与insert/delet操作密切相关。
     >- PO中不应该包含任何对数据库的操作。
     >
     >POJO ：plain ordinary java object 无规则简单java对象
     >
     >VO：value object 值对象 / view object 表现层对象
     >
     >- 主要对应页面显示（web页面/swt、swing界面）的数据对象。
     >- 可以和表对应，也可以不，这根据业务的需要。
     >- 可以细分包括 req、res
     >
     >DO（Domain Object）：领域对象，就是从现实世界中抽象出来的有形或无形的业务实体。通常可以代替部分 PO 的职责。
     >
     >DTO（TO）：Data Transfer Object 数据传输对象
     >
     >- 用在需要跨进程或远程传输时，它不应该包含业务逻辑。
     >- 比如一张表有100个字段，那么对应的PO就有100个属性（大多数情况下，DTO内的数据来自多个表）。但view层只需显示10个字段，没有必要把整个PO对象传递到client，这时我们就可以用只有这10个属性的DTO来传输数据到client，这样也不会暴露server端表结构。到达客户端以后，如果用这个对象来对应界面显示，那此时它的身份就转为VO。

     

* @service注解

  


## `Annotation`注解

- 可以附加在package , class , method , field 等上面,相当于给他们添了额外的辅助信息,
  我们可以通过反射机制编程实现对这些元数据的访问

- 自定义注解: 

  使用@interface自定义注解时,自动继承了java.lang.annotation.Annotation接口

  1. @ interface用来声明一个注解,格式: public @ interface注解名{定义内容}
  2. 其中的每一一个方法实际上是声明了一个配置参数.
  3. 方法的名称就是参数的名称.
  4. 返回值类型就是参数的类型(返回值只能是基本类型,Class , String , enum ).
  5. 可以通过default来声明参数的默认值
  6. 如果只有一个参数成员, - -般参数名为value
  7. 注解元素必须要有值,我们定义注解元素时,经常使用空字符串，0作为默认值.

  

## ThreadLoacl

`ThreadLocal` 中填充的的是当前线程的变量，该变量对其他线程而言是封闭且隔离的，`ThreadLocal` 为变量在每个线程中创建了一个副本，这样每个线程都可以访问自己内部的副本变量。

* 使用场景

  1. 在进行对象跨层传递的时候，使用ThreadLocal可以避免多次传递，打破层次间的约束。

  2. 线程间数据隔离

  3. 进行事务操作，用于存储线程事务信息。

  4. 数据库连接，`Session`会话管理。

## MyBatis

* 与JDBC的区别

MyBatis: ORM框架, 对象关系映射

* DAO

[Java DAO 模式 - 乌漆WhiteMoon - 博客园 (cnblogs.com)](https://www.cnblogs.com/linfangnan/p/13871860.html)

对象和数据库的中间层, 提供了通用的规则, 可以沟通不同的数据库

* 

## Spring

* Spring ioc 源码: [Spring IOC 容器源码分析](https://javadoop.com/post/spring-ioc)
* @Bean生命周期: 

* 注解:[Spring——基于注解的配置](https://blog.csdn.net/gavin_john/article/details/81051343)

`@Resource 	@Autowired`: https://www.cnblogs.com/felordcn/p/13063802.html

[Spring依赖注入`@Autowired`深层原理、源码级分析，感受DI带来的编程之美](https://cloud.tencent.com/developer/article/1497703)

* `@PostConstruct`

被注解的方法，在对象加载完依赖注入后执行。

* `@Service` `@Resource`
* `Bean` 
* `@Configuration`
* `Mapper` 
* `Controller`
* `@Transactional(rollbackFor = Exception.class)`
* `@EnableCaching`

> **同一个类中方法调用，导致@Transactional失效**
>
> 开发中避免不了会对同一个类里面的方法调用，比如有一个类Test，它的一个方法A，A再调用本类的方法B（不论方法B是用public还是private修饰），但方法A没有声明注解事务，而B方法有。则外部调用方法A之后，方法B的事务是不会起作用的。这也是经常犯错误的一个地方。
> 那为啥会出现这种情况？其实这还是由于使用Spring AOP代理造成的，因为只有当事务方法被当前类以外的代码调用时，才会由Spring生成的代理对象来管理。
>
> **开启多线程任务时，事务管理会受到影响**
>
> 因为线程不属于spring托管，故线程不能够默认使用spring的事务,也不能获取spring注入的bean在被spring声明式事务管理的方法内开启多线程，多线程内的方法不被事务控制。
> 如下代码，线程内调用insert方法，spring不会把insert方法加入事务就算在insert方法上加入@Transactional注解，也不起作用。
>
> 另外，使用多线程事务的情况下，进行回滚，比较麻烦。thread的run方法，有个特别之处，它不会抛出异常，但异常会导致线程终止运行。
>
> 

* `@Before`

## Maven

下载不好使了: 

在项目根目录下; 

```
mvn dependency:resolve -Dclassifier=sources
```



# 设计模式

## 模板模式



## 简单工厂模式



# 一些问题

## 表的设计

1. stregy_detail表里, 保存了`prize_count`和`prize_surplu_count`

   实际场景中, 抽奖系统和发奖系统是隔离的, 抽奖计算结果, 发奖执行动作. 因此两方的库也是存在隔离的, 不太可能从抽奖到发奖做一个大的事务, 那么如果抽奖中没有库存, 只是计算结果, 就会导致发奖中库存存在超发的情况.

   类似电商中商品的售卖和对应sku的库存, 售卖系统是需要有 闲置库存字段的, sku里的库存是总库存, 二者隔离.

 2. strateg_detail里面存在冗余信息, 为了展示方便, 可能不完全遵循三段式

# 03-跑通广播模式RPC过程调用



## POM文件的配置

* 总工程下的POM

  

* 模块类POM

MySQL 8.0 版本有更新, 

> 本地是MySQL8版本，启动项目前记得将lottery-interfaces模块下的pom文件mysql-connector-java 部分版本号升级为8.0.22 。并在该模块下的yml配置文件中将driver-class-name 调整为com.mysql.cj.jdbc.Driver ，且因为MySQL8连接要求，必须在连接url后跟上serverTimezone 的配置，即url: jdbc:mysql://127.0.0.1:3306/lottery?useUnicode=true&serverTimezone=Asia/Shanghai

Java版本用错了(17 -> 8), 动态代理出错, 提示

Unable to make [protected](https://so.csdn.net/so/search?q=protected&spm=1001.2101.3001.7020) final java.lang.Class java.lang.ClassLoader.defineClass(java.lang.String,byte[],int,int,java.security.ProtectionDomain) throws java.lang.ClassFormatError accessible: module java.base does not "opens java.lang" to unnamed module @5c29bfd

* `@Service`

  `@Service` 注解是**com.alibaba.dubbo.config.annotation.Service ** 的，不是spring提供的

* 

### 思考:  @Bean的注入

代码里是直接实例化了一个接口对象, 通过注入可以得到我们需要的实例化对象

```java
@Reference(interfaceClass = IActivityBooth.class, url = "dubbo://127.0.0.1:20880")
private IActivityBooth activityBooth;

//2
@Resource
private IActivityDao activityDao;

```



# 04-建表建库逻辑

​	分页分表/分页分库



# 05-抽奖领域模块的开发

## 架构分层

* 从MVC 到 DDD

  ![](https://i0.hdslb.com/bfs/album/0f73d48947b78a1a9f4b4fc8fd1d29ea3589d177.png)

  * MVC: 

    单个对象服务多个Service

    Service重构, 困难, 一改多个domain里的类出问题

  * DDD

    包隔离

    * Domain

      * Model

        

      * Repository --> DAO

      * Service: 服务独立, 只服务于本领域下的功能实现

## 抽奖策略

循环匹配 O(N) 遍历 0-1.0 区间

斐波哈希那契 : 
128 有100个位置存有东西 O(1) 查找

![](https://i0.hdslb.com/bfs/album/8473957878197583292cb717bebee28feafc4883.png)

对比 HashMap --> 哈希碰撞, 拉链寻址, 超过8个, 红黑树

斐波那契数列: 

## 代码实现

* Domain
  * Model:

     里面全是具体对应到数据表里的类

    `Repository`里面查询的结果, 就被转换为这几个类.

  * Repository: 

    定义了`IStrategyRepository`接口, 提供的方法: 对查询结果进行整合, 再封装成新类.

    具体实现:

     	1. 使用之前定义好的`DAO`, 通过`@Resource`自动注入 Bean
     	2. 调用各种 查询数据库方法, 返回封装得到的结果

  * Service: 具体代码实现

    抽奖: 定义接口

    * 初始化数据: 静态的, 单一奖品, 实现确定好数组, 在抽奖过程中不会变化

      斐波那契, hash填充概率

    * 整体概率计算, 循环比对抽奖

    1. 抽奖策略

       

    2. 抽奖算法的使用, 抽奖实现

       

    细节: 

    * 使用`ConcurrentHashMap`

* Infrastructure

  * DAO: 对应数据库里的各个表的接口, 提供了获取表中内容的方法, `Mybaits`注入`Mapper`

  * PO

    表中的数据实体类, DAO接口中的方法得到的数据就被封装成这个类型

* Interfaces

  配置好Mybaits里, 对应数据库各个表的Mapper, 可以注入到`Infrastructure`里各个`DAO`里面

## 踩坑

* jdk版本没选对

* 提示 之前插入了多条相同ID的记录, 自然报错

> ```org.apache.ibatis.exceptionss.TooManyResultsException: Expected one result (or null) to be returned by selectOne(), but found: 2`

```java
@Test
public void test_drawExec() {
    drawExec.doDrawExec(new DrawReq("小傅哥", 10001L));
    drawExec.doDrawExec(new DrawReq("小佳佳", 10001L));
    drawExec.doDrawExec(new DrawReq("小蜗牛", 10001L));
    drawExec.doDrawExec(new DrawReq("八杯水", 10001L));
}
```

* `nullException`

  查询记录为空



# 06 模板模式处理抽奖流程

基于模板设计模式，规范化抽奖执行流程。包括：提取抽象类、编排模板流程、定义抽象方法、执行抽奖策略、扣减中奖库存、包装返回结果等，并基于P3C标准完善本次开发涉及到的代码规范化处理。

![](https://i0.hdslb.com/bfs/album/93631fed10b39f5784db4711c18018e16cd62da0.png@1e_1c.webp)

![](https://i0.hdslb.com/bfs/album/ae02fd90b23d0389bec11f2b2e6977b9ce3a948d.png)

## 重点

 使用`模板方法设计模式`优化类 `DrawExecImpl` 抽奖过程方法实现

设计`AbstractDrawBase`抽象类

如果把全部的流程 在实现 `DrawExecImpl`里写出来, 不好读, 也不利于之后的开发, 可以抽象构成 抽象类

## 模板模式

职责分离 + 标准定义

![](https://i0.hdslb.com/bfs/album/638c96da386f267ae8fdce5587c5dc0781e13bf2.png)

方法, 不把所有方法实现放在抽象类中: 

1. 自身实现(支撑类和配置类)

2. 让业务实现

分离, 就近, 瘦身

## 具体操作

标准化抽奖流程: 

1. 根据入参策略ID获取抽奖策略配置(StrategyConfig)
2. 校验 + 把抽奖策略的数据初始化到内存
3. 获取那些被排除掉的抽奖列表，这些奖品可能是已经奖品库存为空，或者因为风控策略不能给这个用户薅羊毛的奖品
4. 执行抽奖算法
5. 包装中奖结果

## 代碼改變

* `strategy`表中新加入库存字段, 相应的, `infra`里的DAO, `mybatis`需要添加新的的查询空库存, 更新库存的语句, 同时`domain`里的`repository`结果也有提供对应的方法.

* 构建抽象类, 部分查询的功能可以在抽象类里实现, 同时还可以把这一步分方法再提取成一个父类

  这样只用在抽象类里把抽奖的流程实现, 具体的抽奖方法可以在子类里实现

## 踩坑

* `NoUniqueBeanDefinitionException`

  > ``No qualifying bean of type 'cn.leoil.lottery.domain.strategy.service.algorithm.IDrawAlgorithm' available: expected single matching bean but found 2: entiretyProbRandomDrawAlgorithm,singleProbRandomDrawAlgorithm``

  如果不加名字限定, 有给`IDrawAlgorithm`对象赋值的时候, 发现了两个满足类型的对象, 不能判断要注入哪一个, 加上名称限定后解决

  ```java
  //@Resource
  @Resource(name  = "entiretyProbRandomDrawAlgorithm")
  private IDrawAlgorithm entiretyRateRandomDrawAlgorithm;
  
  @Resource(name = "singleProbRandomDrawAlgorithm")
  private IDrawAlgorithm singleProbRandomDrawAlgorithm;
  ```

* 修改数据库字段类型

  ```SQL
  alter table strategy_detail modify prizeId  varchar(64) null comment '奖品ID';
  ```

  保证可以得到正确的查询结果

# 简单工厂搭建发奖领域

## 重点

**运用简单工厂设计模式，搭建发奖领域服务。**
介绍：定义一个创建对象的接口，让其子类自己决定实例化哪一个工厂类，工厂模式使其创建过程延迟到子类进行。

## 简单工厂

不写`if-else`, (map) 还能实现 不同类型,使用不同的类

![](https://i0.hdslb.com/bfs/album/21b65415db58fdc30e4eb78a4197c16d3ab17eea.png)

工厂提供服务, 允许子类决定实例化对象的类型, 可以实例化好/原型模式, 外部调用时生成

奖品服务交给工厂处理, 工厂对外提供发奖服务, 由工厂统一包装

## 具体操作

`goods`商品处理 + `factory`工厂

* `goods`

  包装适配各类奖品的发放逻辑，虽然我们目前的抽奖系统仅是给用户返回一个中奖描述，但在实际的业务场景中，是真实的调用优惠券、兑换码、物流发货等操作，而这些内容经过封装后就可以在自己的商品类下实现了。

* `factory`

  工厂模式通过调用方提供发奖类型，返回对应的发奖服务。通过这样由具体的子类决定返回结果，并做相应的业务处理。从而不至于让领域层包装太多的频繁变化的业务属性，因为如果你的核心功能域是在做业务逻辑封装，就会就会变得非常庞大且混乱。

* 新增`model`

  `GoodsRep`: 	配置各种奖品类, 

  主要是初始化了这么一个map, 让工厂可以实现map

  ``protected static Map<Integer, IDistributionGoods> goodsMap = new ConcurrentHashMap<>();``

  

  `DistributionRes`: 发奖信息

  

关键就在下面了, 工厂类提供的`getDistributionGoodsService`方法, 其实就是根据抽奖结果`drawPrizeInfo`得到奖品类型 `prizeType`, 再从`map`里取出对应的`奖品类`, 调用类的分发方法, 实现发奖操作.

```java
IDistributionGoods distributionGoods = distributionGoodsFactory.getDistributionGoodsService(drawPrizeInfo.getPrizeType());
DistributionRes distributionRes = distributionGoods.doDistribution(goodsReq);

```



## 踩坑

* `resultType`和`resultMap`

  把`SELECT`的结果封装成了`resultMap`, 这时候`XML`配置要设置的参数时`resultMap`, 不再是`Type`, 不然就报错

  > ```Could not resolve type alias 'strategyMap'.  Cause: java.lang.ClassNotFoundException: Cannot find class: strategyMap`

* 报错 , 创建`bean`失败

  > ```
  > No qualifying bean of type 'cn.leoil.lottery.domain.prize.service.factory.DistributionGoodsFactory' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {@javax.annotation.Resource(shareable=true, lookup=, name=, description=, authenticationType=CONTAINER, type=class java.lang.Object, mappedName=)}
  > ```
  >
  > 

  1. 忘记给工厂添加`@Service`
  2. 忘记给商品类添加`@Component`

# 活动领域的配置和状态

ddd模型调整包结构

* `领域层 domain` 定义仓储接口，`基础层 infrastructure` 实现仓储接口。

* 活动创建的操作主要会用到事务，因为活动系统提供给运营后台创建活动时，需要包括：活动信息、奖品信息、策略信息、策略明细以及其他额外扩展的内容，这些信息都需要在一个事务下进行落库

* 活动状态的审核，【1编辑、2提审、3撤审、4通过、5运行(审核通过后worker扫描状态)、6拒绝、7关闭、8开启】，这里我们会用到设计模式中的`状态模式`进行处理

## 状态模式

还是简化`if-else`流程

状态流转

![](https://i0.hdslb.com/bfs/album/ef4ffc53d5ef39778dff4795bd8548adc9b05fd3.png)

创建/map方法使用

![](https://i0.hdslb.com/bfs/album/0544e2f3aa5b2e0f7f7d920590af5d5af1d5292b.png)



## 具体实现

中间事务失败, 进行回滚

* 状态流转: 定义状态抽象类, 
* 每个状态都有处理过程: 传当前状态+ 结果态 ->并发时保证, 前置状态没有变化(行级事务锁)
* 状态使用; 配置类,

每一步只关心自己的流程

## 代码



domain

1. domain不再依赖infrastructure, 而是反过来

   之前domain里对infrastructure下po类的使用, 也需要相应修改, 改成用自己package提供的vo

   原来domain下面用了infra的po, 也要换成自己包下的VO

2. domain下创建activity

* 建立对应的model(VO req 还多了一个aggregates) / repo

  `aggregate`整合了三个VO

  ```java
  /** 活动配置 */
  private ActivityVO activity;
  
  /** 策略配置(含策略明细) */
  private StrategyVO strategy;
  
  /** 奖品配置 */
  private List<PrizeVO> prizeList;
  
  ```

3. activity service

   定义抽象类 `AbstState`, 定义 状态之间可以转化的方法

   实现抽象类, 按照之前画好的图, 定义好不同状态之前是否可以转换

   * `StateConfig`

     通过map, 可以根据状态信息, 获取到注入的bean

     ```java
     @Resource
     private RefuseState refuseState;
     ```

     ```java
     @PostConstruct
     public void init() {
         stateGroup.put(Constants.ActivityState.ARRAIGNMENT, arraignmentState);
         stateGroup.put(Constants.ActivityState.CLOSE, closeState);
         stateGroup.put(Constants.ActivityState.DOING, doingState);
         stateGroup.put(Constants.ActivityState.EDIT, editingState);
         stateGroup.put(Constants.ActivityState.OPEN, openState);
         stateGroup.put(Constants.ActivityState.PASS, passState);
         stateGroup.put(Constants.ActivityState.REFUSE, refuseState);
     }
     ```

     

3. Deploy方法实现

   ```java
   @Transactional(rollbackFor = Exception.class)
   @Override
   public void createActivity(ActivityConfigReq req) {
   
   }
   ```

4. 

infrastructure

1. 继续修改DAO接口, 支持更多数据库操作方法

   Activity / Prize /Strategy /StrategyDetail都需要支持insert方法

   把对象信息复制到VO

   ```java
   Prize prize = prizeDao.queryPrizeInfo(prizeId);
   
   // 可以使用 BeanUtils.copyProperties(prize, prizeBriefVO)、或者基于ASM实现的Bean-Mapping，但在效率上最好的依旧是硬编码
   PrizeBriefVO prizeBriefVO = new PrizeBriefVO();
   prizeBriefVO.setPrizeId(prize.getPrizeId());
   prizeBriefVO.setPrizeType(prize.getPrizeType());
   prizeBriefVO.setPrizeName(prize.getPrizeName());
   prizeBriefVO.setPrizeContent(prize.getPrizeContent());
   ```

   

2. domain里的repository实现移动到此处/ 接口定义还在domain里

   repository作用: 

   ​	调用DAO接口, 返回查询数据

   ​	注册成`@Component`

   ​	负责 活动表、奖品表、策略表、策略明细表 的 增/

   ```java
   @Override
   public void addStrategy(StrategyVO strategy) {
       Strategy req = new Strategy();
       BeanUtils.copyProperties(strategy, req);
       strategyDao.insert(req);
   }
   ```

   实现和之前相比, 多了一步从VO拷贝req对象的操作

   

interface

1. 修改`mybatis` XML

   ```SQL
   UPDATE lottery.activity
   SET state = #{afterState}
   WHERE activity_id = #{activityId} AND state = #{beforeState}
   ```

   `alterstate`增加了`beforeState`的限制, 相当于行级锁

   `foreach`

   ```SQL
   <insert id="insertList" parameterType="java.util.List">
       INSERT INTO  lottery.prize(prize_id, prize_type, prize_name, prize_content, create_time, update_time)
       VALUES
       <foreach collection="list" item="item" index="index" separator=",">
           (
           #{item.prizeId},
           #{item.prizeType},
           #{item.prizeName},
           #{item.prizeContent},
           NOW(),
           NOW()
           )
           )
       </foreach>
   </insert>
   ```

   

   

## 思考

1. domain下model里的VO 和 infrastructure里的PO 有什么区别和联系



2. 为什么要这么干

   `// 可以使用 BeanUtils.copyProperties(prize, prizeBriefVO)、或者基于ASM实现的Bean-Mapping，但在效率上最好的依旧是硬编码`

3. `@Transactional(rollbackFor = Exception.class)`

## 踩坑

*  Annotation processing is not supported for module cycles. 

  光在POM里删了还不行

  看下这个

  [Error:java: Annotation processing is not supported for module cycles. Please ensure that all modules from cycle are excluded from annotation processing - 放飞一切啊 - 博客园 (cnblogs.com)](https://www.cnblogs.com/panpanshen/p/9668058.html)

* VO类一定要实现`to_string`

* 老毛病 接口实现没加 `@Service`

* 没给`state`类加 `@Component`

## 到底干了个啥

1. 把Domain下面关于repo的实现迁移到了infra下, 接口不动, 这样就需要修改依赖关系了

   所以需要把domain里, 用到的infra的po, 全部换成domain下的VO

   注意在使用VO是, 需要把对象的信息传递给VO对象, (copyProperties/直接设置)

   涉及到domain下之前实现的所有package

2. 实现activity domain

   主要是两点

   * 支持包含prize/strtegy/detail信息的新req插入

     注意使用`@Transactional`

   * 支持不同状态流转

     实现了`IStateHandler`接口, 同时继承`StateConfig`类, 可以获取状态Map

     可以根据状态码, 获取到状态类, 每个状态类都定义了是否能转到别的状态



# 活动号ID生成策略领域开发



idGenerator 直接注册`@Bean`

<img src="https://i0.hdslb.com/bfs/album/43532a0e07d8303a1ba6f95dc4e3f968d9cd6c7f.png" style="zoom:150%;" />

# 10 使用路由分库分表

![](https://i0.hdslb.com/bfs/album/350071c921fac90875dbd539f09e600de7244b7c.png)

## 重点



1. 学习SpringBoot组件开发，了解一个简单的数据库路由实现原理
2. 使用 Mybatis 拦截器处理分表 SQL
3. 学习使用数据库路由

## 具体实现: 

1. 定义路由注解

   自定义注解

   ```java
   @Documented
   @Retention(RetentionPolicy.RUNTIME)
   @Target({ElementType.TYPE, ElementType.METHOD})
   public @interface DBRouter {
   
       String key() default "";
   
   }
   ```

   在需要进行AOP拦截的方法上加上注解,(方法配置注解)

   拦截后进行相应的数据库路由计算和判断，并切换到相应的操作数据源上。

   ```java
   @Mapper
   public interface IUserDao {
   
        @DBRouter(key = "userId")
        User queryUserInfoByUserId(User req);
   
        @DBRouter(key = "userId")
        void insertUser(User req);
   }
   ```

   

2. 解析路由配置

   根据xml里的信息, 设置好的不同id与数据库的对应map(分库使用)

   

3. 数据源切换(确定使用哪个库, 分库)

   数据源:  `DataSource` 的实例化对象

   

   我们自己创建了一个`DynamicDataSource` 类中，它是一个继承了 `AbstractRoutingDataSource`, 

   这个类有

   * `private Map<Object, Object> targetDataSources;` 记录了可以切换的不同数据库

   * `determineTargetDataSource`方法

     根据`lookupKey`, 从`targetDataSources`取出对应的数据库进行连接.

   所以我们需要做的是两步:

   1. 从xml中读去我们设置好的几个db信息, 建立map
   2. 根据这个map, 配置`targetDataSources`
   3. 重载`determineCurrentLookupKey`, 让我们可以选择连接哪一个数据库

   

   相关代码

   ```java
   //AbstractRoutingDataSource 抽象类方法与属性
   
   @Nullable
   private Map<Object, Object> targetDataSources;
   
   protected DataSource determineTargetDataSource() {
       Assert.notNull(this.resolvedDataSources, "DataSource router not initialized");
       Object lookupKey = determineCurrentLookupKey();
       DataSource dataSource = this.resolvedDataSources.get(lookupKey);
       if (dataSource == null && (this.lenientFallback || lookupKey == null)) {
           dataSource = this.resolvedDefaultDataSource;
       }
       if (dataSource == null) {
           throw new IllegalStateException("Cannot determine target DataSource for lookup key [" + lookupKey + "]");
       }
       return dataSource;
   }
   ```

   同时重载了`AbstractRoutingDataSource`的`determineCurrentLookupKey`方法, 达到选择库的目的.

   ```java
   //AbstractRoutingDataSource
   protected abstract Object determineCurrentLookupKey();
   
   // DynamicDataSource  extends AbstractRoutingDataSource
   @Override
   protected Object determineCurrentLookupKey() {
       return "db" + DBContextHolder.getDBKey();
   }
   ```

4. 切面拦截

   拦截使用注解的方法,

   重要的就这几句: 

   从` @DBRouter(key = "userId")`传入`key`实参, 在`ProceedingJoinPoint`实例找到对应字段传入的值是多少, 再根据这个值计算`DBKey`和`TBKey`

   注意, 这两个值都是存入`ThreadLocal`里的

   ```java
   @Aspect
   public class DBRouterJoinPoint {
       
       @Pointcut("@annotation(cn.bugstack.middleware.db.router.annotation.DBRouter)")
       public void aopPoint() {
       }
   
       
       @Around("aopPoint() && @annotation(dbRouter)")
       public Object doRouter(ProceedingJoinPoint jp, DBRouter dbRouter) throws Throwable {
           String dbKey = dbRouter.key();
           // 计算路由
           String dbKeyAttr = getAttrValue(dbKey, jp.getArgs());
       }
   }    
   
   ```

   

5. MyBatis 拦截器处理分表

   先AOP拦截, 计算`dbkey`和`tbKey`, 

   再在这里修改sql语句, 

   最后连接数据库(调用`DynamicDataSource.determineCurrentLookupKey()`方法, 选择连接的数据库)

   拦截过程: 

   ​	获取StatementHandler、

   ​	通过自定义注解判断是否进行分表操作、

   ​	获取SQL并替换SQL表名 USER 为 USER_03、

   ​	最后通过反射修改SQL语句

   ```java
   @Intercepts({@Signature(type = StatementHandler.class, method = "prepare", args = {Connection.class, Integer.class})})
   public class DynamicMybatisPlugin implements Interceptor {
   
   
       private Pattern pattern = Pattern.compile("(from|into|update)[\\s]{1,}(\\w{1,})", Pattern.CASE_INSENSITIVE);
   
       @Override
       public Object intercept(Invocation invocation) throws Throwable {
           // 获取StatementHandler
           StatementHandler statementHandler = (StatementHandler) invocation.getTarget();
           MetaObject metaObject = MetaObject.forObject(statementHandler, SystemMetaObject.DEFAULT_OBJECT_FACTORY, SystemMetaObject.DEFAULT_OBJECT_WRAPPER_FACTORY, new DefaultReflectorFactory());
           MappedStatement mappedStatement = (MappedStatement) metaObject.getValue("delegate.mappedStatement");
   
           // 获取自定义注解判断是否进行分表操作
           String id = mappedStatement.getId();
           String className = id.substring(0, id.lastIndexOf("."));
           Class<?> clazz = Class.forName(className);
           DBRouterStrategy dbRouterStrategy = clazz.getAnnotation(DBRouterStrategy.class);
           if (null == dbRouterStrategy || !dbRouterStrategy.splitTable()){
               return invocation.proceed();
           }
   
           // 获取SQL
           BoundSql boundSql = statementHandler.getBoundSql();
           String sql = boundSql.getSql();
   
           // 替换SQL表名 USER 为 USER_03
           Matcher matcher = pattern.matcher(sql);
           String tableName = null;
           if (matcher.find()) {
               tableName = matcher.group().trim();
           }
           assert null != tableName;
           String replaceSql = matcher.replaceAll(tableName + "_" + DBContextHolder.getTBKey());
   
           // 通过反射修改SQL语句
           Field field = boundSql.getClass().getDeclaredField("sql");
           field.setAccessible(true);
           field.set(boundSql, replaceSql);
           field.setAccessible(false);
   
           return invocation.proceed();
       }
   
   }
   ```

   

## 思考

```java
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
```

* SQL语句从修改到执行的过程:  

     	1. 解析数据源(`DataSourceAutoConfig`)
     	2. AOP拦截, 计算两个key
     	3. Mybatis拦截, 修改SQL语句(分表) 
     	4. 再getConnections(决定连接哪个数据库/分库): 

    连进数据库 执行语句



* 注解的作用

  注解类里方法就是可以配置的属性

  

  `DBRouter` : 加在接口类的方法上

  Router组件 `DBRouterJoinPoint`类里, 使用aop处理

  ```java
  @Pointcut("@annotation(cn.bugstack.middleware.db.router.annotation.DBRouter)")
  public void aopPoint() {
  }
  
  @Around("aopPoint() && @annotation(dbRouter)")
  public Object doRouter(ProceedingJoinPoint jp, DBRouter dbRouter) throws Throwable {
      String dbKey = dbRouter.key();
      if (StringUtils.isBlank(dbKey) && StringUtils.isBlank(dbRouterConfig.getRouterKey())) {
          throw new RuntimeException("annotation DBRouter key is null！");
      }
      dbKey = StringUtils.isNotBlank(dbKey) ? dbKey : dbRouterConfig.getRouterKey();
      // 路由属性
      String dbKeyAttr = getAttrValue(dbKey, jp.getArgs());
      // 路由策略
      dbRouterStrategy.doRouter(dbKeyAttr);
      // 返回结果
      try {
          return jp.proceed();
      } finally {
          dbRouterStrategy.clear();
      }
  }
  ```

  

  `DBRouterStrategy`: 加在Dao接口类上

  

* **AOP是怎么获取代理类信息的**

  

  

  ```java
  @Aspect
  public class DBRouterJoinPoint {
      
      //调用DBRouter类时会调用此方法
      @Pointcut("@annotation(cn.bugstack.middleware.db.router.annotation.DBRouter)")
      public void aopPoint() {
      }
  
      //调用aopPoint()/dbRouter的时候, 使用此方法
      @Around("aopPoint() && @annotation(dbRouter)")
      public Object doRouter(ProceedingJoinPoint jp, DBRouter dbRouter) throws Throwable {
          String dbKey = dbRouter.key();
          // 计算路由
          String dbKeyAttr = getAttrValue(dbKey, jp.getArgs());
          
          //计算/保存结果
          
          //返回,继续运行
          try {
              return jp.proceed();
          } finally {
             dbRouterStrategy.clear();
          }        
          
      }
  }    
  ```

  打个断点看看:

  `@annotation(dbRouter)`, 注册在`dbRouter`实例上的方法, 在

  切面执行顺序

  ![](https://i0.hdslb.com/bfs/album/fbac3cd35dcaea421d62d6ccc025915fa97090de.png)

  

# 11 声明事务领取活动领域开发

* 领取 超卖不超发(可以多扣, 但不能领取了不扣)
* 出现异常, 在分库分表下 如何回滚

![](https://i0.hdslb.com/bfs/album/3e14c8b07214b8473e8f99392c31495d17cf3485.png)

## 重点

* 基于**模板模式**开发领取活动领域，因为在领取活动中需要进行活动的日期、库存、状态等校验，并处理扣减库存、添加用户领取信息、封装结果等一系列流程操作，因此使用抽象类定义模板模式更为妥当

## 问题和解决

问题：如果一个场景需要在同一个事务下，连续操作不同的DAO操作，那么就会涉及到在 DAO 上使用注解 @DBRouter(key = "uId") 反复切换路由的操作。虽然都是一个数据源，但这样切换后，事务就没法处理了。

原因: 

每次DAO的方法(有路由标记的情况)执行, 对每一个`uId`都会需要`getLookUpKey`,，有切换数据库的可能，所以都会重新选择数据源导致事务失效的吧

解决：这里选择了一个较低的成本的解决方案，就是把数据源的切换放在事务处理前，而事务操作也通过编程式编码进行处理。*具体可以参考 db-router-spring-boot-starter 源码*

## 代码

* 修改了部分之前的代码, 在抽奖算法部分, 考虑并行, 加上`synchronized `, 同时支持自己选择抽奖策略

  `public synchronized void initRateTuple(Long strategyId, Integer strategyMode, List<AwardRateInfo> awardRateInfoList) `

  ​	

  

* 处理路由切换导致事务失效

  `import org.springframework.transaction.support.TransactionTemplate;`
  
  ```java
  @Override
  protected Result grabActivity(PartakeReq partake, ActivityBillVO bill) {
      try{
          dbRouter.doRouter(partake.getuId());
          return transactionTemplate.execute(status -> {
              try{
                  // 扣减个人已参与次数
                  int updateCount = userTakeActivityRepository.subtractionLeftCount(bill.getActivityId(), bill.getActivityName(), bill.getTakeCount(), bill.getUserTakeLeftCount(), partake.getuId(), partake.getPartakeDate());
                  if(0 == updateCount){
                      status.setRollbackOnly();
                      logger.error("领取活动，扣减个人已参与次数失败 activityId：{} uId：{}", partake.getActivityId(), partake.getuId());
                      return Result.buildResult(Constants.ResponseCode.NO_UPDATE);
                  }
  
                  // 插入领取活动信息
                  Long takeId = idGeneratorMap.get(Constants.Ids.SnowFlake).nextId();
                  userTakeActivityRepository.takeActivity(bill.getActivityId(), bill.getActivityName(), bill.getTakeCount(), bill.getUserTakeLeftCount(), partake.getuId(), partake.getPartakeDate(), takeId);
              }catch (DuplicateKeyException e){
                  status.setRollbackOnly();
                  logger.error("领取活动，唯一索引冲突 activityId：{} uId：{}", partake.getActivityId(), partake.getuId(), e);
                  return Result.buildResult(Constants.ResponseCode.INDEX_DUP);
              }
              return Result.buildSuccessResult();
          });
      }finally {
          dbRouter.clear();
      }
  }
  ```
  
  



这个分支下吧@Bean给取消了, 原因不明

知道原因了, 因为我们要保证事务的完整性, 一旦发生了错误, 需要对事务进行回滚, 

如果还是使用注解式`@Bean`, 如果一个事务执行时, 数据源多次切换, 那么事务失效, 无法回滚.

使用**声明式事务**, 开启事务之前, 就把事务给切换好, 就不会再有事务失效.

具体操作: 

因为取消了`@DBRouter`注解, 我们在使用的时候, 需要手动调用路由切换方法, 在执行事务.

```java
dbRouter.doRouter(partake.getuId());	
    try{
		dbRouter.doRouter(partake.getuId());
        //具体代码在上面
        return transactionTemplate.execute();
    }finally {
        dbRouter.clear();                 
    }
```

事务声明:

```java
return transactionTemplate.execute(status -> {
    try{
        // 扣减个人已参与次数
        int updateCount = userTakeActivityRepository.subtractionLeftCount(bill.getActivityId(), bill.getActivityName(), 
                                                                          bill.getTakeCount(), bill.getUserTakeLeftCount(), 
                                                                          partake.getuId(), partake.getPartakeDate());
        if(0 == updateCount){
            //声明回滚
            status.setRollbackOnly();
            logger.error("领取活动，扣减个人已参与次数失败 activityId：{} uId：{}", partake.getActivityId(), partake.getuId());
            return Result.buildResult(Constants.ResponseCode.NO_UPDATE);
        }

        // 插入领取活动信息
        Long takeId = idGeneratorMap.get(Constants.Ids.SnowFlake).nextId();
        userTakeActivityRepository.takeActivity(bill.getActivityId(), bill.getActivityName(), 
                                                bill.getTakeCount(), bill.getUserTakeLeftCount(), 
                                                partake.getuId(), partake.getPartakeDate(), takeId);
    }catch (DuplicateKeyException e){
        status.setRollbackOnly();
        logger.error("领取活动，唯一索引冲突 activityId：{} uId：{}", partake.getActivityId(), partake.getuId(), e);
        return Result.buildResult(Constants.ResponseCode.INDEX_DUP);
    }
    return Result.buildSuccessResult();
});
```

需要在之前的配置类里, 创建事务对象，用于编程式事务引入

```java
// 定义
@Bean
public TransactionTemplate transactionTemplate(DataSource dataSource) {
    DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
    dataSourceTransactionManager.setDataSource(dataSource);

    TransactionTemplate transactionTemplate = new TransactionTemplate();
    transactionTemplate.setTransactionManager(dataSourceTransactionManager);
    transactionTemplate.setPropagationBehaviorName("PROPAGATION_REQUIRED");
    return transactionTemplate;
}
```



 ## 思考

回到这句话: 

> 如果一个场景需要在同一个事务下，连续操作不同的DAO操作，那么就会涉及到在 DAO 上使用注解 `@DBRouter(key = "uId")` 反复切换路由的操作。虽然都是一个数据源，但这样切换后，事务就没法处理了。

因为有注解和之前的切片, 我们会在每次DAO方法调用的时候, 进入路由, 计算TBkey和DBkey, 切换路由

如果我们在事务开始前, 直接doRouter, 完成连接数据库的设定, 那么之后连接哪一个库就确定了, 使用DAO CURD连接哪个表也清楚了, 就不需要再进一次路由了.保留了事务完整性.



DBRouter包里最重要的是这个类

写了一个自己的`DataSourceAutoConfig`, `SprinBoot`下面有同样功能的类, 主要是用来初始化数据源. 

我们这里还需要引入一个`AOP`切面, 创建事务

```java
@Configuration
public class DataSourceAutoConfig implements EnvironmentAware {
    @Bean(name = "db-router-point")
    @ConditionalOnMissingBean
    public DBRouterJoinPoint point(DBRouterConfig dbRouterConfig, IDBRouterStrategy dbRouterStrategy) {
        return new DBRouterJoinPoint(dbRouterConfig, dbRouterStrategy);
    }
    
    @Bean
    public IDBRouterStrategy dbRouterStrategy(DBRouterConfig dbRouterConfig) {
        return new DBRouterStrategyHashCode(dbRouterConfig);
    }
    
    @Bean
    public TransactionTemplate transactionTemplate(DataSource dataSource) {
        DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
        dataSourceTransactionManager.setDataSource(dataSource);
        TransactionTemplate transactionTemplate = new TransactionTemplate();
        transactionTemplate.setTransactionManager(dataSourceTransactionManager);
        transactionTemplate.setPropagationBehaviorName("PROPAGATION_REQUIRED");
        return transactionTemplate;
    }
        
    
}    
```



## 踩坑

* 数量`NULL`的处理: 用户在第一次参与活动的时候, `user_take_activity`和`user_take_activity_count`是没有entry的.

  在校验库存的时候, 会查询不到数据而报错, 所以需要我们在用户领取活动的时候, 判断是否是第一次参加活动.

  如果是, 添加条目, 不是,` left_count - 1`

  











# 12 应用领域

## 重点

* 分别在两个分库的表 lottery_01.user_take_activity、lottery_02.user_take_activity 中添加 state`【活动单使用状态 0未使用、1已使用】` 状态字段，这个状态字段用于写入中奖信息到 user_strategy_export_000~003 表中时候，两个表可以做一个幂等性的事务。同时还需要加入 strategy_id 策略ID字段，用于处理领取了活动单但执行抽奖失败时，可以继续获取到此抽奖单继续执行抽奖，而不需要重新领取活动。*其实领取活动就像是一种活动镜像信息，可以在控制幂等反复使用*

* 在 lottery-application 模块下新增 process 包用于流程编排，其实它也是 service 服务包是对领域功能的封装，很薄的一层。那么定义成 process 是想大家对流程编排有个概念，一般这一层的处理可以使用可视化的流程编排工具通过拖拽的方式，处理这部分代码的逻辑。
* 抽奖整个活动过程的流程编排，主要包括：对活动的领取、对抽奖的操作、对中奖结果的存放，以及如何处理发奖，对于发奖流程我们设计为MQ触发，后续再补全这部分内容。
* 对于每一个流程节点编排的内容，都是在领域层开发完成的，而应用层只是做最为简单的且很薄的一层。*其实这块也很符合目前很多低代码的使用场景，通过界面可视化控制流程编排，生成代码*

<img src="https://i0.hdslb.com/bfs/album/97705cd4238564b63b0d3669aa89b7cec886e9d7.png" style="zoom: 80%;" />



目前的总体框架

<img src="https://i0.hdslb.com/bfs/album/5c452449c65ce77b4692c5c0544aab9434fa8c02.png@1e_1c.webp"  />

## 代码实现

应用层抽奖流程

```java
@Override
public DrawProcessResult doDrawProcess(DrawProcessReq req) {
    // 1. 领取活动
    PartakeResult partakeResult = activityPartake.doPartake(new PartakeReq(req.getuId(), req.getActivityId()));
    if (!Constants.ResponseCode.SUCCESS.getCode().equals(partakeResult.getCode())) {
        return new DrawProcessResult(partakeResult.getCode(), partakeResult.getInfo());
    }
    Long strategyId = partakeResult.getStrategyId();
    Long takeId = partakeResult.getTakeId();

    // 2. 执行抽奖
    DrawResult drawResult = drawExec.doDrawExec(new DrawReq(req.getuId(), strategyId, String.valueOf(takeId)));
    if (Constants.DrawState.FAIL.getCode().equals(drawResult.getDrawState())) {
        return new DrawProcessResult(Constants.ResponseCode.LOSING_DRAW.getCode(), Constants.ResponseCode.LOSING_DRAW.getInfo());
    }
    DrawAwardInfo drawAwardInfo = drawResult.getDrawAwardInfo();

    // 3. 结果落库
    activityPartake.recordDrawOrder(buildDrawOrderVO(req, strategyId, takeId, drawAwardInfo));

    // 4. 发送MQ，触发发奖流程

    // 5. 返回结果
    return new DrawProcessResult(Constants.ResponseCode.SUCCESS.getCode(), Constants.ResponseCode.SUCCESS.getInfo(), drawAwardInfo);
}

```





## 思考

takeID 和 uuid

uuid就是`take_id == u_id + 参加次数`, 如果`uuId`重复, 主键重复, exception

```String uuid = uId + "_" + activityId + "_" + userTakeActivity.getTakeCount();```

```java
@Override
public void takeActivity(Long activityId, String activityName, Long strategyId, Integer takeCount, Integer userTakeLeftCount, String uId, Date takeDate, Long takeId) {
    UserTakeActivity userTakeActivity = new UserTakeActivity();

    userTakeActivity.setuId(uId);
    userTakeActivity.setTakeId(takeId);
    userTakeActivity.setActivityId(activityId);
    userTakeActivity.setActivityName(activityName);
    userTakeActivity.setTakeDate(takeDate);

    if (null == userTakeLeftCount) {
        userTakeActivity.setTakeCount(1);
    } else {
        userTakeActivity.setTakeCount(takeCount - userTakeLeftCount  + 1);
    }
    userTakeActivity.setStrategyId(strategyId);
    userTakeActivity.setState(Constants.TaskState.NO_USED.getCode());
    String uuid = uId + "_" + activityId + "_" + userTakeActivity.getTakeCount();
    userTakeActivity.setUuid(uuid);

    userTakeActivityDao.insert(userTakeActivity);
}
```



![](https://i0.hdslb.com/bfs/album/cb97b435a57eba311ae25f329d4d1052d79ddecd.png)

结果落库: 写入`user_strategy_export`

应用层的内容少, 基本是调用domain的方法, 判断能不能抽奖, 执行抽奖操作, 成功后把结果落库. 输入的参数也只要stratgy和用户id



## 踩坑

* mybatis xml里 忘记加 `resultMap="userTakeActivityMap"`
* application包的路径 名字没有设置对
* 抽奖策略忘记给`curosrVal`累加了.
* logger debug基本的信息不显示/ 只显示info和error



# 13 规则引擎量化人群参与活动



**组合模式** 搭建用于量化人群的规则, 用于用户参与活动之前, 用过规则引擎过滤性别/年龄/...

**表**: 

**组合**: 逻辑过滤器 + 引擎执行器



* `Repository`接口的实现全部从`@Component`换成了`@Repository`

## 实现

![](https://i0.hdslb.com/bfs/album/f1efb0fe1e11e0b43239b26d1c9c948f3bc8aca1.png)https://i0.hdslb.com/bfs/album/f1efb0fe1e11e0b43239b26d1c9c948f3bc8aca1.png

![](https://i0.hdslb.com/bfs/album/3671c303f2287b55216006188b705d936cd22e2c.png)

<img src="https://i0.hdslb.com/bfs/album/b49dbbd70d5e240669007f94dcf134c26d4419cb.png@1e_1c.webp" style="zoom: 150%;" />

## 思考

之前的流程是, 先领活动号`Partake`, 看`left_count`是不是大于0, 领到活动号才能参会抽奖

现在是再在前面加一步, 根据属性获取可以参加的活动ID



# 14 VO2DTO

重点 : 使用`MapStruct`

`IMapping` :`MapStruct `对象映射接口, 在编译期生成需要手写的getter/setter

* 对象转化操作

  隐藏领域层的信息, DTO转换(防污)

```java
@MapperConfig
public interface IMapping<SOURCE, TARGET> {

    /**
     * 映射同名属性
     * @param var1 源
     * @return     结果
     */
    @Mapping(target = "createTime", dateFormat = "yyyy-MM-dd HH:mm:ss")
    TARGET sourceToTarget(SOURCE var1);

    /**
     * 映射同名属性，反向
     * @param var1 源
     * @return     结果
     */
    @InheritInverseConfiguration(name = "sourceToTarget")
    SOURCE targetToSource(TARGET var1);

    /**
     * 映射同名属性，集合形式
     * @param var1 源
     * @return     结果
     */
    @InheritConfiguration(name = "sourceToTarget")
    List<TARGET> sourceToTarget(List<SOURCE> var1);

    /**
     * 反向，映射同名属性，集合形式
     * @param var1 源
     * @return     结果
     */
    @InheritConfiguration(name = "targetToSource")
    List<SOURCE> targetToSource(List<TARGET> var1);

    /**
     * 映射同名属性，集合流形式
     * @param stream 源
     * @return       结果
     */
    List<TARGET> sourceToTarget(Stream<SOURCE> stream);

    /**
     * 反向，映射同名属性，集合流形式
     * @param stream 源
     * @return       结果
     */
    List<SOURCE> targetToSource(Stream<TARGET> stream);

}
```

运行前, 编译时完成代码的生成

```java
    @Override
    public PrizeDTO sourceToTarget(DrawPrizeVO var1) {
        if ( var1 == null ) {
            return null;
        }

        String userId = null;

        userId = var1.getuId();

        PrizeDTO prizeDTO = new PrizeDTO( userId );

        prizeDTO.setPrizeId( var1.getPrizeId() );
        prizeDTO.setPrizeType( var1.getPrizeType() );
        prizeDTO.setPrizeName( var1.getPrizeName() );
        prizeDTO.setPrizeContent( var1.getPrizeContent() );
        prizeDTO.setStrategyMode( var1.getStrategyMode() );
        prizeDTO.setGrantType( var1.getGrantType() );
        prizeDTO.setGrantDate( var1.getGrantDate() );

        return prizeDTO;
    }

    @Override
    public DrawPrizeVO targetToSource(PrizeDTO var1) {
        if ( var1 == null ) {
            return null;
        }

        DrawPrizeVO drawPrizeVO = new DrawPrizeVO();

        drawPrizeVO.setPrizeId( var1.getPrizeId() );
        drawPrizeVO.setPrizeType( var1.getPrizeType() );
        drawPrizeVO.setPrizeName( var1.getPrizeName() );
        drawPrizeVO.setPrizeContent( var1.getPrizeContent() );
        drawPrizeVO.setStrategyMode( var1.getStrategyMode() );
        drawPrizeVO.setGrantType( var1.getGrantType() );
        drawPrizeVO.setGrantDate( var1.getGrantDate() );

        return drawPrizeVO;
    }
```



## 踩坑

1. 

```java
public RuleQuantificationCrowdResult doRuleQuantificationCrowd(DecisionMatterReq req) {
    // 1. 量化决策
    EngineResult engineResult = engineFilter.process(req);

    if (!engineResult.isSuccess()) {
        return new RuleQuantificationCrowdResult(Constants.ResponseCode.RULE_ERR.getCode(),Constants.ResponseCode.RULE_ERR.getInfo());
    }
    // 2. 封装结果
    RuleQuantificationCrowdResult ruleQuantificationCrowdResult = new RuleQuantificationCrowdResult(Constants.ResponseCode.SUCCESS.getCode(), Constants.ResponseCode.SUCCESS.getInfo());
    ruleQuantificationCrowdResult.setActivityId(Long.valueOf(engineResult.getNodeValue()));
    return ruleQuantificationCrowdResult;
}
```

用户不能直接调用领域层的抽奖接口, 只能调用`LotteryActivityBooth`下面的`doQuantificationDraw`, 获取系统分配的TreeNode id , 通过树id找 Activity id, 可参加活动.

这里面代码有点小bug, 需要修改下: 

`LotteryActivityBooth`下面`activityProcess.doRuleQuantificationCrowd`这个方法

调用`ActivityProcessImpl`下的 `RuleQuantificationCrowd`方法 : `EngineResult engineResult = engineFilter.process(req);`

调用`RuleEngineHandler`下的`EngineResult process(DecisionMatterReq matter) `方法,

这里返回的Result, 没有setSuccess, 默认false, 导致抽奖一直失败

2. `user_take_activity_count`里面更新left_count的时候, 把updta_date也同步更新





# 15 -Kafka



## 踩坑

1. 地址名太长无法启动
2. ` Socket server failed to bind to 0.0.0.0:9092: Address already in use: bind.`
3. ` Timed out waiting for connection while in state: CONNECTING`









![](https://i0.hdslb.com/bfs/album/93ed44c1232bed99d70eda8fcad9831b8e52aa8e.png)



* zookeeper

  ```
  C:\Users\liuqs\kafka_2.13-3.1.0>bin\windows\zookeeper-server-start.bat config\zookeeper.properties
  ```

  

* 启动

  ```
  bin/kafka-server-start.sh -daemon config/server.properties
  ```

  

* consumer

  ```
  bin/kafka-console-producer.sh --broker-list localhost:9092 --topic Hello-Kafka
  ```

  

  

* producer

* topic

  新版本的kafka不再使用zookeeper创建topic/comsumer/producer, 这里的端口和`server.properties`里的保持一致.

  

  创建
  
  ```java
  .\bin\windows\kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic lottery_activity_partake
  ```
  
  	查看

```
 .\bin\windows\kafka-topics.bat --list --bootstrap-server localhost:9092
```



# 16 使用MQ解耦发货流程

![](https://i0.hdslb.com/bfs/album/8dcddb005e567aacdd7116e8f9bdfe2737a5e134.png)

创建`producer`类, 负责执行topic, 实现`sendLotteryInvoice`方法, 将`VO`实例序列化, 通过

`kafkaTemplate.send(TOPIC_INVOICE, objJson);`方法, 发送出去.

```java
@Component
public class KafkaProducer {
    private Logger logger = LoggerFactory.getLogger(KafkaProducer.class);

    @Resource
    private KafkaTemplate<String, Object> kafkaTemplate;

    /**
     * MQ主题：中奖发货单
     *
     * 启动zk：bin/zookeeper-server-start.sh -daemon config/zookeeper.properties
     * 启动kafka：bin/kafka-server-start.sh -daemon config/server.properties
     * 创建topic：bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic lottery_invoice
     */
    public static final String TOPIC_INVOICE = "lottery_invoice";

    /**
     * 发送中奖物品发货单消息
     *
     * @param invoice 发货单
     */
    public ListenableFuture<SendResult<String, Object>> sendLotteryInvoice(InvoiceVO invoice) {
        String objJson = JSON.toJSONString(invoice);
        logger.info("发送MQ消息 topic：{} bizId：{} message：{}", TOPIC_INVOICE, invoice.getuId(), objJson);

        // 发送消息
        return  kafkaTemplate.send(TOPIC_INVOICE, objJson);

    }
}
```

* 抽奖流程的解耦

  任务走到即将发货时, buildVO, 调用producer发送MQ消息

  定义好,成功/失败操作的callback, 对库进行更新

  ```java
  public DrawProcessResult doDrawProcess(DrawProcessReq req) {
      // 1. 领取活动
      
      // 2. 执行抽奖
  
      // 3. 结果落库
  
      // 4. 发送MQ，触发发奖流程
      InvoiceVO invoiceVO = buildInvoiceVO(drawOrderVO);
      ListenableFuture<SendResult<String, Object>> future = kafkaProducer.sendLotteryInvoice(invoiceVO);
      future.addCallback(new ListenableFutureCallback<SendResult<String, Object>>() {
          @Override
          public void onSuccess(SendResult<String, Object> stringObjectSendResult) {
              // 4.1 MQ 消息发送完成，更新数据库表 user_strategy_export.mq_state = 1
              activityPartake.updateInvoiceMqState(invoiceVO.getuId(), invoiceVO.getOrderId(), Constants.MQState.COMPLETE.getCode());
          }
          @Override
          public void onFailure(Throwable throwable) {
              // 4.2 MQ 消息发送失败，更新数据库表 user_strategy_export.mq_state = 2 【等待定时任务扫码补偿MQ消息】
              activityPartake.updateInvoiceMqState(invoiceVO.getuId(), invoiceVO.getOrderId(), Constants.MQState.FAIL.getCode());
          }
      });
  
      // 5. 返回结果
      return new DrawProcessResult(Constants.ResponseCode.SUCCESS.getCode(), Constants.ResponseCode.SUCCESS.getInfo(), drawAwardVO);
  }
  
  ```

  

* 创建消费者类, 监听来自producer的消息

  反序列化, 得到实例, 完成发货操作, 异步处理.

  ```java
  @Component
  public class LotteryInvoiceListener {
  
      private Logger logger = LoggerFactory.getLogger(LotteryInvoiceListener.class);
  
      @Resource
      private DistributionGoodsFactory distributionGoodsFactory;
  
      @KafkaListener(topics = "lottery_invoice", groupId = "lottery")
      public void onMessage(ConsumerRecord<?, ?> record, Acknowledgment ack, @Header(KafkaHeaders.RECEIVED_TOPIC) String topic) {
          Optional<?> message = Optional.ofNullable(record.value());
  
          // 1. 判断消息是否存在
          if (!message.isPresent()) {
              return;
          }
  
          // 2. 处理 MQ 消息
          try {
              // 1. 转化对象（或者你也可以重写Serializer<T>）
              InvoiceVO invoiceVO = JSON.parseObject((String) message.get(), InvoiceVO.class);
  
              // 2. 获取发送奖品工厂，执行发奖
              IDistributionGoods distributionGoodsService = distributionGoodsFactory.getDistributionGoodsService(invoiceVO.getAwardType());
              DistributionRes distributionRes = distributionGoodsService.doDistribution(new GoodsReq(invoiceVO.getuId(), invoiceVO.getOrderId(), invoiceVO.getAwardId(), invoiceVO.getAwardName(), invoiceVO.getAwardContent()));
  
              Assert.isTrue(Constants.AwardState.SUCCESS.getCode().equals(distributionRes.getCode()), distributionRes.getInfo());
  
              // 3. 打印日志
              logger.info("消费MQ消息，完成 topic：{} bizId：{} 发奖结果：{}", topic, invoiceVO.getuId(), JSON.toJSONString(distributionRes));
  
              // 4. 消息消费完成
              ack.acknowledge();
          } catch (Exception e) {
              // 发奖环节失败，消息重试。所有到环节，发货、更新库，都需要保证幂等。
              logger.error("消费MQ消息，失败 topic：{} message：{}", topic, message.get());
              throw e;
          }
      }
  
  }
  ```

  

# 17-使用`xxl-job`定时扫描

`xxl-job`的使用; 

* 后台启动admin
* 给自己的任务注入`@XxlJob("lotteryActivityStateJobHandler")`
* 在后台定期执行管理

## 具体代码

10条10条扫描

```java
    @XxlJob("lotteryActivityStateJobHandler")
    public void lotteryActivityStateJobHandler() throws Exception{
        logger.info("扫描活动状态 Begin");

        //not sure why id == 0
        List<ActivityVO> activityVOList = activityDeploy.scanToDoActivityList(0L);
        if(activityVOList.isEmpty()){
            logger.info("扫描活动状态 End 暂无符合需要扫描的活动列表");
            return;
        }

        // 4：通过
        // 5：运行(活动中)

        while(!activityVOList.isEmpty()){
            for(ActivityVO activityVO : activityVOList){
                Integer state = activityVO.getState();
                switch (state){
                    // 活动状态为审核通过，在临近活动开启时间前，审核活动为活动中。
                    // 在使用活动的时候，需要依照活动状态和时间两个字段进行判断和使用。
                    case 4:
                        Result state4Result = stateHandler.doing(activityVO.getActivityId(), Constants.ActivityState.PASS);
                        logger.info("扫描活动状态为活动中 结果：{} activityId：{} activityName：{} creator：{}",
                                    JSON.toJSONString(state4Result), activityVO.getActivityId(),
                                    activityVO.getActivityName(), activityVO.getCreator());
                        break;
                    // 扫描时间已过期的活动，从活动中状态变更为关闭状态
                    // 【这里也可以细化为2个任务来处理，也可以把时间判断放到数据库中操作】
                    case 5:
                        if(activityVO.getEndDateTime().before(new Date())){
                            Result state5Result = stateHandler.close(activityVO.getActivityId(), Constants.ActivityState.DOING);
                            logger.info("扫描活动状态为关闭 结果：{} activityId：{} activityName：{} creator：{}",
                                        JSON.toJSONString(state5Result), activityVO.getActivityId(),
                                        activityVO.getActivityName(), activityVO.getCreator());
                        }
                        break;
                    default:
                        break;
                }
            }

            // 获取集合中最后一条记录，继续扫描后面10条记录
            ActivityVO activityVO = activityVOList.get(activityVOList.size() - 1);
            activityVOList = activityDeploy.scanToDoActivityList(activityVO.getId());
        }


        logger.info("扫描活动状态 End");
    }

```



## 结果



启动springboot项目

在xxl-job管理中心里, 注册执行器和任务管理, 对表定期扫描

* 扫描审核通过, 还未进行的活动, 变成进行中
* 扫描已经过期, 但认为进行中的任务, 置为结束

![](https://i0.hdslb.com/bfs/album/e4d9db0a0a21cbfab56e3be4610a76a99d02db17.png)

![](https://i0.hdslb.com/bfs/album/d5259d21ae2d8ecd2795693eb69c97899372ab41.png)

```java
2022-03-20 22:42:22.425  INFO 7960 --- [      Thread-18] c.l.l.application.worker.LotteryXxlJob   : 扫描活动状态 Begin
2022-03-20 22:42:22.879  INFO 7960 --- [      Thread-18] c.l.l.application.worker.LotteryXxlJob   : 扫描活动状态为活动中 结果：{"code":"成功","info":"活动变更活动中完成"} activityId：100002 activityName：活动名02 creator：xiaofuge
2022-03-20 22:42:22.898  INFO 7960 --- [      Thread-18] c.l.l.application.worker.LotteryXxlJob   : 扫描活动状态为关闭 结果：{"code":"成功","info":"活动关闭成功"} activityId：100003 activityName：活动3 creator：lqs
2022-03-20 22:42:22.915  INFO 7960 --- [      Thread-18] c.l.l.application.worker.LotteryXxlJob   : 扫描活动状态 End
```



# 18-扫描库表补偿发货单MQ消息

消息的接受和发送: 需要保证幂等性

设置, 对哪一个库, 哪一个表进行扫描, 

![](https://i0.hdslb.com/bfs/album/5f87ad4c28b848343fbaeada199636d97e4ea79b.png)****

- 这一部分就是使用任务扫描库表的操作，在 `activityPartake.scanInvoiceMqState` 方法中，会设定路由，如下：

```
@Override
public List<InvoiceVO> scanInvoiceMqState(int dbCount, int tbCount) {
    try {
        // 设置路由
        dbRouter.setDBKey(dbCount);
        dbRouter.setTBKey(tbCount);
        // 查询数据
        return userTakeActivityRepository.scanInvoiceMqState();
    } finally {
        dbRouter.clear();
    }
}
```

- 另外这里有一点是关于分布式设计的思考，一般我们运行在线上的任务都是由多个实例共同完成，所以这里我们配置里一个任务的参数，已达到可以满足每个任务实例只跑自己需要扫描的库表。

  翻译: 可以配置多个不同的任务, 每个任务只扫描一个库, 分摊负载



```java
2022-03-21 00:29:07.032  INFO 41324 --- [      Thread-18] o.a.kafka.common.utils.AppInfoParser     : Kafka version: 3.0.0
2022-03-21 00:29:07.032  INFO 41324 --- [      Thread-18] o.a.kafka.common.utils.AppInfoParser     : Kafka commitId: 8cb0a5e9d3441962
2022-03-21 00:29:07.032  INFO 41324 --- [      Thread-18] o.a.kafka.common.utils.AppInfoParser     : Kafka startTimeMs: 1647793747032
2022-03-21 00:29:07.036  INFO 41324 --- [ad | producer-1] org.apache.kafka.clients.Metadata        : [Producer clientId=producer-1] Cluster ID: kY8n_6u_SLCy9oy9qEzrcw
2022-03-21 00:29:07.046  INFO 41324 --- [      Thread-18] c.l.l.a.mq.producer.KafkaProducer        : 发送MQ消息 topic：lottery_invoice bizId：fustack message：{"orderId":1444541565086367744,"prizeContent":"Code","prizeId":"5","prizeName":"Book","prizeType":1,"uId":"fustack"}
2022-03-21 00:29:07.046  INFO 41324 --- [      Thread-18] c.l.l.a.mq.producer.KafkaProducer        : 发送MQ消息 topic：lottery_invoice bizId：fustack message：{"orderId":1444810184030633984,"prizeContent":"Code","prizeId":"4","prizeName":"AirPods","prizeType":1,"uId":"fustack"}
2022-03-21 00:29:07.046  INFO 41324 --- [      Thread-18] c.l.l.a.mq.producer.KafkaProducer        : 发送MQ消息 topic：lottery_invoice bizId：fustack message：{"orderId":1444820311156670464,"prizeContent":"Code","prizeId":"2","prizeName":"iphone","prizeType":1,"uId":"fustack"}
2022-03-21 00:29:07.067  INFO 41324 --- [      Thread-18] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：2 扫描表：1 扫描数：0
2022-03-21 00:29:07.084  INFO 41324 --- [      Thread-18] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：2 扫描表：2 扫描数：0
2022-03-21 00:29:07.101  INFO 41324 --- [      Thread-18] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：2 扫描表：3 扫描数：0
2022-03-21 00:29:07.102  INFO 41324 --- [      Thread-18] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 完成 param：["1","2"]
2022-03-21 00:29:07.116  INFO 41324 --- [ntainer#0-0-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：2 tbIdx：0
2022-03-21 00:29:07.140  INFO 41324 --- [ntainer#0-0-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：fustack 发奖结果：{"code":1,"info":"发奖成功","uId":"fustack"}
2022-03-21 00:29:07.166  INFO 41324 --- [ntainer#0-0-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：2 tbIdx：0
2022-03-21 00:29:07.186  INFO 41324 --- [ntainer#0-0-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：fustack 发奖结果：{"code":1,"info":"发奖成功","uId":"fustack"}
2022-03-21 00:29:07.188  INFO 41324 --- [ntainer#0-0-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：2 tbIdx：0
2022-03-21 00:29:07.211  INFO 41324 --- [ntainer#0-0-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：fustack 发奖结果：{"code":1,"info":"发奖成功","uId":"fustack"}
2022-03-21 00:29:07.216  INFO 41324 --- [ntainer#0-0-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：2 tbIdx：0
2022-03-21 00:29:07.236  INFO 41324 --- [ntainer#0-0-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：fustack 发奖结果：{"code":1,"info":"发奖成功","uId":"fustack"}
```





# 19



锁库存编号

![](https://i0.hdslb.com/bfs/album/87efa0ced90d8f922a938cba616fbd05d1bcc3df.png)



## 实现

1. 滑块库存锁设计

    **![](https://i0.hdslb.com/bfs/album/d1488551d1f71b75ae94c3d902c48da0a5858084.png)**
    
2. 滑块库存锁实现

    1. 库存扣减操作
    2. 并发锁删除处理

3. 活动秒杀流程处理

4. 发送MQ消息, 处理数据一致性

5. 活动领取记录MQ消费

## Docker 配置

* 启动docker desktop

* 拉取镜像

* 运行 启动容器, 并进行端口映射

  ```
  docker run --name redis -p 6379:6379 -d redis
  ```

* 启动redis

  ```
  docker start redis
  ```




## 细节

* Redis配置类

  * `@EnableCaching`

  * `RedisConnectionFactory`

  * `RedisTemplate`

  * `Jackson2JsonRedisSerializer`

  * `ObjectMap`

    ```java
    objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
    ```

    ALL: getter/setter/field(类的data member, 一般非static)

    ANY: private + public + protected

  * s

  给5大类型的数据配置`SetOperations<String, Object>`

  ```java
  @Bean
  public SetOperations<String, Object> setOperations(RedisTemplate<String, Object> redisTemplate){
      return redisTemplate.opsForSet();
  }
  
  ```

  

  

* Redis工具类

  对5大类型数据进行操作

  ```java
  /**
           * 分布式锁
           * @param key               锁住的key
           * @param lockExpireMils    锁住的时长。如果超时未解锁，视为加锁线程死亡，其他线程可夺取锁
           * @return
           */
  public boolean setNx(String key, Long lockExpireMils) {
      return (boolean) redisTemplate.execute((RedisCallback) connection -> {
          //获取锁
          return connection.setNX(key.getBytes(), String.valueOf(System.currentTimeMillis() + lockExpireMils + 1).getBytes());
      });
  }
  ```

  

  

  ```java
      /**
       * 使用Redis的消息队列
       *
       * @param channel
       * @param message 消息内容
       */
      public void convertAndSend(String channel, Object message) {
          redisTemplate.convertAndSend(channel, message);
      }
  ```

  `BoundListOperations`

  ```java
  /**
   * 将数据添加到Redis的list中（从右边添加）
   *
   * @param listKey
   * @param timeout 有效时间
   * @param unit    时间类型
   * @param values  待添加的数据
   */
  public void addToListRight(String listKey, long timeout, TimeUnit unit, Object... values) {
      //绑定操作
      BoundListOperations<String, Object> boundValueOperations = redisTemplate.boundListOps(listKey);
      //插入数据
      boundValueOperations.rightPushAll(values);
      //设置过期时间
      boundValueOperations.expire(timeout, unit);
  }
  
  /**
   * 根据起始结束序号遍历Redis中的list
   *
   * @param listKey
   * @param start   起始序号
   * @param end     结束序号
   * @return
   */
  public List<Object> rangeList(String listKey, long start, long end) {
      //绑定操作
      BoundListOperations<String, Object> boundValueOperations = redisTemplate.boundListOps(listKey);
      //查询数据
      return boundValueOperations.range(start, end);
  }
  
  /**
   * 弹出右边的值 --- 并且移除这个值
   *
   * @param listKey
   */
  public Object rightPop(String listKey) {
      //绑定操作
      BoundListOperations<String, Object> boundValueOperations = redisTemplate.boundListOps(listKey);
      return boundValueOperations.rightPop();
  }
  
  ```


* BaseActivityPartake

  6步逻辑,  这里是怎么使用redis的呢?

  ```java
  // 4. 扣减活动库存，通过Redis【活动库存扣减编号，作为锁的Key，缩小颗粒度】 Begin
  StockResult subtractionActivityResult = this.subtractionActivityStockByRedis(req.getuId(), req.getActivityId(), activityBillVO.getStockCount());
  if (!Constants.ResponseCode.SUCCESS.getCode().equals(subtractionActivityResult.getCode())) {
      this.recoverActivityCacheStockByRedis(req.getActivityId(), subtractionActivityResult.getStockKey(), subtractionActivityResult.getCode());
  }
  
  // 5. 领取活动信息【个人用户把活动信息写入到用户表】
  Long takeId = idGeneratorMap.get(Constants.Ids.SnowFlake).nextId();
  Result grabResult = this.grabActivity(req, activityBillVO, takeId);
  if (!Constants.ResponseCode.SUCCESS.getCode().equals(grabResult.getCode())) {
      this.recoverActivityCacheStockByRedis(req.getActivityId(), subtractionActivityResult.getStockKey(), grabResult.getCode());
      return new PartakeResult(grabResult.getCode(), grabResult.getInfo());
  }
  
  // 6. 扣减活动库存，通过Redis End
  this.recoverActivityCacheStockByRedis(req.getActivityId(), subtractionActivityResult.getStockKey(), Constants.ResponseCode.SUCCESS.getCode());
  
  ```

* Kafak消费完需要确认, 不然消息一直存在

  ````
  // 4. 消息消费完成
  ack.acknowledge();
  ```



## 流程

* redis的使用

  在redis中缓存 `lottery_activity_stock_usage_count_100001` 代表 `10001`号活动的使用量(参与数), 减去初始的总库存, 为当前的剩余库存. 

  如果计算得到的剩余库存, 小于库中记录的剩余库存, 完成更新. 如果`stockUsedCount > stockCount`恢复原来的使用量, 

  同时每次更新时(在redis缓存里),  给每一个用量`SetNx`加一个`lottery_activity_stock_usage_count_100001_xxx`分布式锁, 如果没拿到锁, 用户秒杀失败. 同时删除这个key对应的锁. 所以我们的策略是允许少卖, 不能超卖.

  

  这里有一点不太确定: , 

* MQ

  每次抽奖完成后, 显示已经完成抽奖, 后台会生成一个`mq_state`为`0`的`user_strategy_export`

  XXL负责定期扫描指定的库/表, 把未发送且超时(`0`) 和 发送失败(`2`)的, 全部放到MQ里, 用于处理

  s

  * 创建`KafkaProducer`类, 给不同topic配置不同的send方法, 在需要异步处理的时候(发奖)的时候, 调用方法.

    调用的时候, 给`send`的返回值`feature`加上`callBack`, 消费成功/失败-更新落库, 异步处理的消息被listener消费后, 会自动调用`callBack`函数

  * 给不同的Kafka消息创建对应的`Listener`类,  

    对message反序列化, 得到VO对象, 取出里面的信息, 进行消息的异步处理(发奖)

    记得最后要`ack.acknowledge()`, 代表已经消费了这个message

    ```java
    @KafkaListener(topics = KafkaProducer.TOPIC_INVOICE, groupId = "lottery")
    public void onMessage(ConsumerRecord<?, ?> record, Acknowledgment ack, @Header(KafkaHeaders.RECEIVED_TOPIC) String topic) {
         //处理
        ack.acknowledge();
    }
    ```

* XxlJob

  设置好之后, 加上`@XxlJob("lotteryOrderMQStateJobHandler")`完成配置, 可以定期对库进行扫描

  如果找到:

  ​	可以参加的活动:  完成活动状态流转

  ​	没有发货的单号:  发送MQ消息, 等待处理.

## 结果

```java
2022-03-22 10:02:58.785  INFO 42356 --- [ntainer#0-0-C-1] m.c.LotteryActivityPartakeRecordListener : 消费MQ消息，异步扣减活动库存 message：{"activityId":100001,"stockCount":100,"stockSurplusCount":99,"uId":"xiaofuge"}
2022-03-22 10:02:58.799  INFO 42356 --- [ntainer#0-0-C-1] m.c.LotteryActivityPartakeRecordListener : 消费MQ消息，异步扣减活动库存 message：{"activityId":100001,"stockCount":100,"stockSurplusCount":98,"uId":"xiaofuge"}
2022-03-22 10:04:23.884  INFO 42356 --- [ntainer#0-0-C-1] m.c.LotteryActivityPartakeRecordListener : 消费MQ消息，异步扣减活动库存 message：{"activityId":100001,"stockCount":100,"stockSurplusCount":97,"uId":"xiaofuge"}
2022-
```

```java
2022-03-22 10:04:24.058  INFO 42356 --- [ntainer#1-4-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：1 tbIdx：1
2022-03-22 10:04:24.082  INFO 42356 --- [ntainer#1-4-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：xiaofuge 发奖结果：{"code":1,"info":"发奖成功","uId":"xiaofuge"}

```

```java
2022-03-22 10:21:01.030  INFO 42356 --- [adPool-43181356] c.xxl.job.core.executor.XxlJobExecutor   : >>>>>>>>>>> xxl-job regist JobThread success, jobId:3, handler:com.xxl.job.core.handler.impl.MethodJobHandler@7d133fb7[class cn.leoil.lottery.application.worker.LotteryXxlJob#lotteryOrderMQStateJobHandler]
2022-03-22 10:21:01.044  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 开始 params：["1","2"]
2022-03-22 10:21:01.072  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：1 扫描表：0 扫描数：0
2022-03-22 10:21:01.090  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：1 扫描表：1 扫描数：0
2022-03-22 10:21:01.109  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：1 扫描表：2 扫描数：0
2022-03-22 10:21:01.127  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：1 扫描表：3 扫描数：0
2022-03-22 10:21:01.151  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：2 扫描表：0 扫描数：4
2022-03-22 10:21:01.156  INFO 42356 --- [      Thread-25] c.l.l.a.mq.producer.KafkaProducer        : 发送MQ消息 topic：lottery_invoice bizId：fustack message：{"orderId":1444540456057864192,"prizeContent":"Code","prizeId":"3","prizeName":"ipad","prizeType":1,"uId":"fustack"}

```

```java
2022-03-22 10:21:01.240  INFO 42356 --- [      Thread-25] c.l.l.a.mq.producer.KafkaProducer        : 发送MQ消息 topic：lottery_invoice bizId：fustack message：{"orderId":1444541565086367744,"prizeContent":"Code","prizeId":"5","prizeName":"Book","prizeType":1,"uId":"fustack"}
2022-03-22 10:21:01.240  INFO 42356 --- [      Thread-25] c.l.l.a.mq.producer.KafkaProducer        : 发送MQ消息 topic：lottery_invoice bizId：fustack message：{"orderId":1444810184030633984,"prizeContent":"Code","prizeId":"4","prizeName":"AirPods","prizeType":1,"uId":"fustack"}
2022-03-22 10:21:01.240  INFO 42356 --- [      Thread-25] c.l.l.a.mq.producer.KafkaProducer        : 发送MQ消息 topic：lottery_invoice bizId：fustack message：{"orderId":1444820311156670464,"prizeContent":"Code","prizeId":"2","prizeName":"iphone","prizeType":1,"uId":"fustack"}
2022-03-22 10:21:01.245  INFO 42356 --- [ntainer#1-4-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：2 tbIdx：0
2022-03-22 10:21:01.259  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：2 扫描表：1 扫描数：0
2022-03-22 10:21:01.263  INFO 42356 --- [ntainer#1-4-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：fustack 发奖结果：{"code":1,"info":"发奖成功","uId":"fustack"}
2022-03-22 10:21:01.270  INFO 42356 --- [ntainer#1-4-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：2 tbIdx：0
2022-03-22 10:21:01.277  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：2 扫描表：2 扫描数：0
2022-03-22 10:21:01.289  INFO 42356 --- [ntainer#1-4-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：fustack 发奖结果：{"code":1,"info":"发奖成功","uId":"fustack"}
2022-03-22 10:21:01.290  INFO 42356 --- [ntainer#1-4-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：2 tbIdx：0
2022-03-22 10:21:01.293  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：2 扫描表：3 扫描数：0
2022-03-22 10:21:01.293  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 完成 param：["1","2"]
2022-03-22 10:21:01.308  INFO 42356 --- [ntainer#1-4-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：fustack 发奖结果：{"code":1,"info":"发奖成功","uId":"fustack"}
2022-03-22 10:21:01.310  INFO 42356 --- [ntainer#1-4-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：2 tbIdx：0
2022-03-22 10:21:01.327  INFO 42356 --- [ntainer#1-4-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：fustack 发奖结果：{"code":1,"info":"发奖成功","uId":"fustack"}
2022-03-22 10:21:03.100  INFO 42356 --- [adPool-43181356] c.xxl.job.core.executor.XxlJobExecutor   : >>>>>>>>>>> xxl-job regist JobThread success, jobId:2, handler:com.xxl.job.core.handler.impl.MethodJobHandler@1291aab5[class cn.leoil.lottery.application.worker.LotteryXxlJob#lotteryActivityStateJobHandler]
2022-03-22 10:21:03.101  INFO 42356 --- [      Thread-26] c.l.l.application.worker.LotteryXxlJob   : 扫描活动状态 Begin
2022-03-22 10:21:03.135  INFO 42356 --- [      Thread-26] c.l.l.application.worker.LotteryXxlJob   : 扫描活动状态 End
2022-03-22 10:21:07.955  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 开始 params：["1","2"]
2022-03-22 10:21:07.972  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：1 扫描表：0 扫描数：0
2022-03-22 10:21:07.988  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：1 扫描表：1 扫描数：0
2022-03-22 10:21:08.005  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：1 扫描表：2 扫描数：0
2022-03-22 10:21:08.022  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：1 扫描表：3 扫描数：0
2022-03-22 10:21:08.038  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：2 扫描表：0 扫描数：4
2022-03-22 10:21:08.039  INFO 42356 --- [      Thread-25] c.l.l.a.mq.producer.KafkaProducer        : 发送MQ消息 topic：lottery_invoice bizId：fustack message：{"orderId":1444540456057864192,"prizeContent":"Code","prizeId":"3","prizeName":"ipad","prizeType":1,"uId":"fustack"}
2022-03-22 10:21:08.039  INFO 42356 --- [      Thread-25] c.l.l.a.mq.producer.KafkaProducer        : 发送MQ消息 topic：lottery_invoice bizId：fustack message：{"orderId":1444541565086367744,"prizeContent":"Code","prizeId":"5","prizeName":"Book","prizeType":1,"uId":"fustack"}
2022-03-22 10:21:08.039  INFO 42356 --- [      Thread-25] c.l.l.a.mq.producer.KafkaProducer        : 发送MQ消息 topic：lottery_invoice bizId：fustack message：{"orderId":1444810184030633984,"prizeContent":"Code","prizeId":"4","prizeName":"AirPods","prizeType":1,"uId":"fustack"}
2022-03-22 10:21:08.039  INFO 42356 --- [      Thread-25] c.l.l.a.mq.producer.KafkaProducer        : 发送MQ消息 topic：lottery_invoice bizId：fustack message：{"orderId":1444820311156670464,"prizeContent":"Code","prizeId":"2","prizeName":"iphone","prizeType":1,"uId":"fustack"}
2022-03-22 10:21:08.041  INFO 42356 --- [ntainer#1-4-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：2 tbIdx：0
2022-03-22 10:21:08.056  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：2 扫描表：1 扫描数：0
2022-03-22 10:21:08.058  INFO 42356 --- [ntainer#1-4-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：fustack 发奖结果：{"code":1,"info":"发奖成功","uId":"fustack"}
2022-03-22 10:21:08.060  INFO 42356 --- [ntainer#1-4-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：2 tbIdx：0
2022-03-22 10:21:08.072  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：2 扫描表：2 扫描数：0
2022-03-22 10:21:08.077  INFO 42356 --- [ntainer#1-4-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：fustack 发奖结果：{"code":1,"info":"发奖成功","uId":"fustack"}
2022-03-22 10:21:08.079  INFO 42356 --- [ntainer#1-4-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：2 tbIdx：0
2022-03-22 10:21:08.089  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 扫描库：2 扫描表：3 扫描数：0
2022-03-22 10:21:08.089  INFO 42356 --- [      Thread-25] c.l.l.application.worker.LotteryXxlJob   : 扫描用户抽奖奖品发放MQ状态[Table = 2*4] 完成 param：["1","2"]
2022-03-22 10:21:08.098  INFO 42356 --- [ntainer#1-4-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：fustack 发奖结果：{"code":1,"info":"发奖成功","uId":"fustack"}
2022-03-22 10:21:08.100  INFO 42356 --- [ntainer#1-4-C-1] c.b.m.d.r.s.i.DBRouterStrategyHashCode   : 数据库路由 dbIdx：2 tbIdx：0
2022-03-22 10:21:08.120  INFO 42356 --- [ntainer#1-4-C-1] c.l.l.a.m.c.LotteryInvoiceListener       : 消费MQ消息，完成 topic：lottery_invoice bizId：fustack 发奖结果：{"code":1,"info":"发奖成功","uId":"fustack"}
2022-03-22 10:22:36.350  INFO 42356 --- [      Thread-26] com.xxl.job.core.thread.JobThread        : >>>>>>>>>>> xxl-job JobThread stoped, hashCode:Thread[Thread-26,10,main]
2022-03-22 10:22:41.315  INFO 42356 --- [      Thread-25] com.xxl.job.core.thread.JobThread        : >>>>>>>>>>> xxl-job JobThread stoped, hashCode:Thread[Thread-25,10,main]

```



# 3-02 运营后台 活动列表数据展示



# 问题收集

* 数据库的缓存不一致如何处理: 

  缓存一致性: 根本解决方法: 加锁

  只要你用了缓存，一致性不可完全避免
  只要不要把脏数据放缓存一直没刷就还好。剩下的就是解决不一致窗口时间，如何让他变小

  

  延时双删是个比较好的处理方案。简单，快速，而且一般没啥问题。也会有不一致问题出现, 但超级小概率事件在中小公司投入研发成本大于收益，可以忽略。

  携程的处理方案: https://www.infoq.cn/article/ahsatgossvmtpzg3rafb


  用canal + kafka 来处理缓存也是种方案，但是要注意如果分库了之后可能要注意数据库延时同步导致的查询到旧数据的问题	要设置合理的消费时间。

  

  redis 为什么使用槽同步，将每个key在哪个机器上存储在每台机器上不行吗？

  集群内部都需要gossip交互

  主要就是每台机器要存储一份槽空间，而且是基于gossip通信，这样可以让每台机器传输信息量在O(1)不会导致网络风暴

  

  主从解决并发量问题
  集群解决数据量问题

  

  

* s



# 相关问题;

![](https://i0.hdslb.com/bfs/album/4640e5c3674f9eefaa87d4df1f744e5999d4a40d.png)

![](https://i0.hdslb.com/bfs/album/a8974f15c50a81ac4cce9d061dc9758550a9cf7b.png)

![](https://i0.hdslb.com/bfs/album/22dcf0f8b6017d0cb0fa0e28faebb72538470ec4.png)
