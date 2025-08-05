

# SSM框架

## maven介绍

maven是一个软件，掌握软件安装、配置、以及基本功能（项目构建、依赖管理）使用

#### maven主要功能

#### 1.依赖管理

能够帮助开发人员自动解决软件包依赖问题，重点：编写pom.xml文件

```
<!--  第三方依赖信息声明-->
<!--  <dependencies>依赖信息的集合
              <dependence>代表每一个依赖
                    gav -依赖的信息就是其它maven工程（jar）
      第三方依赖信息：
        1.maven提供的查询官网 http://mvnrepository.com
        2.maven插件：maven-search
-->
```

**依赖传递**

导入依赖，自动导入依赖的依赖，减少是重复依赖，总动管理依赖，保证依赖版本无冲突

**依赖冲突**

当发生重复依赖传递导入会终止传递依赖，避免重复依赖和循环依赖

解决原则：

​	·谁短谁优先！引用路径长度；

​	·谁上谁优先！dependencies声明的先后顺序

#### 2.构建管理

指将源代码、依赖库和资源文件等转换成可执行或可部署的应用程序的过程，在这个过程中包括编译源代码，链接依赖库，打包和部署等多个步骤。

清理---->编译----> 测试 ---->报告 ---->打包----> 部署

**主动触发过程**

- 重新编译：编译不充分，部分文件没有被编译
- 打包：独立部署到外部服务器软件，打包部署
- 部署本地或者私服仓库：maven工程加入到本地或私服仓库，供其他工程使用

**命令方式构建**（命令触发**插件**，干活的是插件）

语法：mvn构建命令 构建命令...

mvn clean:清理编译或打包后的文件结构，删除target文件夹

mvn compile：编译项目，生成target文件

mvn test：执行测试源码（测试）

mvn package：打包项目，生成war/jar文件

mvn install：打包上传到maven本地仓库（本地部署）

mvn deploy：只打包，上传到maven私服仓库（私服部署）



注意事项：

1.命令执行需要我们进入到项目的根路径 pom.xml平级

2.部署 必须是jar包形式

**可视化构建**：

![image-20250710151858701](C:\Users\32429\AppData\Roaming\Typora\typora-user-images\image-20250710151858701.png)

**构建命令周期**：可以理解成是一组固定构建命令的有序集合，触发周期后的命令，会自动触发通过同一周期前的命令，简化触发构建命令过程。

- 清理周期：主要对项目编译生成文件进行清理

​	包含命令：clean

- 默认周期：定义了真正构建时所需要执行的所有步骤，它是生命周期中最核心的部分

  包含命令： compile	test	package	install/deploy	

- 报告周期：

  包含命令：site

  打包：mvn clean package 	本地仓库：mvn clean install

**命令、周期、插件**

周期内包含一个或多个命令，命令触发若干插件，最终构建的是插件

#### 3.maven继承特性

maven项目中，让让一个项目从另一个项目中**继承配置信息**的机制。继承可以让我们在多个项目中共享同一配置属性，简化项目的管理和维护工作。

作用：在父工程中统一管理项目中的依赖信息，进行统一版本管理

**父工程不引入依赖，只做依赖版本的声明**，<dependenciesManagement> <denpendecy><gav>, 保证项目使用规范，准确jar包

```
<!--    声明版本信息-->
<!--    导入依赖，此处导入所有子工程都有的相应依赖-->
    <dependencies></dependencies>
<!--    声明依赖，不会下载依赖，可以被子工程继承版本号-->
    <dependencyManagement></dependencyManagement>
```



#### 4.maven聚合特性

指将多个项目组织到一个父级项目中，通过触发父工程的**构建**，统一按顺序触发子工程构建的过程。

```
<!--    要统一管理哪些子工程的artifactId-->
    <modules>
        <module>shop-user</module>
        <module>shop-order</module>
    </modules>
```

## SpringFramework

### 基于xml配置文件管理组件

#### 1.spring-ioc容器和核心概念

##### 组件和组件管理

整个项目都是由各种组件搭建而成的

请求--------->Servlet---------------->Service------------>Dao------------>数据库

​		控制层组件	   业务逻辑层组件	持久化层组件

Spring充当组件管理角色（Ioc），组件完全交给Spring框架进行管理，Spring代替了程序员原有的new对象和对象属性赋值动作等

Spring具体管理动作包括：

- 组件对象实例化
- 组件属性赋值
- 组件对象之间引用
- 组件对象存活周期管理
- .......

我们只需要编写元数据（配置文件）告知Spring管理哪些类组件和他们的关系

注意：组件是映射到应用程序中所有可重用组件的Java对象，应该是可复用的功能对象

- 组件一定是对象
- 对象不一定是组件

综上：Spring充当一个组件容器，创建、管理、存储组件。

##### 组件交给Spring管理作用

- 降低组件之间解耦合
- 提高代码的可复用性和可维护性
- 方便配置和管理
- **交给Spring管理的对象（组件），方可享受Spring框架的其它功能（AOP，声明事务管理）等。**

#### 2.Sping Ioc容器和容器实现

##### 2.1 普通和复杂容器

- 普通容器：数组，集合List、Set
- 复杂容器：Servlet 能够管理Servlet（init、service、destrory）、Filter、Listener这样的组件的一生，所以它是一个复杂容器

总结：Spring管理组件的容器，就是一个复杂容器，不仅储存组件，还可以管理组件之间的依赖关系，并且创建和销毁组件

##### 2.2 SpringIoc容器介绍

SpringIoc容器，负责示例化、配置和组装bean（组件）。容器通过读取元数据来获取有关要实例化、配置和组装组件的指令。

配置元数据以XML、Java注解或Java代码形式表现。

##### 2.3 SpringIoc具体接口和实现类

`BeanFactory`提供了配置框架和基本功能，`ApplicatonContext`添加了更多特定于企业的功能。`ApplicatonContext`是`BeanFactory`

的子接口。

`ApplicationContext`容器实现类：

ClassPathXmlApplicationContext：读取类路径下的XML格式的配置文件创建IOC容器对象

FileSystemXmlApplicationContext：读取文件系统路径下的XML格式的配置文件创建IOC容器对象

AnnotationConfigApplicationContext:通过读取**Java配置类**创建IOC容器对象

WebApplicationContext：基于Web环境创建IOC容器对象，并将对象引入存入ServletContext

**配置类+注解方式**

#### 3.SpringIoC/DI概念总结

##### Ioc（Inversion of Control）控制翻转

主要针对对象的创建和调用控制而言 ，Ioc容器维护着应用程序的对象，并负责创建这些对象

- **基于构造函数的配置**

通过`<constructor-arg>`标签注入依赖：

```xml
<bean id="userService" class="com.example.UserServiceImpl">
    <constructor-arg ref="userRepository"/> <!-- 引用其他Bean -->
    <constructor-arg value="defaultRole"/>  <!-- 字面量值 -->
</bean>
```

- **基于 Setter 方法的配置**

通过`<property>`标签调用 Setter 方法：

```xml
<bean id="dataSource" class="com.zaxxer.hikari.HikariDataSource">
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/mydb"/>
    <property name="username" value="root"/>
</bean>
```

- **基于工厂方法的配置**

通过静态工厂或实例工厂创建 Bean：

​	适用于需要复杂初始化逻辑或第三方库类

```xml
<!-- 静态工厂方法 -->
<bean id="vehicle" class="com.example.VehicleFactory" factory-method="createCar"/>

<!-- 实例工厂方法 -->
<bean id="factory" class="com.example.VehicleFactory"/>
<bean id="car" factory-bean="factory" factory-method="createCar"/>
```



##### DI（Dependence Injection）依赖注入

组件之间依赖传递关系的过程中，将依赖关系在容器内部进行处理，在Spring中，DI通过XML配置文件或注解的方式实现的，它提供了三种形式的依赖注入：构造函数注入、Setter方式注入和接口注入

- **构造函数注入**

​	**核心思想**：通过构造函数传递依赖对象，确保对象创建时就已经获得所有必要依赖

```java
public class UserService {
    private final UserRepository userRepository; // 依赖对象

    // 构造函数注入
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

- **Setter方式注入**

​	**核心思想**：通过公共 Setter 方法传递依赖对象，允许对象在创建后动态修改依赖。

```java
public class UserService {
    private UserRepository userRepository; // 依赖对象

