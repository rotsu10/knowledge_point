# java基础

## 注解

### 注解入门

- `Anootation`的作用

  不是程序本身，可以对程序做出解释

- `Anootation`的格式

  注解是以“@注释名”在代码中存在的，还可以添加一些参数值，例如：

  `@SuppressWarning(value="unchecked")`

- `Anootation`在哪里使用

  可以附加在package，class，method，field等上面，相当于给他们添加了额外的辅助信息，

  我们可以通过**反射机制**编程实现对这些元数据的访问

**可以被其它程序（比如：编译器）读取**

### 内置入门

- `@Override`

  此注释只适用于修辞方法，表示一个方法声明打算重写超累的另一个方法声明

- `@Deprecated`

  用于修辞方法，属性，类，表示不鼓励程序员使用这种元素，通常是因为它很危险或者有更好的选择

- `@SuppressWarnings`

  用来抑制编译时的警告信息。

  与前两个注释不同，需要添加一个参数才能正确使用

  1. **`unchecked`**：抑制与未检查的泛型操作相关的警告
  2. **`deprecation`**：抑制使用已过时的类或方法的警告
  3. **`serial`**：抑制当类实现 `Serializable` 接口但未定义 `serialVersionUID` 时的警告
  4. **`fallthrough`**：抑制 switch 语句中 case 块没有 break 导致的穿透警告
  5. **`path`**：抑制与类路径、源文件路径相关的警告
  6. **`finally`**：抑制 finally 块不能正常完成的警告
  7. **`rawtypes`**：抑制使用原始类型（未指定泛型参数）的警告
  8. **`unused`**：抑制未使用的变量、方法或参数的警告
  9. **`all`**：抑制所有警告（不推荐，可能掩盖重要问题）



### 元注解

**作用**

负责注解其它注解

`@Target`：用于描述注解的使用范围

`@Retention`：表示需要什么级别保存该注释信息，用于描述注解的生命周期 （即注解信息在什么阶段保留）（SOURCE>CLASS>RUNTIME）

`@Document`：说明该注解将被包含在`javadoc`中

`@Inherited`：说明子类可以继承父类中的注解

### 自定义注解

@interface自定义注解时，自动继承了`java.lang.annotation.Annotation`接口

分析：

1. @interface用来声明一个注解，格式：`public @interface 注解名{定义内容}`
2. 其中每一个方法实际上就是声明了一个参数配置
3. 方法的名称就是参数的名称
4. 返回值类型就是参数的类型（返回值只能是基本类型：`Class`，`String`，`enum`）
5. 可以通过default来声明参数的默认值
6. 如果只有一个参数成员，一般参数名为value
7. 注解元素必须要有值，我们定义注解元素时，经常使用空字符串，0作为默认值。

```java
// 用 @interface 定义注解
public @interface MyAnnotation {
    // 注解的成员（类似方法声明，但实际是注解的参数）
    String value();
    int count() default 1; // 带默认值的成员
}
```

## 反射

**优点**：可以实现动态创建对象和编译，体现很大的灵活性

**缺点**：对性能有影响，使用反射基本是一种解释操作，告诉JVM，我们希望做什么并且它满足我们的需求，这类操作总是慢于直接执行相同操作

### java反射机制概念

**Reflection**是被视为Java为动态语言的关键，反射机制允许程序在执行期借助于Reflection API取得**任何类的内部信息**，并能直接**操作**任意对象的内部属性及方法。

`Class c = Class.forName("java.lang.String")`

加载完类之后，在堆内存的方法区中就产生了一个Class类型的对象（**一个类只有一个Class对象**），这个对象就包含了完整的类的结构信息。我们可以通过这个对象看到类的结构。这个对象就像一面镜子，透过这个镜子看到类的结构，所以，我们称之为反射。

**正常方式**: **引入需要的“包类”的名称---->通过new实例化--->取得实例化的对象**

**反射方式**: **实例化对象---->`getClass()`对象---->得到完整的”包类“名称**

```java
public class Main {
    public static void main(String[] args) {
        // 实例化一个对象
        String str = new String("Hello");
        
        // 通过getClass()获取Class对象，再调用getName()得到全限定类名
        String fullClassName = str.getClass().getName();
        
        System.out.println("完整的包类名称: " + fullClassName); // 输出: java.lang.String
    }
}
```



### 理解Class类并获取Class实例

#### 1. `Class` 类

`Class` 本身是一个**类**（位于 `java.lang` 包下），它是 Java 中所有类的 “元类”。

- 它的定义形式是 `public final class Class<T> {...}`，和普通类（如 `String`、`ArrayList`）一样，有自己的字段、方法和构造器（但构造器是私有的，无法直接实例化）。
- 它的作用是**描述所有类的共性特征**，例如：所有类都有类名、父类、方法、字段等，这些共性被抽象到 `Class` 类中，表现为 `getName()`、`getSuperclass()`、`getMethods()` 等方法。

#### 2. `Class` 对象

`Class` 对象是 `Class` 类的**实例**，是具体类在 JVM 中的 “代表”。

- 当一个类（如 `String`、`User`）被加载到 JVM 时，JVM 会自动创建一个对应的 `Class` 对象，这个对象包含了该具体类的所有元信息（类名、方法、字段等）。
- 每个被加载的类（包括接口、枚举、注解等）在 JVM 中**只有一个对应的 `Class` 对象**，它是该类的 “唯一标识”。

#### 获取Class类的实例

- 若已知具体的类，通过类的class属性获取，该方法最为安全，性能最高

  `Class clazz = Person.class`

