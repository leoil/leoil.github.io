# TIL

## JAVA元注解

* `@Target`定义注解的使用位置

  - TYPE：类，接口或者枚举
  - FIELD：域，包含枚举常量
  - METHOD：方法
  - PARAMETER：参数
  - CONSTRUCTOR：构造方法
  - LOCAL_VARIABLE：局部变量
  - ANNOTATION_TYPE：注解类型
  - PACKAGE：包

  于是有了spring中类似于`Component` `Service`一类的注解, 有点只能用于修饰类, 有的只能作用与方法

* `@retention`注解的生命周期

  - SOURCE：源码级别保留，编译后即丢弃。
  - CLASS：编译级别保留，编译后的class文件中存在，在jvm运行时丢弃，这是默认值。
  - RUNTIME：运行级别保留，编译后的class文件中存在，在jvm运行时保留，可以被反射调用。

* `@Inherited`

  是否可被继承

## spring 注解的一些内容

### @Configuration vs  @Bean

Full @Configuration vs “lite” @Bean mode?

When `@Bean` methods are declared within classes that are not annotated with `@Configuration`, they are referred to as being processed in a “lite” mode. Bean methods declared in a `@Component` or even in a plain old class are considered to be “lite”, with a different primary purpose of the containing class and a `@Bean` method being a sort of bonus there. For example, service components may expose management views to the container through an additional `@Bean` method on each applicable component class. In such scenarios, `@Bean` methods are a general-purpose factory method mechanism.

Unlike full `@Configuration`, lite `@Bean` methods cannot declare inter-bean dependencies. Instead, they operate on their containing component’s internal state and, optionally, on arguments that they may declare. Such a `@Bean` method should therefore not invoke other `@Bean` methods. Each such method is literally only a factory method for a particular bean reference, without any special runtime semantics. The positive side-effect here is that no CGLIB subclassing has to be applied at runtime, so there are no limitations in terms of class design (that is, the containing class may be `final` and so forth).

In common scenarios, `@Bean` methods are to be declared within `@Configuration` classes, ensuring that “full” mode is always used and that cross-method references therefore get redirected to the container’s lifecycle management. This prevents the same `@Bean` method from accidentally being invoked through a regular Java call, which helps to reduce subtle bugs that can be hard to track down when operating in “lite” mode.

# IOC相关(bean从xml读取到产生实例的过程)

* 理解

  > 　　在平时的java应用开发中，我们要实现某一个功能或者说是完成某个业务逻辑时至少需要两个或以上的对象来协作完成，在没有使用Spring的时候，每个对象在需要使用他的合作对象时，自己均要使用像new object() 这样的语法来将合作对象创建出来，这个合作对象是由自己主动创建出来的，创建合作对象的主动权在自己手上，自己需要哪个合作对象，就主动去创建，创建合作对象的主动权和创建时机是由自己把控的，而这样就会使得对象间的耦合度高了，A对象需要使用合作对象B来共同完成一件事，A要使用B，那么A就对B产生了依赖，也就是A和B之间存在一种耦合关系，并且是紧密耦合在一起，而使用了Spring之后就不一样了，创建合作对象B的工作是由Spring来做的，Spring创建好B对象，然后存储到一个容器里面，当A对象需要使用B对象时，Spring就从存放对象的那个容器里面取出A要使用的那个B对象，然后交给A对象使用，至于Spring是如何创建那个对象，以及什么时候创建好对象的，A对象不需要关心这些细节问题(你是什么时候生的，怎么生出来的我可不关心，能帮我干活就行)，A得到Spring给我们的对象之后，两个人一起协作完成要完成的工作即可。
  >
  > 　　所以**控制反转IoC(Inversion of Control)是说创建对象的控制权进行转移，以前\**创建对象的主动权和创建时机是由自己把控的\**，而现在这种权力转移到第三方**，比如转移交给了IoC容器，它就是一个专门用来创建对象的工厂，你要什么对象，它就给你什么对象，有了 IoC容器，依赖关系就变了，原先的依赖关系就没了，它们都依赖IoC容器了，通过IoC容器来建立它们之间的关系。
  >
  > 　　这是我对Spring的IoC**(控制反转)**的理解。DI**(依赖注入)**其实就是IOC的另外一种说法，DI是由Martin Fowler 在2004年初的一篇论文中首次提出的。他总结：***\*控制的什么被反转了？就是：获得依赖对象的方式反转了。\****