    // Setter方法注入
    public void setUserRepository(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

#### 4.Ioc基本创建和使用

- 创建Ioc容器

  方式1：直接创建容器并指定配置文件【推荐】

  方式2：先创建ioc容器，再指定配置文件，再刷新

- 在Ioc容器中读取组件Bean

  方法1：直接通过BeanId获取 ，返回值类型为Object 需要强制类型转化【不推荐】

  方法2：根据BeanId，同时指定bean的类型 class

  方法3：直接根据类型获取

#### 5.扩展

##### 5.1 组件周期方法

通过`init-method`和`destroy-method`指定初始化和销毁方法

init-method = "方法名"；

destroy-method = "方法名"；

```xml
<bean id="dataSource" class="com.zaxxer.hikari.HikariDataSource"
      init-method="start" destroy-method="close">
    <!-- 配置属性 -->
</bean>
```

##### 5.2 作用域配置

在Ioc容器中，<bean>标签对应的信息转成Spring内部BeanDefinition对象，在BeanDefinition对象内，包含定义的信息（id，class，属性等）

这意味着，BeanDefinition与类的概念一样，SpringIoc容器可以根据BeanDefinition对象反射创建多个Bean对象实例（由Bean的作用域Scope属性指定）

- singleton（单例，默认）每个 Spring 容器中仅创建一个实例，所有请求都共享该实例。
- prototype（原型）作用：每次请求 Bean 时都会创建一个新实例。

##### 5.3 factoryBean使用

除了使用构造函数实例化对象，还有一种通过工厂模式实例化对象，factoryBean就是简化这种方法的，是一个接口。

`FactoryBean`用于配置复杂的Bean对象，可以创建过程存储在`FactoryBean`的getObject方法

`FactoryBean<T>`接口提供三种方法：

`T getObject()`:返回此工厂创建的对象的实例，该返回值会被存储到IoC容器

`boolean isSingleton()`:判断是否返回单例

`Class<?> getObjectType()`:返回getObject()方法返回的对象类型，如果实现不知道，返回null

### 基于注解方式管理Bean

#### 1.Bean注解标记和扫描

`@Service(value="唯一标识")`默认id为类名（首字母小写）

| `@Component`      | 通用组件              | 普通工具类   |
| ----------------- | --------------------- | ------------ |
| `@Service`        | 业务逻辑层（Service） | 用户服务类   |
| `@Repository`     | 数据访问层（DAO）     | 数据库操作类 |
| `@Controller`     | Web 控制器            | MVC 控制器   |
| `@RestController` | RESTful API 控制器    | REST 接口    |

```xml
<!-- 普通配置包扫描，base-package 指定 IoC 容器去哪些包下查找注解类 -->
<context:component-scan base-package="com.example.ioc"/>
```

```xml
<context:component-scan base-package="com.example.service" use-default-filters="false">
    <!-- 只包含带有 @Service 的类 -->
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Service"/>
    
    <!-- 同时包含带有自定义 @ApiService 注解的类 -->
    <context:include-filter type="annotation" expression="com.example.annotation.ApiService"/>
</context:component-scan>
```

```xml
<context:component-scan base-package="com.example.service">
    <!-- 排除所有带有 @Deprecated 的类 -->
    <context:exclude-filter type="annotation" expression="java.lang.Deprecated"/>
    
    <!-- 排除所有带有自定义 @Internal 注解的类 -->
    <context:exclude-filter type="annotation" expression="com.example.annotation.Internal"/>
</context:component-scan>
```

##### 核心的 XML 配置元素

- **`<context:property-placeholder>` 外部属性配置**

​	属性名：location 

​	描述：属性文件路径（支持 `classpath:`、`file:` 前缀，多个路径用逗号分隔）。

-  **`<context:component-scan>`组件自动扫描**

​	属性名：base-package

​	描述：要扫描的包路径（支持通配符 `*`）。



#### 2.组件（Bean）作用域和周期方法注解

类似Servlet的init/destory方法，我们可以在周期方法完成初始化和资源释放等工作

```java
@Component
public class JavaBean {
//    周期方法名随意 但必须为 public void 无形参
    @PostConstruct
    public void init(){}
    @PreDestroy
    public void destroy(){}
}
```

`@scope`

```
@Scope(scopeName = "prototype")  // 指定作用域为 prototype
@Component
public class MyBean {}
```

- `singleton`（默认）：单例模式，整个 Spring 容器中只有一个实例。
- `prototype`：原型模式，每次请求时都会创建一个新的实例。

#### 3.Bean属性赋值：引用类型自动装配（DI）

```
@Service
public class OrderService {
    @Autowired  // 自动按类型（UserService）查找并注入[推荐]
    private UserService userService;
}
```

默认情况下至少有一个bean，否则报错！可以指定佛系装配`@Autowired(required = false)`

**解决歧义性（多个同类型 Bean）**

如果容器中存在多个相同类型的 Bean，需通过以下方式指定：

- 成员属性名指定@Autowired多个组件的时候，默认会根据成员属性名查找

- `@Qualifier` 指定 Bean 名称

```
@Autowired
@Qualifier("userServiceImplA")  // 指定注入的 Bean 名称
private UserService userService;
```

- @Resource ==@Qualifier+@Autowired

  ```
  @Resource(name = "userServiceImplA")  // 强制按名称注入
  private UserService userService;
  Spring 容器会将一个名为 "userServiceImplA" 的 Bean 实例，注入到当前类的 userService 字段中
  ```

​	**`@Resource`**：Java 标准（JSR-250）提供的依赖注入注解。

​	**`name = "userServiceImplA"`**：显式指定要注入的 Bean 的名称（ID）。

​	**`UserService userService`**：目标字段，接收注入的 Bean。

#### 4.Bean属性赋值：基本类型属性赋值（DI）

@value注解

-  直接赋值【不推荐】
- 读取外部配置

```java
@Component
public class AppConfig {
    @Value("1001")              // 直接赋字面值【很少使用】
    private int appId;

    @Value("${app.timeout}")    // 从配置文件中读取（如application.properties）
    private int timeout;

    @Value("true")              // boolean类型
    private boolean enableCache;
}
```

```properties
app.timeout=5000
app.name=MyApp
```

#### 5.基于注解+XML方式整合三层架构

### 基于配置类管理Bean

#### 1.完全注解开发理解

#### 2.配置类和扫描注解

```
package com.example.config;
//Java配置类替代xml配置文件
//包扫描注解配置   @ComponentScan(value = "com.example.ioc_01")
//引用外部配置文件  @PropertySource(value = "classpath:jdbc.properties")
//声明第三方依赖的Bean组件

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;
//添加@Configuration 代表该文件为配置类
//实现 1.包扫描注解配置 2.引用外部配置文件 3.声明第三方依赖的Bean组件

@ComponentScan(value = "com.example.ioc_01")
@PropertySource(value = "classpath:jdbc.properties")
@Configuration
public class JavaConfiguration {
}
```

#### 3.@Bean定义组件

<Bean> ------> 一个方法

方法的返回值类型 ===bean组件的类型或接口和它的父类

方法的名字 ==bean id

方法体中可以自定义实现过程

@Bean 让配置类的方法创建的对象存储到ioc容器中

#### 4.@Bean注解细节

@Bean生成BeanName的问题 即bean的id

​	默认：方法名

​	指定：name/value属性起名字，覆盖其方法 `@Bean(value = "bean的id")`

周期方法如何指定

​	原有注解方法：@PostConstruct	@PreDestroy

​	bean属性方法属性：initMethod	destroyMethod指定

作用域

​	@Scope 	默认：单例

如何引用其它ioc组件

​	方法一： 如果其他组件也是@Bean方法，可以直接调用【不推荐】

​	方法二： 形参列表声明想要的组件类型，可以是一个或多个

​		如果有多个，注入一个时需要使用形参名===对应的beanid标识即可

#### 5.@Import扩展

当有多个配置类时，可以整合配置

#### 6.基于注解+配置类方式整合三层框架结构

#### 7.总结

- 配置类的作用，完全注解开发

  替代xml文件

  替代扫描标签---》注解

  引用外部配置标签--》注解

  替代<bean> ---》注解

- 具体注解

  如何声明配置类@Configuration

  如何扫描包注解@ComponentScan

  如何引用外部配置@PropertySource

  如何声明bean @Bean->方法

- 配置类对应ioc容器

  ClasspathXml变为AnnotationConfigApplication

  #### 7.@SpringJUnitConfig

  自动创建ioc容器

  属性

  ​	locations=指定配置文件xml

  ​	value = 指定配置类

### Spring AOP面向切面编程

AOP将软件系统分为两个部分：核心关注点和横切关注点

#### 核心概念

##### 横切关注点

##### **通知（增强）**

每一个横切关注点上要做的事情都需要一个方法来实现，这样的方法就叫通知方法

- 前置通知 @Before ：在被代理的目标方法执行前执行

- 返回通知 @AfterReturing ：在被代理的目标方法成功结束后执行 
- 异常通知 @AfterThrowing ：在被代理的目标方法异常结束后执行
- 后置通知 @After ：在被代理的目标方法最终结束后执行
- 环绕执行 @Around ：使用try...catch...finally结构围绕整个被代理的目标方法，包括上面四种通知所对应的所有位置

##### **连接点joinpoint**

指那些被拦截的点.可以被切入的方法

##### **切入点pointcut**

定位连接的点，可以理解为被选中的连接点

##### **切面aspect**

切入点和通知的结合。是一个类。切点+增强

##### **目标target**

被代理的目标对象。被切入类的对象

##### **代理proxy**

向目标对象应用通知之后创建的代理对象

##### **织入weave**

指把通知应用到目标上，生成代对象的过程。可以在编译织入，也可在运行期织入，spring采用后者。

#### Spring AOP基于注解方式实现和细节

##### Spring AOP底层技术组成

Spring基于注解的AOP>AspectJ注解层+具体实现层

具体实现层：动态代理（有接口情况）或者cglib（没有接口的情况）

##### 初步实现

1. 加入依赖
   - spring-aop
   - spring-aspects
2. 增强类，定义三个增强方法（存储横切关注点的代码）
3. 增强类的配置：插入切点的位置，切点指定等等
4. 补全注解 @Component 加入ioc容器         @Aspect 配置切面
5. 开启aop配置：<aop:aspectj-autoproxy />           @EnableAspecJAutoProxy           支持aspectj的注解配置   

##### 获取通知细节信息

- 目标方法信息（方法名，参数，访问修饰符，所属类的信息等）

  - （JointPoint jointpoint） import org.aspectj.lang.JoinPoint;	

  - jointpoint包含目标方法的信息	

    - 获取方法属于的类的信息

      JoinPoint.getTarget().getClass().getSimpleName()

    - 获取方法名称

      JoinPoint.getSignature().getName();

    - 获取参数列表

      JoinPoint.getArgs();

    - 访问修饰符

      JoinPoint.getSignature().getModifiers();

方法返回值 - @AfterReturing

异常信息 - @AfterThrowing

##### 切点表达式语法

execution(权限修饰符 方法返回参数类型 方法所在类型的全类名 方法名 （参数列表）)

- 固定语法 execution（1 2 3.4.5 （6））

  - 1.访问修饰符 private/public/....

  - 2.方法返回参数类型 String int void

    ​	如果不考虑修饰符和返回值，这两位整合一起写 *

  - 3.包的位置

    - 具体包
    - 单层模糊  *
    - 多层模糊  .. 不能开头

  - 4.类的名称

    - 具体：CalculationPureUmpl
    - 模糊：* 
    - 部分模糊：*Impl 以Impl结尾的

  - 5.方法名：语法与类名一致

  - 6.形参参数列表 （String，int）

  - 模糊参数（...） 有没有参数都行

  - 部分模糊（String..）String后面有没有参数都行

    ​		（..int）

##### 重点（提取）切点表达式

方法一【不推荐】：当前一个类中提取 

定义一个空方法

 注解@Pointcut() 

增强注解中引用切点表达式的方法 直接调用方法

方法二：创建一个存储切点的类 

单独维护切点表达式

其它类的切点方法 类的全限定符.方法

##### 环绕通知

1. **拦截目标方法**：当程序调用匹配切入点的目标方法时，环绕通知会首先拦截该调用。

2. **执行前置逻辑**：在通知中编写的前置增强代码会在此阶段执行（例如记录开始时间、验证参数等）。

3. 调用目标方法：通过`ProceedingJoinPoint.proceed()`

   方法触发目标方法的执行。此步骤可选择：

   - 传递原始参数直接执行
   - 修改参数后执行
   - 完全阻止目标方法执行（不调用`proceed()`）

4. 处理返回值 / 异常：

   - 如果目标方法正常返回，通知可以获取返回值并进行处理（如包装、转换等）
   - 如果目标方法抛出异常，通知可以捕获并处理，或继续向上抛出

5. **执行后置逻辑**：目标方法执行完成后（无论正常返回还是抛出异常），执行通知中的后置增强代码（如记录结束时间、释放资源等）

6. **返回结果**：通知必须显式返回目标方法的返回值（或处理后的返回值）给调用方

##### 切面优先级设置

@Order(值大)：优先级低

@Order(值小)：优先级高

##### CGLib动态代理生效

##### 注解实现小结

### Spring声明式事务

#### 声明式事务概念

声明式事务是使用注解或XML配置的方式来控制事务的提交和回滚

#### 基于注解的声明式事务

##### **基本流程**

1. 基本配置步骤	
   1. 添加必要依赖
   2. 配置事务管理器

```
@Configuration
@EnableTransactionManagement //开启事务支持
public class AppConfig {
    
    @Bean
    public DataSource dataSource() {
        // 创建并配置数据源
        return new DriverManagerDataSource();
    }
    //装配事务管理器实现对象
    @Bean
    public TransactionManager transactionManager() {
        return new DataSourceTransactionManager(dataSource());
    }
}
```

 2. 使用 @Transactional 注解

    	1. 基本用法

    ```
    @Service
    @EnableTransactionManagement //开启事务支持
    public class OrderService {
        
        @Autowired//事务注解
        private OrderRepository orderRepository;
        
        @Transactional 
        public void createOrder(Order order) {
            // 业务逻辑
            orderRepository.save(order);
            // 如果抛出RuntimeException，事务会自动回滚
        }
    }
    ```

​		2.完整属性配置

```
@Transactional(
    propagation = Propagation.REQUIRED,      // 事务传播行为
    isolation = Isolation.DEFAULT,           // 隔离级别
    timeout = 30,                           // 超时时间(秒)
    readOnly = false,                       // 是否只读事务
    rollbackFor = Exception.class,          // 触发回滚的异常类型
    noRollbackFor = RuntimeException.class // 不触发回滚的异常类型
)
public void updateOrder(Order order) throws Exception {
    // 业务逻辑
}
```

##### 只读模式：

​	带来性能优化，特别是对于查询操作。

使用 `@Transactional` 注解的 readOnly 属性

```
@Transactional(readOnly = true)
public List<User> findAllUsers() {
    return userRepository.findAll();
}
```

##### 超时时间:

​	指一个事务允许执行的最长时间。如果事务在指定时间内没有完成，将被自动回滚。超时回滚，释放资源

使用 `@Transactional` 注解设置超时（默认永不超时）

```
@Transactional(timeout = 30) //单位：s
```

##### 事务异常指定：

1.基本异常回滚配置

默认行为

- **回滚**：遇到 `RuntimeException` 或 `Error` 时自动回滚
- **不回滚**：遇到检查型异常(checked exception)时默认提交事务

2.让所有异常都触发回滚

```
@Transactional(rollbackFor = Exception.class)
public void method() throws Exception {
    // ...
}
```

3.noRollbackFor

回滚异常范围内，控制某个异常不回滚

##### 事务隔离级别

定义了一个事务可能受其他并发事务影响的程度。4种隔离级别，通过`@Transactional`注解的`isolation`属性进行配置。

- READ_UNCOMMITTED（读未提交）
  - 允许读取未提交的数据变更（脏读）
  - 性能最高，但可能出现：
    - 脏读（Dirty Read）
    - 不可重复读（Non-repeatable Read）
    - 幻读（Phantom Read）
  - **适用场景**：对数据一致性要求极低，需要最高性能的统计查询
-  READ_COMMITTED（读已提交）
  - 只允许读取已提交的数据
  - 防止脏读，但可能出现：
    - 不可重复读
    - 幻读
  - **适用场景**：大多数业务场景的推荐选择
- REPEATABLE_READ（可重复读）
  - 确保同一事务中多次读取同样数据结果一致
  - 防止：
    - 脏读
    - 不可重复读
  - 但可能出现幻读
  - **适用场景**：需要保证同一事务内多次查询结果一致的场景
- SERIALIZABLE（串行化）
  - 最高隔离级别，完全串行执行
  - 防止所有并发问题：
    - 脏读
    - 不可重复读
    - 幻读
  - 性能最低
  - **适用场景**：对数据一致性要求极高且并发量不大的金融交易

##### 事务的传播行为

​	事务方法相互调用时，事务应该如何传播。Spring提供了7种传播行为，通过`@Transactional`注解的`propagation`属性配置。事务传播行为设置到子事务上

- REQUIRED（默认）

  ```
  @Transactional(propagation = Propagation.REQUIRED)
  ```

  - **行为**：如果当前存在事务，则加入该事务；如果不存在，则新建一个事务
  - **特点**：最常用的传播行为
  - **适用场景**：大多数业务方法

-  REQUIRES_NEW

  ```
  @Transactional(propagation = Propagation.REQUIRES_NEW)
  ```

  - **行为**：总是新建一个事务，如果当前存在事务，则将其挂起

  - **特点**：独立事务，不受外层事务影响

  - **适用场景**：需要独立提交/回滚的操作（如日志记录）

##### 传播行为对比表

| 传播行为      | 当前有事务           | 当前无事务 | 特点           |
| :------------ | :------------------- | :--------- | :------------- |
| REQUIRED      | 加入事务             | 新建事务   | 默认选择       |
| REQUIRES_NEW  | 挂起当前，新建事务   | 新建事务   | 独立事务       |
| SUPPORTS      | 加入事务             | 非事务执行 | 灵活查询       |
| NOT_SUPPORTED | 挂起当前，非事务执行 | 非事务执行 | 强制非事务     |
| MANDATORY     | 加入事务             | 抛出异常   | 强制要求事务   |
| NEVER         | 抛出异常             | 非事务执行 | 强制要求无事务 |
| NESTED        | 嵌套事务执行         | 新建事务   | 部分回滚       |

## MyBatis

### Mybatis简介

**持久层框架**，主要用于简化 Java 程序与数据库的交互过程。支持自定义SQL、存储过程以及高级映射

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--xml方式写sql语句-->
<!--namespace = mapper对应接口的全限定符-->
<mapper namespace="com.example.mapper.UserMapper">
    <!-- 这里编写SQL映射语句 -->
<!--    声明标签写sql语句 crud select insert update delete-->
<!--    每个标签对应接口的一个方法 方法的一个实现-->
<!--    主要 mapper接口不能重载 ,因为mapper.xml无法识别 根据方法名识别-->
    <select id="queryById" resultType="com.example.pojo.Employee">
        select * from t_emp where emp_id = #(id)
    </select>
</mapper>
```



将SQL与Java代码分离，mybatis通过xml配置文件或注解，将 SQL 语句与 Java 接口绑定

```xml
<!-- UserMapper.xml -->
<select id="selectUserById" parameterType="int" resultType="User">
    SELECT * FROM users WHERE id = #{id}
</select>
```

```
public class MyBatisTest {
    @Test
    public void test() throws IOException {
        //读取外部配置文件
        InputStream ips = Resources.getResourceAsStream("mybatis-config.xml");
        //创建sqlSessionFactory
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(ips);
        //获取SqlSession对象【自动开启 JDBC】
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //获取代理mapper对象
        EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
        Employee employee = mapper.queryById(1);
        System.out.println("employee"+employee);
        //关闭资源或者提交事务
        sqlSession.close();
    }
}
```

### Mybatis基本使用

#### 向SQL语句传参

##### mybatis日志输出配置

在 `mybatis-config.xml` 中添加日志配置：

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

- **优先使用 `#{}`**：几乎所有场景都应使用 `#{}`，尤其是接收用户输入时。 可以防止在【注入攻击】的问题
- **谨慎使用 `${}`**：仅在无法避免的场景（如表名、列名、排序字段）使用，且必须确保参数来源安全。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
<!--        开启mybatis的日志输出，选择system进行控制台输出-->
        <setting name="IogImpl" value="STDOUT_LOGGING"/>
    </settings>

    <!-- 环境配置 -->
    <environments default="development">
        <environment id="development">
            <!-- 使用JDBC事务管理 -->
            <transactionManager type="JDBC"/>

            <!-- 数据库连接池配置 -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis-example"/>
                <property name="username" value="root"/>
                <property name="password" value="051013"/>
            </dataSource>
        </environment>
    </environments>

    <!-- 映射器配置 -->
    <mappers>
        <mapper resource="mappers/EmployeeMapper.xml"/>
    </mappers>
</configuration>
```

#### 数据输入

#{key} 	${key}

传入单个简单类型 key随便写

传入实体对象 key为传入对象的属性名

传入多个简单类型 	方案1：key = @Param("value值")【推荐】	方案2：形参从左到右依次对应 arg0 arg1 （name，salary）name->key=arg0   salary->key = arg1	

传入map类型 key = map的key

#### 数据输出 

resultType 语法 ：1.类的全限定符 2.别名简称

- dml语句（插入、修改、删除）

- 查询语句 

  返回单个简单类型 resultType = “ ”	resultType 语法 ：1.类的全限定符 2.别名简称

- 自定义数据类型

  - 类的全限定符号	

    - 自定义别名   

      - <typeAliases> 

      ​	<typeAlias type="类的全限定符" alias="别名">

      </typeAliases>   

      - @Alias("别名")

      - 批量设置

         <typeAliases> 

        <!--批量将包下的类给与别名 别名就是类的首字母小写-->

        ​	<package name="">

        </typeAliases>   

- 返回单个自定义实体类型

  - resultType = “返回值类型” 	
  - 默认要求：查询，返回单个实体类型，要求列名和属性名要一致 这样才可以进行实体类和属性映射
  - 但是可以进行设置 设置支持驼峰式自动映射 <setting name = "mapUnderscoreToCamelCase" value ="true"/>

- 返回map数据类型            当没有实体类可以使用接值的时候 我们可以使用 `Map<String, Object>`接受数据      key ->查询的列   value-->查询的值

  ```xml
  <!-- EmployeeMapper.xml -->
  <select id="selectEmpAsMap" resultType="map">
      SELECT emp_id AS id, emp_name AS name, salary 
      FROM employee 
      WHERE emp_id = #{id}
  </select>
  ```

- 返回集合类型  **`List<实体类>`**          resultType不需要指定集合类型，只需要指定泛型即可

- 主键回显 获取插入数据的主键

  - **使用 `useGeneratedKeys` + `keyProperty`（推荐，简洁）**

    - **`useGeneratedKeys="true"`**：告诉 MyBatis 该语句使用数据库自动生成的主键。
    - **`keyColumn` ：**是用于指定数据库表中主键列名
    - **`keyProperty="id"`**：指定实体类中接收主键的属性名（如实体类的 `id` 属性）。

    ```xml
    <insert id="insertUser" 
            useGeneratedKeys="true"
            keyProperty="id"       <!-- 实体类中接收主键的属性名 -->
            keyColumn="user_id">   <!-- 数据库表中主键的列名 -->
        INSERT INTO t_user (username) VALUES (#{username})
    </insert>
    ```

- 非自动增长类型

```xml
<insert id="insertUser" parameterType="com.example.pojo.User">
    <!-- 插入前执行，获取序列值并赋值给实体类的 id 属性 -->
    <selectKey 
        keyProperty="id"        <!-- 实体类接收主键的属性 -->
        resultType="java.lang.Long" <!--返回值类型 -->
        order="BEFORE">        <!-- 插入前执行 -->
        SELECT USER_SEQ.NEXTVAL FROM DUAL
    </selectKey>
    
    INSERT INTO t_user (id, username) VALUES (#{id}, #{username})
</insert>
```

- 自定义映射关系

  `resultMap` 是用于**自定义 SQL 查询结果与实体类属性之间映射关系**

  自定义映射关系

  ```xml
  <resultMap id="userResultMap" type="com.example.pojo.User"> <!--type 具体的返回值类型 -->
      <!-- id 标签：映射主键列（可选，用于优化） -->
      <id property="id" column="user_id"/>
      
      <!-- result 标签：映射普通列 -->
      <result property="name" column="user_name"/>  <!--property实体属性 column数据库列名 -->
  </resultMap>
  ```

```xml
<select id="getUserById" resultMap="userResultMap">
    SELECT user_id, user_name FROM t_user WHERE user_id = #{id}
</select>
```

### Mybatis多表映射

#### 多表实体类存储设计

##### 技巧：

对一，属性包含对方集合

对多，属性包含对方集合

```xml
<!--一对一 -->
<mapper namespace="com.example.mapper.OrderMapper">
    <!-- 订单基本信息 -->
    <resultMap id="BaseResultMap" type="Order">
        <id column="id" property="id"/>
        <result column="order_date" property="orderDate"/>
    </resultMap>

	<!-- 带客户信息的订单 -->	
    <resultMap id="OrderWithCustomerMap" type="Order" extends="BaseResultMap">
        <association property="customer" javaType="Customer">
            <id column="customer_id" property="id"/>
            <result column="customer_name" property="name"/>
        </association>
    </resultMap>

    <!-- 查询订单及其客户 -->
    <select id="selectOrderWithCustomer" resultMap="OrderWithCustomerMap">
        SELECT 
            o.id,
            o.order_date,
            o.customer_id,
            c.name AS customer_name
        FROM orders o
        JOIN customer c ON o.customer_id = c.id
        WHERE o.id = #{id}
    </select>
</mapper>
```




```xml
<!-- 客户映射 (一对多) -->
<resultMap id="CustomerWithOrdersMap" type="Customer">
    <id column="id" property="id"/>
    <result column="name" property="name"/>
    <collection property="orders" ofType="Order">  <!--给集合属性赋值 property=集合属性名 ofType=集合泛型类型 -->
        <id column="order_id" property="id"/>
        <result column="order_date" property="orderDate"/>
    </collection>
</resultMap>
```

##### 优化：

`<autoMappingBehavior>` 可以控制这种自动映射的范围，有三个可选值：

- **`NONE`**：取消自动映射，必须通过 `<resultMap>` 显式映射每个字段。
- **`PARTIAL`**（默认值）：自动映射没有在 `<resultMap>` 中显式配置的字段。
- **`FULL`**：自动映射所有字段，即使存在嵌套的复杂类型（如 `Customer` 对象）。

```xml
<settings>
    <setting name="autoMappingBehavior" value="PARTIAL"/>
</settings>
```

### Mybatis动态语句

#### where和if标签

if 判断传入的参数，最终是否添加语句

​	test属性 内部做比较运算，最终为true将标签内部的sql语句进行拼接 false不拼接

```xml
<select id="selectUser" resultMap="BaseResultMap">
    SELECT * FROM users
    WHERE
    <if test="id != null">
        AND id = #{id}
    </if>
    <if test="username != null and username != ''">
        AND username LIKE CONCAT('%', #{username}, '%')
    </if>
</select>
```

**使用 `<where>` 标签替代 `WHERE`**

标签会自动处理：

- 当没有条件时或条件为false时，不生成 WHERE 子句
- 自动移除第一个条件前的 AND/OR 关键字

#### set标签

1. **动态生成 SET 子句**

   - 只更新非空字段，避免将 NULL 值更新到数据库
   - 自动处理逗号问题，移除最后一个多余的逗号

   ```xml
   <!-- 使用<set>标签 -->
   <set>
       <if test="username != null">
           username = #{username},
       </if>
   </set>
   ```

#### trim标签

**`<trim>` 标签的核心功能和参数：**

1. **通用标签**
   - 可替代 `<where>`、`<set>` 等标签
   - 通过自定义参数实现灵活的前缀、后缀处理
2. **关键参数**
   - `prefix`：在内容前添加的前缀
   - `suffix`：在内容后添加的后缀
   - `prefixOverrides`：移除内容前的特定字符
   - `suffixOverrides`：移除内容后的特定字符

**替代 `<where>` 的原理**

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
    ...
</trim>
```

- 添加 WHERE 前缀
- 自动移除第一个条件前的 AND 或 OR

**替代 `<set>` 的原理**

```xml
<trim prefix="SET" suffixOverrides=",">
    ...
</trim>
```

- 添加 SET 前缀
- 移除最后一个逗号

#### choose/when/otherwise 标签

在多个分支条中，仅执行一个

- 步骤

  - 从上到下依次执行条件判断

  - 遇到的第一个满足条件的分支会被采纳

  - 被采纳分支后面的分支都不被考虑

  - 如果所有的when分支都不被满足，那么执行otherwise

  <select id="selectUserByCondition" resultMap="BaseResultMap">
          SELECT * FROM users
          <where>
              <choose>
                  <when test="id != null">
                      id = #{id}
                  </when>
                  <when test="username != null">
                      username LIKE CONCAT('%', #{username}, '%')
                  </when>
                  <when test="email != null">
                      email = #{email}
                  </when>
                  <otherwise>
                      status = 'active'
                  </otherwise>
              </choose>
          </where>
      </select>

#### foreach标签

1. **遍历集合生成 SQL 片段**

   - 支持数组、List、Set 等集合类型
   - 常用于 IN 查询、批量插入 / 更新

2. **关键参数**

   - ```
     collection
     ```

     ：必填，指定要遍历的集合

     - 传入 List 时默认使用 "list"
     - 传入数组时默认使用 "array"
     - 传入 Map 时可使用 key

   - `item`：集合中每个元素的别名

   - `index`：当前元素在集合中的索引

   - `open`：拼接的字符串前缀

   - `close`：拼接的字符串后缀

   - `separator`：元素之间的分隔符

   ```xml
   WHERE id IN
   <foreach item="id" collection="list" open="(" separator="," close=")">
       #{id}
   </foreach>
   ```

   - 当传入 [1,2,3] 时，生成：`WHERE id IN (1,2,3)`

3. 批量插入中需要设置 允许多语句执行

   如果使用传统的 `mybatis-config.xml`，在数据源配置中添加参数：

   ```xml
   <dataSource type="POOLED">
       <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
       <property name="url" value="jdbc:mysql://localhost:3306/yourdb?allowMultiQueries=true"/>
       <property name="username" value="root"/>
       <property name="password" value="yourpassword"/>
   </dataSource>
   ```

#### sql片段

- **定义可复用的 SQL 片段**
  - 将常用的 SQL 片段提取出来，避免重复编写
  - 提高 SQL 的可维护性和复用性

```xml
<sql id="片段ID">
    SQL语句片段
</sql>

<select|insert|update|delete>
    <include refid="片段ID"/>
</select|insert|update|delete>
```

### Mybatis高级扩展

#### Mapper批量映射优化

<package name = "com.example.mapper"/>

1.要求Mapperxml文件和mapper接口的命名必须相同

2.要求打包后的位置要一致 都是指定的包地址下

​	resource文件夹创建对应结构

#### 插件和分页插件PageHelper

插件可以用于语句执行过程中进行拦截，并允许通过自定义处理程序来拦截和修改SQL语句、映射语句的结果等。

#####  PageHelper使用

1. 导入依赖

2. mybatis-config.xml配置分页插件

     

   ```
   <plugins>
           <plugin interceptor="com.github.pagehelper.PageInterceptor">
               <!-- 数据库方言 -->
               <property name="helperDialect" value="mysql"/>
           </plugin>
       </plugins>
   ```

   `com.github.pagehelper.PageInterceptor`是PageHelper插件的名称。`dialect`属性用于指定数据库类型

   

3. ```java
   @Test
   public void testPageHelper() {
       // 测试第一页，每页10条
       int pageNum = 1;
       int pageSize = 10;
       //调用之前，先设置分页数
       PageHelper.startPage(pageNum,pageSize);
       List<Employee> list = mapper.queryList();
       //将查询数据封装到一个PageInfo的分页实体类
       PageInfo<Employee> pageInfo = new PageInfo<>(list);
       
       //pageInfo获取分页的数据
       
       //当前页的数据  
       List<Employee> list1 = pageInfo.grtList();
       //获取总条数 
       int pages = pageInfo.getList(); 
       //获取总页数...
       long total = pageInfo.total();
      
   }
   ```

#### 逆向工程和MyBatisX插件

用于**自动创建 MyBatis 映射文件和实体类**，减少手动编写重复代码的工作量。它通过解析数据库表结构，生成对应的：

1. **实体类（POJO）**：与数据库表字段一一对应的 Java 类
2. **XML 映射文件**：包含基本的 CRUD 操作 SQL
3. **Mapper 接口**：定义数据库操作的接口

```plaintext
数据库表结构 → MBG配置 → 自动生成代码 → 手动调整优化
```

Mybatis的逆向工程向我们提供快捷方便的方式，能够快速的生成持久层代码和映射文件，是半自动ORM思维向全自动发展的过程，提高开发效率。

**注意：**逆向工程只能生成单表crud的操作，多表查询依然需要我i们自己编写

## SpringMVC

### SpringMVC简介

SpringMVC 是 Spring 框架的一个核心模块，用于构建构建基于 Java Web 应用的**MVC 设计模式实现**，专注于处理 Web 层的请求与响应

**什么是 MVC？**

MVC 是一种软件架构模式，将应用分为三个核心部分：

- **Model（模型）**：处理业务逻辑和数据（如 JavaBean/POJO、Service、DAO 等）。
- **View（视图）**：展示数据（如 JSP、HTML、Thymeleaf 等）。
- **Controller（控制器）**：接收用户请求，协调 Model 和 View（如 SpringMVC 的 `@Controller` 类）。

SpringMVC 通过这种分层思想，实现了**业务逻辑与显示逻辑的分离**，使代码更易于维护和扩展。

#### SpringMVC 的核心组件的详细理解：

##### **1. 前端控制器（DispatcherServlet）**

- **核心地位**：整个 SpringMVC 的入口，所有请求都先经过它处理，相当于 “中央调度器”。
- 作用
  - 接收客户端所有请求（通过 `web.xml` 或注解配置映射路径，如 `/` 匹配所有请求）。
  - 协调其他组件完成请求处理（无需开发者手动调用其他组件）。
  - 统一处理响应结果，返回给客户端。
- **为什么需要**：替代传统 Web 开发中多个 Servlet 各自处理请求的混乱模式，通过一个入口统一管理请求，降低组件间耦合。

##### **2. 处理器映射器（HandlerMapping）**

- **作用**：根据请求的 URL 路径，找到对应的**处理器（Handler）**（即控制器中处理请求的方法）。（秘书）
- 工作方式
  - 内部维护 “URL 路径 → 处理器” 的映射关系（基于 `@RequestMapping` 等注解或 XML 配置生成）。
  - 当 `DispatcherServlet` 收到请求后，会调用 `HandlerMapping` 获取对应的处理器。
- 常见实现
  - `RequestMappingHandlerMapping`（主流）：处理 `@RequestMapping` 注解的映射关系。
  - 基于 XML 配置的 `BeanNameUrlHandlerMapping`（早期使用，现在较少）。

##### **3. 处理器适配器（HandlerAdapter）**

- **作用**：适配不同类型的处理器（Handler），执行处理器中的业务逻辑。（经理）
- 为什么需要
  - 处理器可能有多种形式（如注解式控制器、实现特定接口的控制器等），`HandlerAdapter` 屏蔽了不同处理器的差异，使 `DispatcherServlet` 无需关心具体实现。
- 工作方式
  - `DispatcherServlet` 通过 `HandlerMapping` 拿到处理器后，会找到对应的 `HandlerAdapter`。
  - 由 `HandlerAdapter` 调用处理器的方法（如控制器中被 `@RequestMapping` 标记的方法），并返回处理结果（`ModelAndView`）。

##### **4. 处理器（Handler）**

- **本质**：开发者编写的**业务逻辑处理器**，即控制器（`@Controller` 标记的类）中的方法。
- 作用
  - 接收请求参数（通过参数绑定自动获取）。
  - 调用 Service 层处理业务逻辑。
  - 返回处理结果（如 `ModelAndView` 或 JSON 数据）。

##### **5. 视图解析器（ViewResolver）**

- **作用**：将处理器返回的**逻辑视图名**（如 `"userDetail"`）解析为**物理视图路径**（如 `/WEB-INF/views/userDetail.jsp`），非必须。
- 工作方式
  - 接收 `ModelAndView` 中的逻辑视图名。
  - 根据配置的前缀（`prefix`）和后缀（`suffix`）拼接路径。
  - **支持的视图技术**：JSP、Thymeleaf、FreeMarker 等，不同视图技术对应不同的 `ViewResolver` 实现。

### SpringMVC接收数据

#### 访问路径设置

@RequestMapping注解的作用是将请求的URL地址和处理器方法（handler方法）关联起来，建立映射关系

`*`任意一层字符串，`**`任意层任意字符中

##### 1. 基本用法（类级别 + 方法级别）

```java
// 类级别：指定基础路径
@RequestMapping("/user")
@Controller
public class UserController {
    
    // 方法级别：指定具体路径，完整路径为 /user/list
    @RequestMapping("/list")
    public String userList() {
        return "userList"; // 返回视图名
    }
}
```

#####  指定请求方法

通过`method`属性限制请求类型（GET/POST/PUT/DELETE 等）：

```java
@RequestMapping(value = "/save", method = RequestMethod.POST)
public String saveUser() {
    // 处理POST请求保存用户
    return "success";
}

// REST风格示例（GET请求）
@RequestMapping(value = "/{id}", method = {RequestMethod.GET , RequestMethod.POST})
public String getUserById(@PathVariable Integer id) {
    // 根据ID查询用户
    return "userDetail";
}
```

##### 3.注解简化

针对常用**请求方法**提供了简化注解：

- `@GetMapping` = `@RequestMapping(method = RequestMethod.GET)`
- `@PostMapping` = `@RequestMapping(method = RequestMethod.POST)`
- `@PutMapping`、`@DeleteMapping`等类似

#### 接收数据【重点】

##### param和json参数比较

1. 参数编码
   1. **Param 参数**
      - 采用`application/x-www-form-urlencoded`编码（默认），特殊字符（如空格、&、= 等）需转义（如空格→`+`或`%20`）。
      - 数据格式为`key1=value1&key2=value2`，适合简单键值对。
      - 示例：`name=zhangsan&age=20&hobby=reading&hobby=sports`
   2. **JSON 参数**
      - 采用`application/json`编码，数据以纯文本形式传输，特殊字符无需额外转义（由 JSON 语法本身处理）。
      - 示例：`{"name":"zhangsan","age":20,"hobby":["reading","sports"]}`
2. 参数顺序
   1. **Param 参数**
      - 传统上 HTTP 规范不保证 Param 参数的顺序（尽管实际传输中顺序会保留），但服务器解析时可能不严格依赖顺序。
      - 适合无顺序依赖的简单场景（如表单提交）。
   2. **JSON 参数**
      - JSON 规范（RFC 7159）明确规定对象的键值对**无顺序性**（尽管现代解析器通常会保留插入顺序）。
      - 数组（`[]`）中的元素**严格保留顺序**，适合需要顺序的列表数据。
3. 数据类型
   1. **Param 参数**
      - 本质上是**字符串键值对**，没有原生类型区分，服务器需手动转换类型（如字符串→数字、布尔值）。
      - 示例：`age=20`在传输中是字符串，需解析为整数。
   2. **JSON 参数**
      - 原生支持多种数据类型：字符串（`""`）、数字（`123`）、布尔值（`true/false`）、null、数组、对象。
      - 示例：`{"age":20,"isStudent":true,"score":95.5}`中的类型可被直接解析。
4. 嵌套性
   1. **Param 参数**
      - 不直接支持嵌套结构，需通过特殊格式模拟（如`user.name=zhangsan&user.age=20`），复杂度高且不直观。
      - 多层嵌套（如对象数组）难以表达，易出错。
   2. **JSON 参数**
      - 原生支持任意层级的嵌套结构（对象嵌套对象、对象包含数组等），适合复杂数据。
5. 可读性
   1. **Param 参数**
      - 扁平结构可读性尚可，但多层嵌套时（如`user.address.city=Beijing`）显得冗长混乱。
      - 示例：`user.name=zhangsan&user.age=20&user.address.city=Beijing&user.address.street=Main%20St`
   2. **JSON 参数**
      - 结构化的层级格式（缩进、括号匹配）使数据关系清晰，尤其适合复杂数据。
      - 人类可直接阅读和编辑，调试友好。

| 维度     |            Param 参数             |           JSON 参数            |
| -------- | :-------------------------------: | :----------------------------: |
| 参数编码 | application/x-www-form-urlencoded |        application/json        |
| 参数顺序 |      不保证（实际可能保留）       |       对象无序，数组有序       |
| 数据类型 |       仅字符串，需手动转换        | 原生支持多类型（数字、布尔等） |
| 嵌套性   |      不支持，需模拟（复杂）       |      原生支持任意层级嵌套      |
| 可读性   |     扁平结构尚可，复杂结构差      |  结构化清晰，复杂场景可读性好  |

##### 接收参数方法

**1.直接指定**

形参列表，填写对应名称即可  	请求形参名 =形参参数名即可

名称相同	可以不传递 不报错

data?name = "张三"&age =18

```java
@RequestMapping("data")	
@ResponseBody
public String data(String name,int age){
	return "name= "+name +",age="+age;
}
```

**2.注解指定**

指定任意的请求参数名，要求必须传递，或者要求不必须传递，给予一个默认值

name必须传递 ，age不必须传递，默认值为1

data?name = "张三"&age =18

```java
@RequestMapping("data")	
@ResponseBody
public String data(@RequestParam(value="name") String username,
                   @RequestParam(required = false, defaulet = "1") int age){
	return "name= "+name +",age="+age;
}
```

 @RequestParam --->形参列表 指定请求参数名 或者是否必须传递 或者 非必须传递设置默认值

| `value`/`name` | 指定要获取的请求参数名称（当方法参数名与请求参数名不一致时使用）。 | 无     |
| -------------- | ------------------------------------------------------------ | ------ |
| `required`     | 表示该参数是否必须存在。若为 `true` 且参数不存在，会抛出异常。 | `true` |
| `defaultValue` | 当参数不存在时的默认值（设置后 `required` 会自动变为 `false`）。 | 无     |

**3.特殊值**

一名多值 key = 1& key = 2 直接使用集合接值

data?hbs = 吃&hbs = 玩 &hbs = 学习

```java
@RequestMapping("data")	
@ResponseBody
public String data(@RequesParam List<String> hbs){
	return hbs;
}
```

**4.实体类接值**

1.  定义实体类（仅包含 name 和 age）

   创建一个简单的实体类，包含`name`和`age`属性，并提供必要的`getter`、`setter`和无参构造函数。

   ```java
   public class User {
       private String name;  // 姓名
       private Integer age;  // 年龄
   	// 无参构造函数（Spring必须）
       public User() {}
       
       // getter和setter方法（用于Spring注入参数）
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public Integer getAge() {
           return age;
       }
   
       public void setAge(Integer age) {
           this.age = age;
       }
   }
   ```

2. 控制器中使用实体类接收参数        接收 JSON 参数

   适用于`application/json`类型的请求（如 AJAX、前端框架提交的 JSON 数据），需配合`@RequestBody`注解。

   ```java
   @RestController  // 自动返回JSON，无需@ResponseBody
   @RequestMapping("/api/user")
   public class UserApiController {
   	// 接收JSON参数并绑定到User实体
   	@PostMapping("/add")
   	public String addUser(@RequestBody User user) {
       	// 直接使用实体类中的属性
       	return "接收成功：姓名=" + user.getName() + "，年龄=" + user.getAge();
   	}
   }
   ```

**5.路径参数传递**

通过 URL 路径中的动态部分传递数据（如`/user/1001`中的`1001`），在 Spring MVC 中可通过`@PathVariable`注解接收这些参数。

    @RestController
    public class PathParamController {
    	// 路径中的{id}是参数占位符，通过@PathVariable接收
        @GetMapping("/user/{id}")
        public String getUserById(@PathVariable Integer id) {
            return "获取用户ID：" + id;
        }
    }
**6.json数据接收**

​	1.定义实体类（与 JSON 结构对应）

​		首先创建一个实体类，属性名需与 JSON 中的键名一致（大小写敏感）。

​	2.控制器接收 JSON 数据

​		使用`@RequestBody`注解将请求体中的 JSON 绑定到实体类对象。

```java
@RestController  // 自动返回JSON，无需@ResponseBody
public class JsonController {    
    // 接收JSON格式的User数据
    @PostMapping("/user/json")
    public String receiveJson(@RequestBody User user) {
        // 直接使用转换后的User对象
        return user.toString();
    }
}
```


#### 接受cookie

    @RestController
    public class CookieController {
     	// 接收指定名称的Cookie
        @GetMapping("/get-cookie")
        public String getCookie(
            // 获取名为"username"的Cookie值
            @CookieValue("username") String username,
    
            // 获取名为"token"的Cookie值，若不存在则使用默认值"unknown"
            @CookieValue(value = "token", defaultValue = "unknown") String token,
    
            // 接收可选Cookie，不存在时为null（required默认为true，不传递会报错）
            @CookieValue(value = "theme", required = false) String theme
        ) {
            return String.format(
                "Cookie信息：<br>" +
                "username: %s<br>" +
                "token: %s<br>" +
                "theme: %s",
                username, token, theme
            );
        }
    }
**关键参数说明**

- `value`/`name`：指定要获取的 Cookie 名称（必需）。
- `defaultValue`：当 Cookie 不存在时使用的默认值（可选）。
- `required`：是否为必需 Cookie。默认为`true`，若 Cookie 不存在且未设置默认值，会抛出`MissingCookieException`；设为`false`时，不存在则为`null`。

#### 接受请求头数据

    // 标记为控制器，且响应直接返回字符串（无需视图解析）
    @RestController
    public class SimpleHeaderController {
        // 处理GET请求，路径为/agent
        @GetMapping("/agent")
        public String getAgent(
            // 接收名为"User-Agent"的请求头，直接绑定到参数
            @RequestHeader("User-Agent") String userAgent
        ) {
            // 直接返回获取到的请求头信息
            return "你的浏览器信息：" + userAgent;
        }
    }
#### 原生Api操作

```java
@RestController
public class RequestDemoController {
    // 处理登录请求（模拟）
    @RequestMapping("/login")
    public void login(HttpServletRequest request, HttpServletResponse response) throws IOException {
        // 1. 从请求中获取参数（账号和密码）
        String username = request.getParameter("username");
        String password = request.getParameter("password");

        // 2. 简单验证（实际项目中会查数据库）
        String result;
        if ("admin".equals(username) && "123456".equals(password)) {
            result = "登录成功，欢迎您：" + username;
        } else {
            result = "账号或密码错误";
        }

        // 3. 通过响应对象返回结果（设置编码避免中文乱码）
        response.setContentType("text/html;charset=UTF-8"); // 关键：指定编码
        PrintWriter out = response.getWriter();
        out.write(result); // 输出响应内容
        out.flush(); // 刷新缓冲区
    }
}
```
#### 共享域对象操作

### SpringMVC响应数据

##### 快速返回逻辑

在 Spring MVC 中，快速返回逻辑视图的核心是**控制器方法直接返回逻辑视图名**，配合视图解析器（ViewResolver）自动定位到实际视图文件（如 JSP、Thymeleaf 模板等）。以下是最简单的实现方式

##### 转发

**概念**

转发是**服务器内部的跳转**：客户端发送请求到服务器后，服务器内部将请求转发给另一个资源（如控制器方法或视图），整个过程客户端（浏览器）不知情，地址栏 URL 不变，且仅产生一次请求。

**用法**

通过控制器方法返回字符串，前缀为 `forward:`，后跟目标路径（可以是控制器路径或视图路径）。

```java
@Controller
public class ForwardController {
    // 1. 转发到另一个控制器方法
    @GetMapping("/forward/to/controller")
    public String forwardToController() {
        // 转发到 /user/list 控制器（由UserController的list方法处理）
        return "forward:/user/list";
    }

    // 2. 转发到视图（JSP/Thymeleaf等）
    @GetMapping("/forward/to/view")
    public String forwardToView() {
        // 转发到逻辑视图 "index"（由视图解析器定位到实际视图文件）
        return "forward:/index.jsp"; // 或直接写视图名 "forward:index"（依赖视图解析器）
    }
}
```
##### 重定向

**概念**

重定向是**服务器通知客户端重新发送请求**：服务器收到请求后，返回一个 302 状态码和新的 URL，客户端（浏览器）根据新 URL 重新发送请求，地址栏 URL 会更新为新地址，产生两次请求。

**用法**

通过控制器方法返回字符串，前缀为 `redirect:`，后跟目标路径（可以是当前应用内的路径或外部 URL）。

```java
@GetMapping("redirect")
public String redirect(){
	return "redirect:jsp/index"
}
```

转发（Forward）和重定向（Redirect）是两种不同的**页面跳转方式**，适用于不同场景，核心区别在于跳转发生的位置和浏览器的感知程度。

##### 返回json数据

- 导入json依赖

- @EnableWebMvc

  - `@ResponseBody` 用于标识控制器方法的返回值**直接作为响应体**，并自动转换为 JSON 格式（依赖 Jackson）。

  - 简化方式：`@RestController` 注解

    `@RestController` 是 `@Controller + @ResponseBody` 的组合注解，类中所有方法的返回值都会自动转为 JSON，无需再单独添加 `@ResponseBody`。

- @ResposnseBody

  - 作用：
    1. 注册默认的 **HandlerMapping** 和 **HandlerAdapter**（处理请求映射和方法调用）；
    2. 配置默认的 **消息转换器**（如处理 JSON、XML 数据的转换器）；
    3. 支持 **静态资源访问**（如 CSS、JS 文件）；
    4. 启用 **数据绑定** 和 **类型转换**（如将请求参数转为 Java 对象）；
    5. 允许开发者通过实现 `WebMvcConfigurer` 接口**自定义 MVC 配置**（如拦截器、视图解析器等）。

### RESTFul风格设计

RESTful 是一种**软件架构风格**，主要用于设计网络应用程序（尤其是基于 HTTP 的 API），核心是通过规范的 HTTP 方法和 URI 设计，实现客户端与服务器之间的高效交互。

- 如何设计路径
- 如何设计参数传递
- 如何选择请求方式

### SpringMVC扩展



## SSM

- SpringMVC框架负责控制层
- Spring框架负责整体和业务层的声明式事务管理
- MyBatis框架负责数据库访问层

### 核心问题明确

#### **SSM整合需要几个IoC容器**

​	两个容器  web容器和root容器	

1. **Root 容器（根容器）**
   - **负责管理的组件**：Service 层、Dao 层、数据源（DataSource）、MyBatis 的 SqlSessionFactory、事务管理器等**业务逻辑相关的 Bean**。
   - **配置方式**：通常通过`applicationContext.xml`（或注解配置类）定义，由`ContextLoaderListener`加载。
   - **作用范围**：整个应用生命周期，不依赖于 Web 层。
2. **Web 容器（SpringMVC 容器）**
   - **负责管理的组件**：Controller 层、视图解析器（ViewResolver）、HandlerMapping 等**Web 层相关的 Bean**。
   - **配置方式**：通常通过`spring-mvc.xml`（或注解配置类）定义，由`DispatcherServlet`加载。
   - **作用范围**：与 Servlet 生命周期绑定，仅在 Web 请求处理中生效。

**两个容器的关系：**

- **父子容器关系**：Web 容器是 Root 容器的**子容器**。

#### **需要几个配置类**

​	至少两个，一个容器一个，一般三个，对应三层架构，指定两个容器

#### **IoC初始方式和配置位置**

**实现原理与步骤**

`AbstractAnnotationConfigDispatcherServletInitializer` 本质上是 `WebApplicationInitializer` 的实现类，Web 容器（如 Tomcat）启动时会自动检测并执行其 `onStartup()` 方法，从而完成两个 IoC 容器的创建：

1. **Root 容器**：管理 Service、Dao 等业务层组件
2. **Web 容器**：管理 Controller、视图解析器等 Web 层组件

### SSM整合配置

#### 依赖整合和添加

#### 控制层配置编写（SpringMVC整合）

#### 业务层编写配置（AOP/TX整合）

#### 持久层编写配置（Mybatis整合）

#### 容器初始化配置类



## SpringBoot3

SpringBoot底层是Spring

简化开发，简化配置，简化整合，简化部署，简化监控，简化运维

### 快速入门

**1.依赖配置**

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.5.3</version>
</parent>
    <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

**2.简单程序**

```java
/**
 * Spring Boot 应用的主启动类
 * 此类是整个应用的入口点，负责启动 Spring 容器和嵌入式服务器
 */
@SpringBootApplication
// @SpringBootApplication 是一个组合注解，包含以下三个核心注解：
// 1. @Configuration：标记此类为配置类，允许在类中定义 Bean
// 2. @EnableAutoConfiguration：启用 Spring Boot 的自动配置机制，根据依赖自动配置 Bean
// 3. @ComponentScan：自动扫描当前类所在包及其子包下的组件（@Component、@Service等注解标记的类）
public class Main {  // 类名建议首字母大写，规范命名为 Main 而非 main

    /**
     * 应用程序的主方法，Java 程序的入口点
     * @param args 命令行参数，可在启动时传递（如 --server.port=8081 自定义端口）
     */
    public static void main(String[] args) {
        // 启动 Spring Boot 应用
        // SpringApplication.run() 方法会：
        // 1. 创建并初始化 Spring 应用上下文（ApplicationContext）
        // 2. 启动嵌入式 Web 服务器（如 Tomcat，根据依赖自动配置）
        // 3. 扫描并注册所有符合条件的 Bean 到容器中
        // 4. 执行自动配置流程，加载必要的组件
        SpringApplication.run(Main.class, args);
    }
}
```

### SpringBoot3配置文件

#### 统一配置管理概述

SpringBoot工程下，进行统一的配置管理，所有配置都写在一个固定位置和命名的配置文件（application.properties或者application.yml）配置文件应该放置在SpringBoot工程的src/main/resources目录下

#### 配置文件的格式与位置

Spring Boot 支持两种主要格式的配置文件，作用相同，可根据习惯选择：

##### 1. 格式类型

- **`.properties`**（键值对格式）

  properties

  ```properties
  server.port=8080
  spring.datasource.url=jdbc:mysql://localhost:3306/test
  ```

- **`.yml`**（YAML 格式，层次结构更清晰，注意缩进用空格）

  yaml

  ```yaml
  server:
    port: 8080
  spring:
    datasource:
      url: jdbc:mysql://localhost:3306/test
     
  ```
  

#### yaml文件格式

##### YAML 基本语法规则

1. **大小写敏感**
2. **缩进表示层级**：只能用空格（不能用 Tab），缩进数量不固定，但同层级必须保持一致
3. **注释**：以 `#` 开头，从 `#` 到行尾均为注释
4. **字符串**：可加引号（单引号 `'` 或双引号 `"`），也可不加（建议特殊字符时加引号）

#####  对象（键值对集合）

用缩进表示属性与值的层级关系：

##### 数组（列表）

用 `- `（减号 + 空格）表示数组元素

#### 批量配置读取

@ConfigurationProperties("prefix" = "通用前缀")

- 方便 相较于@value(${name})

- 可以给集合类型赋值

多环境配置和使用

#### 多环境配置和激活

```yml
spring:
	profiles:
		active:test,dev
```

激活外部配置 application-test|application-dev

如果外部配置的key和application key重复 外部的覆盖内部

### SpringBoot3整合SpringMVC

1.导入依赖

2.创建启动类

3.创建实体类

4.编写controller

### SpringBoot3整合Druid数据源



### SpringBoot3整合MyBatis

## MyBatisPlus

[快速掌握：全新SSM+Spring Boot+MyBatis-Plus实战精讲](https://www.wolai.com/v5Kuct5ZtPeVBk4NBUGBWF)
