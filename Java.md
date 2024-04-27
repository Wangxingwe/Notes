# 一：Java

## 1、Java基础

## 2、Java高级

### 2.1、多线程与并发

### 2.2、lambda

lambda的本质是<mark>行为参数化</mark>。这种方式类似于<mark>策略设计模式</mark>。

**函数式接口**

1. 只有一个抽象方法的接口被称为函数式接口。（可以有多个默认方法，但是只能有一个抽象方法）

2. 新的Java API中函数式接口会有@FunctionalInterface的标注。

3. 任何函数式接口都不允许抛出受检异常（checked exception）。如果你需要Lambda
   
   表达式来抛出异常，有两种办法：定义一个自己的函数式接口，并声明受检异常，或者把Lambda包在一个try/catch块中。
   
   ```java
   //自己定义函数式接口抛异常
   @FunctionalInterface 
   public interface BufferedReaderProcessor { 
    String process(BufferedReader b) throws IOException; 
   }
   //lambda包在try/catch中
   Function<BufferedReader, String> f = (BufferedReader b) -> { 
    try { 
        return b.readLine(); 
    } catch(IOException e) { 
        throw new RuntimeException(e); 
    } 
   };
   ```

4. 同一个Lambda表达式就可以与不同的函数式接口联系起来，只要它
   
   们的抽象方法签名能够兼容。

5. Lambda表达式也允许使用自由变量（不是参数，而是在外层作用域中定义的变量），就像匿名类一样。 它们被称作捕获Lambda。
   
   ```java
   int portNumber = 1337; 
   Runnable r = () -> System.out.println(portNumber);
   ```

6. 

# 二：Sping6用到的技术

## 1、IOC和DI

## 2、FactoryBean和BeanFactory

## 3、设计模式

### 3.1、简单工厂模式（不是23种模式之一）

![](/Users/xingweiwang/Library/Application%20Support/marktext/images/2024-02-25-10-14-19-image.png)

**缺点：** 1、不符合ocp原则。

             2、有一个全能类，一旦瘫痪系统就瘫痪了。

**优点：** 1、生产与消费分离。

### 3.2、工厂方法模式

![](/Users/xingweiwang/Library/Application%20Support/marktext/images/2024-02-25-10-21-43-image.png)

**缺点：** 1、每增加一个产品都要增加一个对应工厂，会有类爆炸的问题。

**优点：** 1、拓展性高。

### 3.3、抽象方法工厂模式

![](/Users/xingweiwang/Library/Application%20Support/marktext/images/2024-02-25-10-15-29-image.png)

**缺点：** 1、产品族扩展⾮常困难，要增加⼀个系列的某⼀产品，既要在AbstractFactory⾥加代码，⼜要在具体的⾥⾯加代码。

**优点：** 1、当⼀个产品族中的多个对象被设计成⼀起⼯作时，它能保证客户端始终只使⽤同⼀个产品族中的对象。

## 4、Bean的生命周期

### 4.1、bean五步生命周期

实例化bean-->bean属性赋值-->初始化bean->使用bean-->销毁bean

1. Java通过默认通过无参构造方法进行对象的实例化即：new XXX()，因为实例化只是创建了对象所以需要的是无参构造方法。

### 4.2、bean的实例化方式

- 第⼀种：通过构造⽅法实例化

- 第⼆种：通过简单⼯⼚模式实例化（简单工厂包括静态方法）

- 第三种：通过factory-bean实例化（工厂方法）

- 第四种：通过FactoryBean接⼝实例化（实现FactoryBean接口）

### 4.2、FactoryBean和BeanFactory的区别

**<mark>BeanFactory</mark>**

spring IOC的顶级对象，是负责创建Bean对象的工厂类。最初使用XML文件配置的时候，Application Context就继承了BeanFactory的子类（ListableBeanFactory）。到如今使用的@Bean注解实际上也是使用的BeanFactory来进行Bean的实例化。

 **<mark>FactoryBean</mark>**