- 已知某个类的实例，调用该实例的`getClass()`方法获取Class对象

  `Class clazz = Person.getclass()`

- 已知一个类的全类名，且该类在类的路径下，可通过Class类的静态方法`forName()`获取，可能抛出`ClassNotFoundException`

  `Class clazz = Class.forName(类的全限定名)`

- 内置基本数据类型可以直接用类名.Type

  `class clazz = Integer.Type`

- 利用`ClassLoader`

### 类的加载和`ClassLoader`

#### **类的加载过程**

类的加载load---->类的链接Link---->类的初始化Initialize

**加载**：将class字节码内容加载到内存中，并将这些静态数据转化成方法区的实行时数据类型，然后生成一个代表这个类的`java.lang.Class`对象

**链接**：将Java类的二进制代码合并到JVM运行状态之中的过程

**初始化**

#### 类加载器的作用

##### 类加载器的作用

其核心作用是**将字节码文件（.class 文件）加载到 JVM 中，并生成对应的 Class 对象**，使类能够被程序使用

##### 类缓存

**类缓存（Class Caching）** 指的是 JVM 在加载类后，对生成的`Class`对象进行持久化存储，避免重复加载同一类的机制

##### 获取加载类

- 获取系统类的加载器

- 获取系统类加载器的父类加载器--->扩展类加载器

- 获取扩展类加载器的父类加载器--->根加载器

  ```java
  public class ClassLoaderDemo {
      public static void main(String[] args) {
          // 1. 获取系统类加载器
          ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
          System.out.println("系统类加载器: " + systemClassLoader);
  
          // 2. 获取扩展类加载器（系统类加载器的父类）
          ClassLoader extClassLoader = systemClassLoader.getParent();
          System.out.println("扩展类加载器: " + extClassLoader);
  
          // 3. 获取根加载器（扩展类加载器的父类）
          ClassLoader bootstrapClassLoader = extClassLoader.getParent();
          System.out.println("根加载器: " + bootstrapClassLoader); // 输出null
      }
  }
  ```



### 创建运行时类的对象

#### 获取运行时类的结构

- `Class.getDeclaredConstructors()`：获取所有构造器（包括私有）
- `Class.getDeclaredFields()`：获取所有成员变量（包括私有）
- `Class.getDeclaredMethods()`：获取所有成员方法（包括私有）
- `Modifier.toString()`：将修饰符的整数表示转换为字符串（如将 1 转换为 "public"）
- `getDeclaredXXX`与`getXXX`的区别：`getDeclaredXXX`可以获取所有访问权限的成员，而`getXXX`只能获取 public 成员

#### 动态创建对象执方法

1. **动态加载类**：
   - 使用`Class.forName("类的全限定名")`在运行时加载类，返回`Class`对象
   - 这里以`Student`类为例，实际使用时可以通过配置文件或用户输入获取类名
2. **动态创建对象**：
   - 方式一：使用`Class.newInstance()`调用类的无参构造器（要求类必须有无参构造器）
   - 方式二：通过`getConstructor(参数类型)`获取指定构造器，再用`newInstance(参数值)`创建对象
   - 两种方式都可以创建类的实例，无需在编译期知道具体类
3. **动态执行方法**：
   - 使用`getMethod(方法名, 参数类型...)`获取 public 方法
   - 使用`getDeclaredMethod(方法名, 参数类型...)`获取包括私有方法在内的所有方法
   - 通过`method.invoke(对象实例, 参数值...)`执行方法，第一个参数是调用方法的对象，后面是方法参数
   - 对于私有方法，需要先调用`setAccessible(true)`来突破访问权限限制
4. **动态操作字段**：
   - 通过反射获取字段并设置值，即使是私有字段也可以操作
   - 这使得我们可以在不调用 setter 方法的情况下修改对象状态

#### 获取泛型信息

- `getTypeParameters()`：获取类或方法声明的泛型参数（如`<T>`）
- `getGenericType()`：获取字段的泛型类型
- `getGenericParameterTypes()`：获取方法参数的泛型类型
- `getGenericReturnType()`：获取方法返回值的泛型类型
- `ParameterizedType`：表示带实际参数的泛型类型（如`List<String>`），通过它可以获取原始类型（`List`）和实际泛型参数（`String`）

## 代理

在 Java 中，**代理（Proxy）** 是一种设计模式，它通过创建一个 “代理对象” 来间接访问目标对象，从而在不修改目标对象代码的前提下，为其增加额外功能（如日志记录、权限校验、性能监控等）。

代理模式的核心思想是**分离核心业务逻辑与辅助功能**，使代码更具扩展性和可维护性。

### 代理的两种常见形式

Java 中主要有两种代理方式：

#### 1. 静态代理

- **特点**：代理类在编译期就已确定，与目标类实现同一接口。
- 实现步骤
  1. 定义一个接口（规范目标类和代理类的行为）
  2. 目标类实现接口（核心业务逻辑）
  3. 代理类实现同一接口，内部持有目标类对象，在调用目标方法前后添加额外功能

**缺点**：每一个目标类都需要对应一个代理类，代码冗余度高，不易维护。

#### 2. 动态代理

- **特点**：代理类在运行时动态生成，无需手动编写代理类代码。
- Java 中两种动态代理实现
  - **JDK 动态代理**：基于接口实现，要求目标类必须实现接口。
  - **CGLIB 动态代理**：基于继承实现，无需目标类实现接口（需要引入第三方库）。

- **核心 API**：

  - `Proxy.newProxyInstance()`：动态生成代理对象

  - `InvocationHandler`：定义代理对象的增强逻辑，所有方法调用都会转发到`invoke()`方法