[Spring IOC 容器源码分析_Javadoop](https://javadoop.com/post/spring-ioc)

创建bean容器前准备

创建Bean容器  `obtainFreshBeanFactory()`

```java
customizeBeanFactory(beanFactory);

```

``loadBeanDefinitions(beanFactory)``: 这个方法将根据配置，加载各个 Bean，然后放到 BeanFactory 中。

` parseBeanDefinitions(root, this.delegate) :` 解析XML



加载/注册Bean(XML中) + 初始化Beans

​	`beanDefinition`就是定义好的`bean`类：从ＸＭＬ里读入(<bean/>)，

​	注册: 放入`this.beanDefinitionMap.put(beanName, beanDefinition);`



* 初始化所有singleton beans 

  ```java
  if (instanceWrapper == null) {
      // 说明不是 FactoryBean，这里实例化 Bean，这里非常关键，细节之后再说
      instanceWrapper = createBeanInstance(beanName, mbd, args);
  }
  // 这个就是 Bean 里面的 我们定义的类 的实例，很多地方我直接描述成 "bean 实例"
  final Object bean = (instanceWrapper != null ? instanceWrapper.getWrappedInstance() : null);
  ```

  

  `doCreateBean`

  接下来我们挑` doCreateBean `中的三个细节出来说说。

  1. 创建 Bean 实例的 `createBeanInstance `方法 : 返回一个`BeanWrapper`


  ```java
  // 对于普通的 Bean，只要调用 getBean(beanName) 这个方法就可以进行初始化了
  getBean(beanName);
  ```

  ​	根据类型创建`singleton`和`prototype`

  ```java
  if (mbd.isSingleton()) {
      sharedInstance = getSingleton(beanName, new ObjectFactory<Object>() {
          @Override
          public Object getObject() throws BeansException {
              try {
                  // 执行创建 Bean，详情后面再说
                  return createBean(beanName, mbd, args);
              }
              catch (BeansException ex) {
                  destroySingleton(beanName);
                  throw ex;
              }
          }
      });
      bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
  }
  ```
  *  createBean 方法

    ``protected abstract Object createBean(String beanName, RootBeanDefinition mbd, Object[] args) throws BeanCreationException;``

  * 使用工厂模式实例化

    `instantiateUsingFactoryMethod(beanName, mbd, args);`

    

    通过构造方法实例化

    `BeanUtils.instantiateClass(constructorToUse);`

    实例化 : 如果不存在方法覆写 那就使用 java 反射进行实例化，否则使用 CGLIB,

    `beanInstance = getInstantiationStrategy().instantiate(mbd, beanName, parent);`

  


  2. 依赖注入的` populateBean` 方法

     通过名字找到所有属性值，如果是 bean 依赖，先初始化依赖的 bean。记录依赖关系

  3. 回调方法 `initializeBean`。 

# AOP

## 使用

[Spring AOP 使用介绍，从前世到今生_Javadoop](https://javadoop.com/post/spring-aop-intro)

### AOP/AspectJ/Spring AOP

* Spring AOP

  基于动态代理实现 [3.1. JDK 动态代理机制 (gitee.io)](https://snailclimb.gitee.io/javaguide/#/docs/java/basis/proxy?id=_31-jdk-动态代理机制)

  动态代理对比静态代理: 不用单独创建代理类

  * JDK代理

    **`InvocationHandler` 接口和 `Proxy` 类是核心**

    ```java
    public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h)
        throws IllegalArgumentException
    {
        ......
    }
    ```

    ```java
    public interface InvocationHandler {
    
        /**
         * 当你使用代理对象调用方法的时候实际会调用到这个方法
         */
        public Object invoke(Object proxy, Method method, Object[] args)
            throws Throwable;
    }
    ```

  * CGLIB代理 [源码详解系列(一)--cglib动态代理的使用和分析 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/106069224)

    JDK代理只能代理实现了接口的类,

    基于[ASM](http://www.baeldung.com/java-asm)的字节码生成库,运行时对字节码进行修改和动态生成。

    

    1. cglib 的代理类会缓存起来，不会重复创建；
    2. 使用的是 asm 来生成`Class`文件。

    会创建两个`fastClass`文件

    * 代理类: 主要使用, 可以直接调用 目标类的方法, **快**, 不用**再走反射**调用

    * 被代理类

      

    代理步骤: 

    1. 定义一个类；

    2. 自定义 `MethodInterceptor` 并重写 `intercept` 方法，`intercept` 用于拦截增强被代理类的方法，和 JDK 动态代理中的 `invoke` 方法类似；

       使用`proxy.invokeSuper(obj, args);`

    3. 创建`Enhancer`动态代理类, 设置类加载器和被代理类/拦截器

       通过 `Enhancer` 类的 `create()`创建代理类；

* AOP依赖与IOC管理

*  只能作用于 Spring 容器中的 Bean，它是使用纯粹的 Java 代码实现的，只能作用于 bean 的方法。

* AspectJ: 静态注入

## AOP术语

* Advice

  拦截 拦截器的粒度只控制到了类级别，类中所有的方法都进行了拦截

  ![6](https://www.javadoop.com/blogimages/spring-aop/6.png)

* Advisor

  **它内部需要指定一个 Advice**，Advisor 决定该拦截哪些方法，拦截后需要完成的工作还是内部的 Advice 来做。

  

  每个 advisor 内部持有 advice 实例，advisor 负责匹配，内部的 advice 负责实现拦截处理。配置了各个 advisor 后，配置 `DefaultAdvisorAutoProxyCreator` 使得所有的 advisor 配置自动生效。

* `Interceptor`

问题: 每个`bean`都需要一个代理. 使用的时候需要手动获取, 不能自动根据类型注入

* autoproxy

  自动代理，也就是说当 Spring 发现一个 bean 需要被切面织入的时候，Spring 会自动生成这个 bean 的一个代理来拦截方法的执行，确保定义的切面能被执行

  ` BeanNameAutoProxyCreator `, 正则匹配名字

  不需要通过代理找`bean`

  ![10](https://www.javadoop.com/blogimages/spring-aop/10.png)

* Aspect

  @Aspect 注解要作用在 bean 上面，不管是使用 @Component 等注解方式，还是在 xml 中配置 bean，首先它需要是一个 bean。

* Pointcut

  

Joinpoint 

![](https://i0.hdslb.com/bfs/album/2ee8b75ef0c407515a64b7c02de1dca56b72820f.png)

## 源码

在 Spring 的容器中，我们面向的对象是一个个的 bean 实例，bean 是什么？我们可以简单理解为是 `BeanDefinition `的实例，Spring 会根据 `BeanDefinition `中的信息为我们生产合适的 `bean` 实例出来。

需要使用 `bean` 的时候，通过 IOC 容器的` getBean(…)` 方法从容器中获取 bean 实例，只不过大部分的场景下，我们都用了依赖注入，所以很少手动调用` getBean(...)` 方法。

Spring AOP 的原理很简单，就是**动态代理**

代理模式很简单，接口 + 真实实现类 + 代理类，其中 真实实现类 和 代理类 都要实现接口，实例化的时候要使用代理类。所以，Spring AOP 需要做的是生成这么一个代理类，然后**替换掉**真实实现类来对外提供服务。

替换的过程怎么理解呢？在 Spring IOC 容器中非常容易实现，就是在 getBean(…) 的时候返回的实际上是代理类的实例，而这个代理类我们自己没写代码，它是 Spring 采用 JDK Proxy 或 CGLIB 动态生成的。

> getBean(…) 方法用于查找或实例化容器中的 bean，这也是为什么 Spring AOP 只能作用于 Spring 容器中的 bean 的原因，对于不是使用 IOC 容器管理的对象，Spring AOP 是无能为力的。

## IOC容器管理AOP实例

![1](https://www.javadoop.com/blogimages/spring-aop-source/1.png)

`DefaultAdvisorAutoProxyCreator `最后居然是一个 **BeanPostProcessor**

IOC容器在创建Bean(`doCreateBead`)的最后一步`initializeBean`里会调用`BeanPostProcessor`,

Spring AOP 会在 IOC 容器创建 bean 实例的最后对 bean 进行处理

```java
// 返回匹配当前 bean 的所有的 advisor、advice、interceptor
// 对于本文的例子，"userServiceImpl" 和 "OrderServiceImpl" 这两个 bean 创建过程中，
//   到这边的时候都会返回两个 advisor
Object[] specificInterceptors = getAdvicesAndAdvisorsForBean(bean.getClass(), beanName, null);

if (specificInterceptors != DO_NOT_PROXY) {
    this.advisedBeans.put(cacheKey, Boolean.TRUE);
    // 创建代理...创建代理...创建代理...
    Object proxy = createProxy(
        bean.getClass(), beanName, specificInterceptors, new SingletonTargetSource(bean));
    this.proxyTypes.put(cacheKey, proxy.getClass());
    return proxy;
}
```

`getAdvicesAndAdvisorsForBean(bean.getClass(), beanName, null)`，这个方法将得到所有的**可用于拦截当前 bean 的** advisor、advice、interceptor。

`createProxy(…)`

```java
// 注意看这个方法的几个参数，
//   第三个参数携带了所有的 advisors
//   第四个参数 targetSource 携带了真实实现的信息
protected Object createProxy(
      Class<?> beanClass, String beanName, Object[] specificInterceptors, TargetSource targetSource) {

   if (this.beanFactory instanceof ConfigurableListableBeanFactory) {
      AutoProxyUtils.exposeTargetClass((ConfigurableListableBeanFactory) this.beanFactory, beanName, beanClass);
   }

   // 创建 ProxyFactory 实例
   ProxyFactory proxyFactory = new ProxyFactory();
   proxyFactory.copyFrom(this);

   // 在 schema-based 的配置方式中，我们介绍过，如果希望使用 CGLIB 来代理接口，可以配置
   // proxy-target-class="true",这样不管有没有接口，都使用 CGLIB 来生成代理：
   //   <aop:config proxy-target-class="true">......</aop:config>
   if (!proxyFactory.isProxyTargetClass()) {
      if (shouldProxyTargetClass(beanClass, beanName)) {
         proxyFactory.setProxyTargetClass(true);
      }
      else {
         // 点进去稍微看一下代码就知道了，主要就两句：
         // 1. 有接口的，调用一次或多次：proxyFactory.addInterface(ifc);
         // 2. 没有接口的，调用：proxyFactory.setProxyTargetClass(true);
         evaluateProxyInterfaces(beanClass, proxyFactory);
      }
   }

   // 这个方法会返回匹配了当前 bean 的 advisors 数组
   // 对于本文的例子，"userServiceImpl" 和 "OrderServiceImpl" 到这边的时候都会返回两个 advisor
   // 注意：如果 specificInterceptors 中有 advice 和 interceptor，它们也会被包装成 advisor，进去看下源码就清楚了
   Advisor[] advisors = buildAdvisors(beanName, specificInterceptors);
   for (Advisor advisor : advisors) {
      proxyFactory.addAdvisor(advisor);
   }

   proxyFactory.setTargetSource(targetSource);
   customizeProxyFactory(proxyFactory);

   proxyFactory.setFrozen(this.freezeProxy);
   if (advisorsPreFiltered()) {
      proxyFactory.setPreFiltered(true);
   }

   return proxyFactory.getProxy(getProxyClassLoader());
}
```

这个方法主要是在内部创建了一个 ProxyFactory 的实例，然后 set 了一大堆内容，剩下的工作就都是这个 ProxyFactory 实例的了，通过这个实例来创建代理: `getProxy(classLoader)`。

### ProxyFactory 详解[

https://javadoop.com/post/spring-aop-source#toc_3

`InvocationHandler`

`invoke`

`JdkDynamicAopProxy implement InvocationHandler` : 就是在执行每个方法的时候，判断下该方法是否需要被一次或多次增强（执行一个或多个 advice）。



# 手撸Spring

# 容器篇: IOC

## 02-创建简单的Bean容器



## 03-实现 Bean 的定义\注册\获取

绕, 给我绕 !

![](https://i0.hdslb.com/bfs/album/212c3b8d6047ebc40f383f1e45d4d632f34c5d36.png@1e_1c.webp)

4. ### **抽象类定义模板方法(AbstractBeanFactory)**

`getBean`方法, 先看有没有存在的单例, 没有再`createBean`

```java
public abstract class AbstractBeanFactory extends DefaultSingletonBeanRegistry implements BeanFactory {

    @Override
    public Object getBean(String name) throws BeansException {
        Object bean = getSingleton(name);
        if (bean != null) {
            return bean;
        }

        BeanDefinition beanDefinition = getBeanDefinition(name);
        return createBean(name, beanDefinition);
    }

    protected abstract BeanDefinition getBeanDefinition(String beanName) throws BeansException;

    protected abstract Object createBean(String beanName, BeanDefinition beanDefinition) throws BeansException;

}
```

5. ### 实例化Bean类(AbstractAutowireCapableBeanFactory)

   实现`createBean`方法

   根据`BeanDefinition`里面存的`Class`类, 调用`newInstance()`实例化对象, 并`addSingleton`

   ```java
   public abstract class AbstractAutowireCapableBeanFactory extends AbstractBeanFactory {
   
       @Override
       protected Object createBean(String beanName, BeanDefinition beanDefinition) throws BeansException {
           Object bean = null;
           try {
               bean = beanDefinition.getBeanClass().newInstance();
           } catch (InstantiationException | IllegalAccessException e) {
               throw new BeansException("Instantiation of bean failed", e);
           }
   
           addSingleton(beanName, bean);
           return bean;
       }
   
   }
   ```

   6. ###  核心类实现(DefaultListableBeanFactory)

      存`beanDefinitionMap`

## 04-基于Cglib实现含构造函数的类实例化策略

坑: 上一章, 把实例化对象交给容器来统一处理. 但是, 如果实例对象有不同构造函数, 这样如何实例化?

`beanDefinition.getBeanClass().newInstance();`没有考虑入参

![](https://i0.hdslb.com/bfs/album/a204f9aee0797833516a5d86441cdfface93f0b5.png@1e_1c.webp)

- 参考 Spring Bean 容器源码的实现方式，在 BeanFactory 中添加 `Object getBean(String name, Object... args)` 接口，这样就可以在获取 Bean 时把构造函数的入参信息传递进去了。

- 另外一个核心的内容  创建含有构造函数的 Bean 对象

  一个是基于 JDK 本身自带的方法 `DeclaredConstructor`，

  另外一个是使用 Cglib 来动态创建 Bean 对象。*Cglib 是基于字节码框架 ASM 实现，所以你也可以直接通过 ASM 操作指令码来创建对象*

##  

![](https://i0.hdslb.com/bfs/album/50745662fd659139172b43e62f9fa557693f243c.png)



代码实现

1. 新增`getBean`接口, 支持多参数传入

   ```java
   @Override
   public Object getBean(String name, Object... args) throws BeansException {
       return doGetBean(name, args);
   }
   
   protected <T> T doGetBean(final String name, final Object[] args) {
       Object bean = getSingleton(name);
       if (bean != null) {
           return (T) bean;
       }
   
       BeanDefinition beanDefinition = getBeanDefinition(name);
       return (T) createBean(name, beanDefinition, args);
   }
   ```

2. ### 定义实例化策略接口

   `InstantiationStrategy`

   ```java
   public interface InstantiationStrategy {
   
       Object instantiate(BeanDefinition beanDefinition, String beanName, Constructor ctor, Object[] args) throws BeansException;
   
   }
   ```

3. JDK实例化

   ```java
   public class SimpleInstantiationStrategy implements InstantiationStrategy {
   
       @Override
       public Object instantiate(BeanDefinition beanDefinition, String beanName, Constructor ctor, Object[] args) throws BeansException {
           Class clazz = beanDefinition.getBeanClass();
           try {
               if (null != ctor) {
                   return clazz.getDeclaredConstructor(ctor.getParameterTypes()).newInstance(args);
               } else {
                   return clazz.getDeclaredConstructor().newInstance();
               }
           } catch (NoSuchMethodException | InstantiationException | IllegalAccessException | InvocationTargetException e) {
               throw new BeansException("Failed to instantiate [" + clazz.getName() + "]", e);
           }
       }
   }
   
   ```

   

4. Cglib实例化

   ```java
   public class CglibSubclassingInstantiationStrategy implements InstantiationStrategy {
   
       @Override
       public Object instantiate(BeanDefinition beanDefinition, String beanName, Constructor ctor, Object[] args) throws BeansException {
           Enhancer enhancer = new Enhancer();
           enhancer.setSuperclass(beanDefinition.getBeanClass());
           enhancer.setCallback(new NoOp() {
               @Override
               public int hashCode() {
                   return super.hashCode();
               }
           });
           if (null == ctor) return enhancer.create();
           return enhancer.create(ctor.getParameterTypes(), args);
       }
   }
   ```

## 为Bean对象注入属性和依赖Bean的功能实现

坑: bean创建时, 如果有类中包含属性那么在实例化的时候就需要把属性信息填充上，这样才是一个完整的对象创建

![](https://i0.hdslb.com/bfs/album/4572e953c6e61aef22011e6c00aa1a2cf6f16664.png)

- 属性填充要在类实例化创建之后，也就是需要在 `AbstractAutowireCapableBeanFactory` 的 createBean 方法中添加 `applyPropertyValues` 操作。
- 由于我们需要在创建Bean时候填充属性操作，那么就需要在 bean 定义 BeanDefinition 类中，添加 PropertyValues 信息。
- 另外是填充属性信息还包括了 Bean 的对象类型，也就是需要再定义一个 BeanReference，里面其实就是一个简单的 Bean 名称，在具体的实例化操作时进行递归创建和填充，与 Spring 源码实现一样。*Spring 源码中 BeanReference 是一个接口*

## 

结构

**![](https://i0.hdslb.com/bfs/album/b0ebb021605bbf1fb87bdbcbae698e99bbf6a21c.png)**



`Bean`属性填充

**我的理解**

`@Service`等一堆注解相当于注册`Bean`,

我们在注册的时候, 应该还需要给bean带上一堆它需要的参数, (类似于`@Around("aopPoint() && @annotation(dbRouter)")`这种

当我们需要使用使用bean的时候(`getBean`), 就需要对bean进行实例化了`createBean`

这里又包含**三步**: 

 1. `createBeanInstance(beanDefinition, beanName, args)`

    jkd/cglib 选择合适的 constructor, 实例化bean对象

 2. `applyPropertyValues(beanName, bean, beanDefinition);`

    注入之前就存在`beanDefinition`里的 `propertyValues`

    如果某个`value`是一个`Bean`实例, 那么我们还需要递归`getBean`, 先把 B 初始化, 属性依赖注入了, 再给我们的bean A的对应属性赋值

	3. 将初始化的`Bean`加入`Singleton` hashMap, 保证只有一个实例对象



```java
@Override
protected Object createBean(String beanName, BeanDefinition beanDefinition, Object[] args) throws BeansException {
    Object bean = null;
    try {
        bean = createBeanInstance(beanDefinition, beanName, args);
        // 给 Bean 填充属性
        applyPropertyValues(beanName, bean, beanDefinition);
    } catch (Exception e) {
        throw new BeansException("Instantiation of bean failed", e);
    }

    addSingleton(beanName, bean);
    return bean;
}

/**
* Bean 属性填充
*/
protected void applyPropertyValues(String beanName, Object bean, BeanDefinition beanDefinition) {
    try {
        PropertyValues propertyValues = beanDefinition.getPropertyValues();
        for (PropertyValue propertyValue : propertyValues.getPropertyValues()) {

            String name = propertyValue.getName();
            Object value = propertyValue.getValue();

            if (value instanceof BeanReference) {
                // A 依赖 B，获取 B 的实例化
                BeanReference beanReference = (BeanReference) value;
                value = getBean(beanReference.getBeanName());
            }
            // 属性填充
            BeanUtil.setFieldValue(bean, name, value);
        }
    } catch (Exception e) {
        throw new BeansException("Error setting property values：" + beanName);
    }
}
```



手动注册/注入属性

```java
// 1.初始化 BeanFactory
DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();

// 2. UserDao 注册
beanFactory.registerBeanDefinition("userDao", new BeanDefinition(UserDao.class));

// 3. UserService 设置属性[uId、userDao]
PropertyValues propertyValues = new PropertyValues();
propertyValues.addPropertyValue(new PropertyValue("uId", "10001"));
propertyValues.addPropertyValue(new PropertyValue("userDao",new BeanReference("userDao")));

// 4. UserService 注入bean
BeanDefinition beanDefinition = new BeanDefinition(UserService.class, propertyValues);
beanFactory.registerBeanDefinition("userService", beanDefinition);
```



## 06-资源加载器解析文件注册对象

不再手动注册spring,通过 Spring **配置文件**的方式将 Bean 对象实例化 (Spring 配置的**读取、解析、注册**Bean的操作)



![](https://i0.hdslb.com/bfs/album/26b6414233897e9db329462aa81d2156b0490493.png)



![](https://i0.hdslb.com/bfs/album/3093b3520e305877d1659ed0040d8e96f03c0105.png)

# 代理篇 AOP



## 12 基于JDK/Cglib实现AOP动态代理

反正肯定还要再看第二遍的

![](https://i0.hdslb.com/bfs/album/7348f0dceb0cc8d770c1491c44d2fa5b758d166c.png)



**JdkDynamicAopProxy**

分别实现了`AopProxy`和`InvocationHandler`两个接口, 

代理对象 getProxy 和反射调用方法 invoke 分开处理了

```java
public class JdkDynamicAopProxy implements AopProxy, InvocationHandler {

    private final AdvisedSupport advised;

    public JdkDynamicAopProxy(AdvisedSupport advised) {
        this.advised = advised;
    }

    @Override
    public Object getProxy() {
        return Proxy.newProxyInstance(Thread.currentThread().getContextClassLoader(), advised.getTargetSource().getTargetClass(), this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        if (advised.getMethodMatcher().matches(method, advised.getTargetSource().getTarget().getClass())) {
            MethodInterceptor methodInterceptor = advised.getMethodInterceptor();
            return methodInterceptor.invoke(new ReflectiveMethodInvocation(advised.getTargetSource().getTarget(), method, args));
        }
        return method.invoke(advised.getTargetSource().getTarget(), args);
    }

}

```

`getProxy `传入的handler参数是`this`, 因为子集也实现了`InvocationHandler`接口, 如果调用的话, 使用的就直接是`this`里的`invoke`方法

`invoke`

​	使用Support里提供的`targetSource`, `methodInterceptor.invoke`

​	`ReflectiveMethodInvocation`: 对入参信息包装, 目标对象、方法、入参  同时提供`proceed`方法: 

```java
@Override
public Object proceed() throws Throwable {
    return method.invoke(target, arguments);
}
```



**Cglib2AopProxy**

只需要继承`AopProxy`接口, 被代理类不用实现任何接口

拦截方法是通过`Enhancer#setCallback`, 没有`invoke`方法.



### 代理流程总结

## 13-AOP扩展到Bean的生命周期



![](https://i0.hdslb.com/bfs/album/1826c7e3ecce4ac85ba81fc049c8adf31b9ce61e.png)



* 让对象创建过程中，能把xml中配置的代理对象也就是切面的一些类对象实例化，就需要用到 BeanPostProcessor 提供的方法，因为这个类的中的方法可以分别作用与 Bean 对象执行初始化前后修改 Bean 的对象的扩展信息。但这里需要集合于 BeanPostProcessor 实现新的接口和实现类，这样才能定向获取对应的类信息。

* 在 AbstractAutowireCapableBeanFactory#createBean 优先完成 Bean 对象的判断，是否需要代理，有则直接返回代理对象。

* 提供一些 BeforeAdvice、AfterAdvice 的实现，让用户可以更简化的使用切面功能。除此之外还包括需要包装切面表达式以及拦截方法的整合，以及提供不同类型的代理方式的代理工厂，来包装我们的切面服务。

![](https://i0.hdslb.com/bfs/album/1327a77cf23cce436fd61af2ec382d6f9631583b.png)

`BeanPostProcessor`: 在bean真正实例化之前调用, 完成后返回的是已经注入切片的代理类, 之后创建实例, 再对代理类的任何方法调用都会被拦截

```java
public class DefaultAdvisorAutoProxyCreator implements InstantiationAwareBeanPostProcessor, BeanFactoryAware {

    private DefaultListableBeanFactory beanFactory;

    @Override
    public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
        this.beanFactory = (DefaultListableBeanFactory) beanFactory;
    }

    @Override
    public Object postProcessBeforeInstantiation(Class<?> beanClass, String beanName) throws BeansException {

        if (isInfrastructureClass(beanClass)) return null;

        Collection<AspectJExpressionPointcutAdvisor> advisors = beanFactory.getBeansOfType(AspectJExpressionPointcutAdvisor.class).values();

        for (AspectJExpressionPointcutAdvisor advisor : advisors) {
            ClassFilter classFilter = advisor.getPointcut().getClassFilter();
            if (!classFilter.matches(beanClass)) continue;

            AdvisedSupport advisedSupport = new AdvisedSupport();

            TargetSource targetSource = null;
            try {
                targetSource = new TargetSource(beanClass.getDeclaredConstructor().newInstance());
            } catch (Exception e) {
                e.printStackTrace();
            }
            advisedSupport.setTargetSource(targetSource);
            advisedSupport.setMethodInterceptor((MethodInterceptor) advisor.getAdvice());
            advisedSupport.setMethodMatcher(advisor.getPointcut().getMethodMatcher());
            advisedSupport.setProxyTargetClass(false);

            return new ProxyFactory(advisedSupport).getProxy();

        }

        return null;
    }

    private boolean isInfrastructureClass(Class<?> beanClass) {
        return Advice.class.isAssignableFrom(beanClass) || Pointcut.class.isAssignableFrom(beanClass) || Advisor.class.isAssignableFrom(beanClass);
    }

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }
    
}
```