实际上就是一个Bean，能够辅助spring进行实例化其他Bean的一个Bean。常见的 FactoryBean包含：ProxyFactoryBean、JdbcTemplate、RestTemplate。

## 5、Bean循环依赖问题

### 5.1、三级缓存解决循环依赖

Spring使用三级缓存来解决循环依赖的问题。三级缓存包括singletonObjects、earlySingletonObjects和singletonFactories。

1. singletonObjects：存储完全初始化的单例Bean实例。                   一级缓存

2. earlySingletonObjects：存储尚未完全初始化的单例Bean实例。  二级缓存

3. singletonFactories：存储用于创建单例Bean的ObjectFactory。   三级缓存
   
   当解决循环依赖时，Spring会将正在创建的Bean实例放入earlySingletonObjects缓存中，并将其ObjectFactory放入singletonFactories缓存中。然后，Spring会继续创建其他Bean，并尝试解决它们之间的依赖关系。当所有Bean都创建完成后，Spring会回到之前放入缓存的Bean实例，完成它们的初始化过程。

通过使用三级缓存，Spring能够在运行时动态地解决循环依赖，确保所有相关的Bean都能正确地初始化，并且不会导致死锁或无限递归等问题。

<mark>Spring的三级缓存解决的是singleton+set注入的问题</mark>

<mark>Spring的三级缓存无法解决singleton+构造注入</mark>

```textile
根本的原因在于：这种⽅式可以做到将“实例化Bean”和“给Bean属性赋值”这两个动作分开去完成。
实例化Bean的时候：调⽤⽆参数构造⽅法来完成。此时可以先不给属性赋值，可以提前将该Bean对
象“曝光”给外界。
给Bean属性赋值的时候：调⽤setter⽅法来完成。
```

## 6、spring Ioc注解开发

### 6.1、注解的原理

1. 注解实现原理：反射。

### 6.2、注解的使用

1. spring使用注解开发也要指定bean对象所在的包路径。

2. @Component(value="beanId")<==>  <bean id="beanId" class="类路径"/>

3. 在这个spring配置的XML文件中要使用<mark>context命名空间</mark>。
   
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
   
       <!--这个包路径的指定和使用@ComponentScan具有相同作用。
           有多个包的解决方式：1、使用逗号分隔即可。2、指定多个包的共同父包。-->
       <context:component-scan base-package="com.we.annotation.bean"/>
   
   </beans>
   ```

4. 选择性实例化Bean：
   
   ```xml
   <?xml version="1.0" encodeing="UFT-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
   
       <!--use-default-filters="false"表示不使用spring默认实例化规则-->
       <context:component-scan base-package="包路径" use-default-filters="false">
           <!--表示只对controller注解的对象进行实例化-->
           <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
       </context:component-scan>    
   
   </beans>
   ```

### 6.3、负责注入的注解

1. @Value-简单类型的注入 

2. @Autowired-非简单类型注入，默认【byType】。**使用范围：**属性上，构造方法上，构造方法的参数上，setter方法上。如果要根据名称注入，需要配合@Qualifier注解一起使用。

3. @Resource-非简单类型的注入默认【byName】，未指定name时，使用属性名作为name。通过name找不到的话会自动启动【byTtype】注入。**使用范围：** 属性上和setter方法上。

4. @Autowired和@Qualifier注解与@Resource注解的区别与联系
   
   **<mark>共同点：</mark>**
   
   都可以完成非简单类型的注入
   
   **<mark>不同点：</mark>**
   
   @Resource是JDK扩展包的，属于标准注解更加具有通用型，而@Autowired注解是spring框架的。

### 6.4、全注解式开发

全注解开发就是不在使用spring配置文件了，写一个配置类来代替配置文件。

```java
package com.we;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

/**
 * @ClassName: Spring6Configuration
 * @Auther: wangxingwe
 * @Description: 配置类代替配置文件，配置类代替了spring.xml配置文件
 * @Date: 2023/12/23 21:34
 * @Version: V1.0
 */

@Configuration
@ComponentScan({"包路径","包路径","...","..."...})
public class Spring6Configuration {
}
```

## 7、JdbcTemplate

jdbctemplate是spring提供的一个JDBC模版类，是对JDBC的封装简化JDBC代码。

## 8、GoF之代理模式

代理模式要有：目标类和代理类

代理模式在代码实现上包括两种形式：

- 静态代理

- 动态代理

### 8.1、静态代理

<mark>静态代理为什么被称为静态？</mark>

静态代理之所以称为静态，是因为代理类在编译时期就已经存在，代理关系在程序运行之前就已经确定，而无法在运行时动态地改变。

1. 接口类。
2. 具体用户管理实现类。
3. 代理类(业务增强类)
4. 客户端调用。

代理类和目标类实现共同的接口，并进行实现，在代理类进行需求扩展，将目标类通过构造方法注入到代理类，在代理类中调用目标类。

//静态代理：通过静态代理实现统计各个方法实现所耗时间

<mark>代理接口</mark> jdk动态代理也使用这个接口

```java
package com.we.proxy.staticclass;

/**
 * @ClassName: OrderService
 * @Auther: wangxingwe
 * @Description: 代理接口
 * @Date: 2023/12/24 09:41
 * @Version: V1.0
 */

public interface OrderService {

    void generate();

    void detail();

    void modify();
}
```

<mark>接口实现类</mark> jdk动态代理也使用这个接口

```java
package com.we.proxy.staticclass;

/**
 * @ClassName: OrderServiceProxy
 * @Auther: wangxingwe
 * @Description: 订单服务的代理类
 * @Date: 2023/12/24 09:53
 * @Version: V1.0
 */

public class OrderServiceProxy implements OrderService{

    private OrderService orderService;

    //构造方法注入
    public OrderServiceProxy(OrderService orderService) {
        this.orderService = orderService;
    }

    @Override
    public void generate() {
        long begin = System.currentTimeMillis();
        orderService.generate();
        long end = System.currentTimeMillis();
        System.out.println("generate:"+(end-begin));
    }

    @Override
    public void detail() {
        long begin = System.currentTimeMillis();
        orderService.detail();
        long end = System.currentTimeMillis();
        System.out.println("detail:"+(end-begin));
    }

    @Override
    public void modify() {
        long begin = System.currentTimeMillis();
        orderService.modify();
        long end = System.currentTimeMillis();
        System.out.println("modify:"+(end-begin));
    }
}
```

```java
package com.we.proxy.staticclass;

/**
 * @ClassName: Client
 * @Auther: wangxingwe
 * @Description: 测试静态代理的客户端程序
 * @Date: 2023/12/24 09:56
 * @Version: V1.0
 */

/**
 *OrderService-->接口
 *OrderServiceImpl-->目标类
 *OrderServiceProxy-->代理类
 */

public class Client {
    public static void main(String[] args) {
        //创建目标对象
        OrderService target = new OrderServiceImpl();
        //创建代理对象
        OrderService proxy = new OrderServiceProxy(target);
        //调用代理对象的代理方法
        proxy.generate();
        proxy.detail();
        proxy.modify();
    }
}
```

<mark>静态代理的缺点：</mark>

一个接口对应一个代理类，导致类爆炸。优化方向--><mark>动态代理</mark>

关联考虑：设计模式-->工厂方法模式，同样是增加一个产品种类增加一个工厂类，同样导致了类爆炸。不同点是，工厂方法是创建型设计模式，解决对象创建问题；静态代理是结构型设计模式，用于控制对真实对象的访问。

### 8.2、动态代理⭐️

1. 接口类。
2. 代理类(业务增强类)
3. 客户端调用。

在程序运行阶段，在内存中动态生成代理类，别称为动态代理，目的是为了减少代理类的数量

![](/Users/xingweiwang/Library/Application%20Support/marktext/images/2024-03-02-18-40-25-image.png)

#### 8.2.1、JDK动态代理

特点：<mark>只能代理接口</mark>

```java
package com.we.proxy.dynamicclass.jdkproxy;


import java.lang.reflect.Proxy;

/**
 * @ClassName: Client
 * @Auther: wangxingwe
 * @Description: jdk动态代理的客户端访问类
 * @Date: 2023/12/24 22:36
 * @Version: V1.0
 */

public class Client {
    public static void main(String[] args) {
        //创建目标对象
        OrderService target = new OrderServiceImpl();
        //创建代理对象-->在内存中生成了代理类的字节码，创建了代理对象。
        OrderService orderServiceProxy =
                (OrderService) Proxy.newProxyInstance( target.getClass().getClassLoader(),
                                        target.getClass().getInterfaces(),
                                        new TimerInvocationHandler(target));

        //调用代理对象的代理方法
        orderServiceProxy.generate();
        orderServiceProxy.detail();
        orderServiceProxy.modify();
    }
}
```

- 第一个参数：类加载器。在内存中生成了字节码，要想执行这个字节码，也是需要先把这个字节码加载到内存当中的。所以要指定使用哪个类加载器加载

- 第二个参数：接口类型。代理类和目标类实现相同的接口，所以要通过这个参数告诉JDK动态代理**生成的类要实现哪些接口。**

- 第三个参数：调用处理器。这是一个JDK动态代理规定的接口，接口全名：

java.lang.reflect.InvocationHandler。显然这是一个回调接口，也就是说调用这个接口中方法的程序已经写好了，就差这个接口的实现类了。

<mark>实现InvocationHandler接口。</mark>

```java
package com.we.proxy.dynamicclass.jdkproxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

/**
 * @ClassName: TimerInvocationHandler
 * @Auther: wangxingwe
 * @Description: 调用处理器
 * @Date: 2023/12/24 22:43
 * @Version: V1.0
 */

public class TimerInvocationHandler implements InvocationHandler {

    //目标对象
    private Object target;

    //通过构造方法来传送目标对象
    public TimerInvocationHandler(Object target){
        this.target=target;
    }

    /*
        1. 为什么强行要求你必须实现InvocationHandler接口？
            因为一个类实现接口就必须实现接口中的方法。
            以下这个方法必须是invoke()，因为JDK在底层调用invoke()方法的程序已经提前写好了。
            注意：invoke方法不是我们程序员负责调用的，是JDK负责调用的。
        2. invoke方法什么时候被调用呢？
            当代理对象调用代理方法的时候，注册在InvocationHandler调用处理器当中的invoke()方法被调用。

        3. invoke方法的三个参数：
            invoke方法是JDK负责调用的，所以JDK调用这个方法的时候会自动给我们传过来这三个参数。
            我们可以在invoke方法的大括号中直接使用。
            第一个参数：Object proxy 代理对象的引用。这个参数使用较少。
            第二个参数：Method method 目标对象上的目标方法。（要执行的目标方法就是它。）
            第三个参数：Object[] args 目标方法上的实参。

            invoke方法执行过程中，使用method来调用目标对象的目标方法。
    */

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //目标执行前的增强
        long begin = System.currentTimeMillis();
        //调用目标对象的目标方法
        Object retValue = method.invoke(target,args);
        //目标执行后的增强
        long end = System.currentTimeMillis();
        System.out.println("耗时："+(end - begin));
        //一定要返回-返回值是代理对象的有返回类型时使用，不返回目标对象无法获取返回值信息。
        return retValue;
    }
}
```

#### 8.2.2、CGLIB动态代理

特点：CGLIB（code generation library）是一个开源项目。既可以代理接口，又可以代理类，<mark>底层是通过继承的方式实现的</mark>。性能比JDK动态代理好。

```java
package com.we.proxy.dynamicclass.cglibproxy;

/**
 * @ClassName: UserService
 * @Auther: wangxingwe
 * @Description:
 * @Date: 2023/12/26 07:56
 * @Version: V1.0
 */

public class UserService {
    public void login() throws InterruptedException {
        Thread.sleep(1000);
        System.out.println("sign in...");
    }

    public void logout() throws InterruptedException {
        Thread.sleep(1000);
        System.out.println("sign out...");
    }

}
```

<mark>cglib拦截器</mark>

```java
package com.we.proxy.dynamicclass.cglibproxy;

import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

/**
 * @ClassName: TimerMethodInterceptor
 * @Auther: wangxingwe
 * @Description: cglib拦截器
 * @Date: 2023/12/26 08:04
 * @Version: V1.0
 */

public class TimerMethodInterceptor implements MethodInterceptor {


    @Override
    public Object intercept(Object target, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        long begin = System.currentTimeMillis();
        Object retValue = methodProxy.invokeSuper(target,objects);
        long end = System.currentTimeMillis();
        System.out.println("耗时："+(end-begin));
        //一定要返回-返回值是代理对象的有返回类型时使用，不返回目标对象无法获取返回值信息。 
        return retValue;
    }
}
```

<mark>客户端程序</mark>

```java
package com.we.proxy.dynamicclass.cglibproxy;

import net.sf.cglib.proxy.Enhancer;

/**
 * @ClassName: Client
 * @Auther: wangxingwe
 * @Description: cglib客户端
 * @Date: 2023/12/26 07:57
 * @Version: V1.0
 */

public class Client {
    public static void main(String[] args) throws InterruptedException {
        //创建字节码增强器
        Enhancer enhancer = new Enhancer();
        //告诉cglib要继承的类
        enhancer.setSuperclass(UserService.class);
        //设置回掉接口
        enhancer.setCallback(new TimerMethodInterceptor());
        //生成源码，编译class，加载到jvm，并创建代理对象。
        UserService userServiceProxy = (UserService) enhancer.create();

        userServiceProxy.login();
        userServiceProxy.logout();
    }
}
```

## 9、面向切面编程AOP

AOP底层基于动态代理实现。

Spring的AOP使用的动态代理是：JDK动态代理+CGLIB动态代理技术。Spring在这两种动态代理中灵活切换，如果是代理接口，会默认使用JDK动态代理，如果要代理某个类，这个类没有实现接口，就会切换使用CGLIB。当然，你也可以强制通过一些配置让Spring只使用CGLIB。

![](/Users/xingweiwang/Library/Application%20Support/marktext/images/2023-12-26-21-51-46-image.png)

用一句话总结AOP：将与核心业务无关的代码独立的抽取出来，形成一个独立的组件，然后以横向交叉的方式应用到业务流程当中的过程被称为AOP。

<mark>AOP优点</mark>

第一：代码复用性增强。

第二：代码易维护。

第三：使开发者更关注业务逻辑。

<mark>spring的七大术语</mark>

## 10、spring对事务的支持

### 10.1、编程式事务

通过编写代码的方式来实现事务的管理

spring中的事务底层是基于AOP实现的。采用AOP的方式进行了封装。所以spring专门针对事务开发了一套API，API的核心接口如下：

![](/Users/xingweiwang/Library/Application%20Support/marktext/images/2024-03-09-15-06-53-image.png)

<mark>代码示例：</mark>

```java
@Service
public class UserService {
    private final TransactionTemplate transactionTemplate;

    @Autowired
    public UserService(PlatformTransactionManager transactionManager) {
        this.transactionTemplate = new TransactionTemplate(transactionManager);
    }

    public void createUser(User user) {
        transactionTemplate.execute(new TransactionCallbackWithoutResult() {
            @Override
            protected void doInTransactionWithoutResult(TransactionStatus status) {
                // 在事务中执行的逻辑
                // ...
                userRepository.save(user);
            }
        });
    }
}
```

### 10.2、声明式事务

1、基于注解-@Transactional注解

@Transactional注解的详细属性

```java
package org.springframework.transaction.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.core.annotation.AliasFor;

@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Transactional {
    @AliasFor("transactionManager")
    String value() default "";

    @AliasFor("value")
    String transactionManager() default "";

    String[] label() default {};

    Propagation propagation() default Propagation.REQUIRED;

    Isolation isolation() default Isolation.DEFAULT;

    int timeout() default -1;

    String timeoutString() default "";

    boolean readOnly() default false;

    Class<? extends Throwable>[] rollbackFor() default {};

    String[] rollbackForClassName() default {};

    Class<? extends Throwable>[] noRollbackFor() default {};

    String[] noRollbackForClassName() default {};
}
```

2、基于XML配置方式

### 10.3、事务中的重点属性

- 事务传播行为⭐️

- 事务隔离级别⭐️

- 事务超时

- 只读事务

- 设置出现哪些异常回滚事务

- 设置出现哪些异常不会滚事务

#### 10.3.1、事务传播行为

1. REQUIRED：支持当前事务，如果不存在就新建一个(默认)【没有就新建，有就加入】

2. REQUIRES_NEW：开启一个新的事务，如果一个事务已经存在，则将这个存在的事务挂起【不管有没有，直接开启一个新事务，开启的新事务和之前的事务不存在嵌套关系，之前事务被挂起】

3. SUPPORTS：支持当前事务，如果当前没有事务，就以非事务方式执行【有就加入，没有就不管了】

4. NOT_SUPPORTED：以非事务方式运行，如果有事务存在，挂起当前事务【不支持事务，存在就挂起】

5. MANDATORY：必须运行在一个事务中，如果当前没有事务正在发生，将抛出一个异常【有就加入，没有就抛异常】

6. NEVER：以非事务方式运行，如果有事务存在，抛出异常【不支持事务，存在就抛异常】

7. NESTED：如果当前正有一个事务在进行中，则该方法应当运行在一个嵌套式事务中。被嵌套的事务可以独立于外层事务进行提交或回滚。如果外层事务不存在，行为就像REQUIRED一样。【有事务的话，就在这个事务里再嵌套一个完全独立的事务，嵌套的事务可以独立的提交和回滚。没有事务就和REQUIRED一样。】

<mark>REQUIRED和NESTED的异同</mark>

`PROPAGATION_REQUIRED`会加入已存在的事务，共享同一个事务，当外部事物会滚，内部事务也会会滚。而`PROPAGATION_NESTED`会在嵌套的事务中执行，嵌套事务可以独立进行提交或回滚。

#### 10.3.2、事务隔离级别

**读未提交：READ_UNCOMMITTED**

这种隔离级别，存在脏读问题，所谓的脏读(dirty read)表示能够读取到其它事务未提交的数据。

**读提交：READ_COMMITTED**

解决了脏读问题，其它事务提交之后才能读到，但存在不可重复读问题。

**可重复读：REPEATABLE_READ**

解决了不可重复读，可以达到可重复读效果，只要当前事务不结束，读取到的数据一直都是一样的。但存在幻读问题。

**序列化：SERIALIZABLE**

决了幻读问题，事务排队执行。不支持并发。

![](/Users/xingweiwang/Library/Application%20Support/marktext/images/2023-12-26-22-17-23-image.png)

# 三、SpringMVC

概览：

![](/Users/xingweiwang/Library/Application%20Support/marktext/images/2024-02-25-11-26-57-image.png)

1、 用户发送请求至前端控制器DispatcherServlet。

2、 DispatcherServlet收到请求调用HandlerMapping处理器映射器。

3、 处理器映射器找到具体的处理器(可以根据xml配置、注解进行查找)，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。

4、 DispatcherServlet调用HandlerAdapter处理器适配器。

5、 HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)。

6、 Controller执行完成返回ModelAndView。

7、 HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet。

8、 DispatcherServlet将ModelAndView传给ViewReslover视图解析器。

9、 ViewReslover解析后返回具体View。

10、DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）。

11、 DispatcherServlet响应用户。

# 四、Mybatis

# 五、多线程和并发
