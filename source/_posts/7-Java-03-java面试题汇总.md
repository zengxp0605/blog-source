---
title: Java 面试题汇总
comments: true
date: 2019-09-02 10:57
categories: [Java]
tags: [Java]
---

## 一. Java 基础

1. JDK 和 JRE 有什么区别？
  JDK 其实包含了 JRE，同时还包含了编译 java 源码的编译器 javac，还包含了很多 java 程序调试和分析的工具。
  简单来说：运行 java 程序，只需安装 JRE 就可以了，如果需要编写 java 程序，需要安装 JDK。

2. == 和 equals 的区别是什么？
  对于基本类型和引用类型 == 的作用效果是不同的，如下所示：
  基本类型：比较的是值是否相同；
  引用类型：比较的是引用是否相同；

  总结：== 对于基本类型来说是值比较，对于引用类型来说是比较的是引用；而 equals 默认情况下是引用比较，只是很多类重新了 equals 方法，比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。

3. 两个对象的 hashCode()相同，则 equals()也一定为 true，对吗？
  不一定。同时反过来equals为true，hashCode也不一定相同。
  类的hashCode方法和equals方法都可以重写，返回的值完全在于自己定义。

  Java对于eqauls方法和hashCode方法是这样规定的
    - 如果两个对象相同，那么它们的hashCode值一定要相同；
    - 如果两个对象的hashCode相同，它们并不一定相同（这里说的对象相同指的是用eqauls方法比较），如不按要求去做了，会发现相同的对象可以出现在Set集合中，同时，增加新元素的效率会大大下降。
    - equals()相等的两个对象，hashcode()一定相等；equals()不相等的两个对象，却并不能证明他们的hashcode()不相等。
      换句话说，equals()方法不相等的两个对象，hashcode()有可能相等。反过来，hashcode()不等，一定能推出equals()也不等；hashcode()相等，equals()可能相等，也可能不等。

4. final 在 java 中有什么作用？
  - final 修饰的类叫最终类，该类不能被继承。
  - final 修饰的方法不能被重写。
  - final 修饰的变量叫常量，常量必须初始化，初始化之后值就不能被修改

5. java 中的 Math.round(-1.5) 等于多少？
  ```java
  Math.round(-1.5) = -1
  Math.round(-1.6) = -2
  Math.round(1.5) = 2
  ```
  注：四舍六入五成双:
  当有效位数确定后，其后面多余的数字应该舍去，只保留有效数字最末一位，这种修约（舍入）规则是“四舍六入五成双”，也即“4舍6入5凑偶”这里“四”是指≤4 时舍去，”六”是指≥6时进上，”五”指的是根据5后面的数字来定，当5后有数时，舍5入1；当5后无有效数字时，需要分两种情况来讲：①5前为奇数，舍5入1；②5前为偶数，舍5不进。（0是偶数）

<!-- more -->
6. String 属于基础的数据类型吗？
  String不是基本数据类型，而是一个类（class）
  ```
  Java的8大基本数据类型分别是：

  整数类 byte, short, int, long

  浮点类 double, float

  逻辑类 boolean

  文本类 char
  ```

7. java 中操作字符串都有哪些类？它们之间有什么区别？
  String、StringBuffer、StringBuilder

  - String : final修饰，String类的方法都是返回new String。即对String对象的任何改变都不影响到原对象，对字符串的修改操作都会生成新的对象。
  - StringBuffer : 对字符串的操作的方法都加了synchronized，保证线程安全。
  - StringBuilder : 不保证线程安全，在方法体内需要进行字符串的修改操作，可以new StringBuilder对象，调用StringBuilder对象的append、replace、delete等方法修改字符串。

  > String对象真的不可变吗？
  - String的成员变量是private final 的，也就是初始化之后不可改变
  - 但是可以用**反射**实现，反射出String对象中的value属性， 进而改变通过获得的value引用改变数组的结构 

8. String str="i"与 String str=new String(“i”)一样吗？

9. 如何将字符串反转？
```java
  String str = "zouzou-lover";
  String reverseStr = new StringBuffer(str).reverse().toString();
```

10. String 类的常用方法都有那些？


11. 抽象类必须要有抽象方法吗？
  抽象类中可以没有抽象方法，但有抽象方法的一定是抽象类，比如常见的有HttpServlet类。
  但是抽象类是不能被实例化的，即使它没有抽象方法

12. 普通类,抽象类, 接口有哪些区别？
  - 包含抽象方法的类称为抽象类，但并不意味着抽象类中只能有抽象方法，它和普通类一样，同样可以拥有成员变量和普通的成员方法。注意，抽象类和普通类的主要有三点区别：
  1）抽象方法必须为public或者protected（因为如果为private，则不能被子类继承，子类便无法实现该方法），缺省情况下默认为public。
  2）普通类可以去实例化调用；抽象类不能被实例化，因为它是存在于一种概念而不非具体
  3）如果一个类继承于一个抽象类，则子类必须实现父类的抽象方法。如果子类没有实现父类的抽象方法，则必须将子类也定义为为abstract类。

  - 抽象类与接口
  1) 接口是对动作的抽象，抽象类是对本质的抽象。
  2) 抽象类 和 接口 都是用来抽象具体对象的，但是接口的抽象级别最高
  3) 抽象类可以有具体的方法和属性,  接口只能有抽象方法和不可变常量(final)
  > 当你关注一个事物的本质的时候，用抽象类；  
  > 当你关注一个操作的时候，用接口。 


13. 抽象类能使用 final 修饰吗？
不能，抽象类是被用于继承的，final修饰代表不可修改、不可继承的。

14. 接口和抽象类有什么区别？
参见12

15. java 中 IO 流分为几种？
Java中的流分为两种，一种是字节流，另一种是字符流，分别由四个抽象类来表示（每种流包括输入和输出两种所以一共四个）:InputStream，OutputStream，Reader，Writer

16. BIO、NIO、AIO 有什么区别？
- BIO：Block IO 同步阻塞式 IO，就是我们平常使用的传统 IO，它的特点是模式简单使用方便，并发处理能力低。
- NIO：New IO 同步非阻塞 IO，是传统 IO 的升级，客户端和服务器端通过 Channel（通道）通讯，实现了多路复用。
- AIO：Asynchronous IO 是 NIO 的升级，也叫 NIO2，实现了异步非堵塞 IO ，异步 IO 的操作基于事件和回调机制。


17. Files的常用方法都有哪些？
- Files.exists()  // 检测文件路径是否存在。
- Files.createFile()  // 创建文件。
- Files.createDirectory()  // 创建文件夹。
- Files.delete()  // 删除一个文件或目录。
- Files.copy()  // 复制文件。
- Files.move()  // 移动文件。
- Files.size()  // 查看文件个数。
- Files.read()  // 读取文件。
- Files.write()  // 写入文件。
- Files.list() // 列出该文件夹下所有子文件、子文件夹的路径。
        

<!-- more -->


## 二. 容器

18. java 容器都有哪些？
- Java 集合框架主要包括两种类型的容器，一种是集合（Collection），存储一个元素集合，另一种是图（Map），存储键/值对映射
|Collection
|　　├List
|　　│-├LinkedList
|　　│-├ArrayList
|　　│-└Vector
|　　│　└Stack
|　　├Set
|　　│├HashSet
|　　│├TreeSet
|　　│└LinkedSet
|
|Map
　　├Hashtable
　　├HashMap
　　└WeakHashMap


19. Collection 和 Collections 有什么区别？
- java.util.Collection 是一个集合接口。它提供了对集合对象进行基本操作的通用接口方法。Collection接口在Java 类库中有很多具体的实现。Collection接口的意义是为各种具体的集合提供了最大化的统一操作方式。
- java.util.Collections 是一个包装类。它包含有各种有关集合操作的静态多态方法。此类不能实例化，就像一个工具类，服务于Java的Collection框架。

20. List、Set、Map 之间的区别是什么？
- List,Set都是继承自Collection接口，Map则不是

- List特点：元素有放入顺序，元素可重复 ，Set特点：元素无放入顺序，元素不可重复，重复元素会覆盖掉，（注意：元素虽然无放入顺序，但是元素在set中的位置是有该元素的HashCode决定的，其位置其实是固定的，加入Set 的Object必须定义equals()方法 ，另外list支持for循环，也就是通过下标来遍历，也可以用迭代器，但是set只能用迭代，因为他无序，无法用下标来取得想要的值。） 

- Set和List对比： 
Set：检索元素效率低下，删除和插入效率高，插入和删除不会引起元素位置改变。 
List：和数组类似，List可以动态增长，查找元素效率高，插入删除元素效率低，因为会引起其他元素位置改变。 

- Map适合储存键值对的数据

- 线程安全集合类与非线程安全集合类 
LinkedList、ArrayList、HashSet是非线程安全的，Vector是线程安全的;
HashMap是非线程安全的，HashTable是线程安全的;
StringBuilder是非线程安全的，StringBuffer是线程安全的。


21. HashMap 和 Hashtable 有什么区别？
- HashMap允许将 null 作为一个 entry 的 key 或者 value，而 Hashtable 不允许。
- HashMap 把 Hashtable 的 contains 方法去掉了，改成 containsValue 和 containsKey。因为 contains 方法容易让人引起误解。
- HashTable 继承自 Dictionary 类，而 HashMap 是 Java1.2 引进的 Map interface 的一个实现。
- HashTable 的方法是 Synchronize 的，而 HashMap 不是，在多个线程访问 Hashtable 时，不需要自己为它的方法实现同步，而 HashMap 就必须为之提供外同步。


22. 如何决定使用 HashMap 还是 TreeMap？
简单来说，需要排序就用TreeMap，不需排序则使用 HashMap

23. 说一下 HashMap 的实现原理？

24. 说一下 HashSet 的实现原理？

25. ArrayList 和 LinkedList 的区别是什么？

26. 如何实现数组和 List 之间的转换？

27. ArrayList 和 Vector 的区别是什么？

28. Array 和 ArrayList 有何区别？
Array：数组，最高效；但是其容量固定且无法动态改变，只能存储同一类型的数据
ArrayList：集合，容量可动态增长；但牺牲效率；需要先申明，然后再添加数据，长度是根据内容的多少而改变的，ArrayList可以存放不同类型的数据，在存储基本类型数据的时候要使用基本数据类型的包装类

29. 在 Queue 中 poll()和 remove()有什么区别？

30. 哪些集合类是线程安全的？
- 早期的线程安全的集合说起，它们是Vector (~Arraylist)和HashTable (~Hashmap), 因为性能原因被弃用  

- Collections包装方法
```java
List<E> synArrayList = Collections.synchronizedList(new ArrayList<E>());

Set<E> synHashSet = Collections.synchronizedSet(new HashSet<E>());

Map<K,V> synHashMap = Collections.synchronizedMap(new HashMap<K,V>());

```

- java.util.concurrent包中的集合
  - ConcurrentHashMap和HashTable都是线程安全的集合，它们的不同主要是加锁粒度上的不同    
  - CopyOnWriteArrayList和CopyOnWriteArraySet, 它们是加了写锁的ArrayList和ArraySet，锁住的是整个对象，但读操作可以并发执行  
  - ConcurrentSkipListMap、ConcurrentSkipListSet、ConcurrentLinkedQueue、ConcurrentLinkedDeque  


31. 迭代器 Iterator 是什么？

32. Iterator 怎么使用？有什么特点？

33. Iterator 和 ListIterator 有什么区别？


34. 怎么确保一个集合不能被修改？
```java
    private static Map<Integer, String> map = new HashMap<>();

    static {
        map.put(1, "one");
        map.put(2, "two");
        map = Collections.unmodifiableMap(map);
    }
```

- Guava提供的类可实现的不可变对象


## 三. 多线程

35. 并行和并发有什么区别？

36. 线程和进程的区别？

37. 守护线程是什么？

38. 创建线程有哪几种方式？

39. 说一下 runnable 和 callable 有什么区别？

40. 线程有哪些状态？

41. sleep() 和 wait() 有什么区别？

42. notify()和 notifyAll()有什么区别？

43. 线程的 run()和 start()有什么区别？

44. 创建线程池有哪几种方式？

45. 线程池都有哪些状态？

46. 线程池中 submit()和 execute()方法有什么区别？

47. 在 java 程序中怎么保证多线程的运行安全？

48. 多线程锁的升级原理是什么？

49. 什么是死锁？

50. 怎么防止死锁？

51. ThreadLocal 是什么？有哪些使用场景？

52. 说一下 synchronized 底层实现原理？

53. synchronized 和 volatile 的区别是什么？

54. synchronized 和 Lock 有什么区别？

55. synchronized 和 ReentrantLock 区别是什么？

56. 说一下 atomic 的原理？

## 四. 反射

57. 什么是反射？
  反射就是在运行时才知道要操作的类是什么，并且可以在运行时获取类的完整构造，并调用对应的方法。  
  `java.lang.reflect`包下
    - Class类：代表一个类，位于java.lang包下。
    - Field类：代表类的成员变量（成员变量也称为类的属性）。
    - Method类：代表类的方法。
    - Constructor类：代表类的构造方法。
    - Array类：提供了动态创建数组，以及访问数组的元素的静态方法。
    ```java
        Class clz = Class.forName("com.jason.basejava.reflectTest.model.Apple");
        Constructor constructor = clz.getConstructor(int.class, String.class);
        Object appleObj = constructor.newInstance(21,"red");

        // getFields() 方法可以获取 Class 类的属性，但无法获取私有属性
        Field[] allFields = clz.getDeclaredFields();
    ```

  参考： [大白话说Java反射：入门、使用、原理](http://cnblogs.com/chanshuyi/p/head_first_of_reflection.html)

58. 什么是 java 序列化？什么情况下需要序列化？
  序列化：将 Java 对象转换成字节流的过程。
  反序列化：将字节流转换成 Java 对象的过程。
  当 Java 对象需要在网络上传输 或者 持久化存储到文件中时，就需要对 Java 对象进行序列化处理。

59. 动态代理是什么？有哪些应用？
  我们根据加载被代理类的时机不同，将代理分为静态代理和动态代理。如果我们在代码编译时就确定了被代理的类是哪一个，那么就可以直接使用静态代理；
  如果不能确定，那么可以使用类的动态加载机制，在代码运行期间加载被代理的类这就是动态代理，比如RPC框架和Spring AOP机制。

  静态代理的缺点: 代理对象的一个接口只能服务于一种类型的对象,每个类型都需要一个代理类,会使代理类的数量膨胀;

  动态代理可以解决上述静态代理的缺点, 动态代理代理类不需要显式的实现被代理类所实现的接口

  动态代理类优缺点
  优点:
  1.动态代理类的字节码在程序运行时由Java反射机制动态生成，无需程序员手工编写它的源代码。
  2.动态代理类不仅简化了编程工作，而且提高了软件系统的可扩展性，因为Java 反射机制可以生成任意类型的动态代理类。

  缺点:
  JDK的动态代理机制只能代理实现了接口的类，而不能实现接口的类就不能实现JDK的动态代理，cglib是针对类来实现代理的，他的原理是对指定的目标类生成一个子类，并覆盖其中方法实现增强，但因为采用的是继承，所以不能对final修饰的类进行代理。

  > Javassist是一个开源的分析、编辑和创建Java字节码的类库。是由东京工业大学的数学和计算机科学系的 Shigeru Chiba （千叶 滋）所创建的。它已加入了开放源代码JBoss 应用服务器项目,通过使用Javassist对字节码操作为JBoss实现动态AOP框架。javassist是jboss的一个子项目，其主要的优点，在于简单，而且快速。直接使用java编码的形式，而不需要了解虚拟机指令，就能动态改变类的结构，或者动态生成类。

60. 怎么实现动态代理？

 - JDK动态代理实现
```java
package test;

import java.lang.reflect.InvocationHandler;  
import java.lang.reflect.Method;  
import java.lang.reflect.Proxy;  

public class ProxyHandler implements InvocationHandler
{
    private Object target;

    //绑定委托对象，并返回代理类
    public Object bind(Object target)
    {
        this.target = target;
        //绑定该类实现的所有接口，取得代理类 
        return Proxy.newProxyInstance(target.getClass().getClassLoader(),
                                      target.getClass().getInterfaces(),
                                      this);
    }    

    public Object invoke(Object proxy , Method method , Object[] args)throws Throwable
    {
        Object result = null;
        //这里就可以进行所谓的AOP编程了
        System.out.println( "before doSomething, methodName: " + method.getName());   

        result = method.invoke(target, args);
        
        System.out.println( "after doSomething, result: " + result);   

        return result;
    }
}
```

```java
package test;

public interface Subject   
{   
  public void doSomething();   
}
```

```java
package test;

public class RealSubject implements Subject   
{   
  public void doSomething()   
  {   
    System.out.println( "call doSomething()" );   
  }   
}
```

```java
public class TestProxy{
    public static void main(String args[]){
        ProxyHandler proxy = new ProxyHandler();
        //绑定该类实现的所有接口
        Subject sub = (Subject) proxy.bind(new RealSubject());
        sub.doSomething();
    }
}
```

- 利用`cglib`实现动态代理
> cglib是一种基于ASM的字节码生成库，用于生成和转换Java字节码.  

- 利用`javassist`实现
> 提供者动态代理接口慢,但是生成字节码比较快

- 几种方式生成字节码的性能对比: <https://www.iteye.com/blog/javatar-814426>

## 五. 对象拷贝

61. 为什么要使用克隆？
我们常见的`Object a=new Object();Object b;b=a;`这种形式的代码复制的是引用，即对象在内存中的地址，a和b对象仍然指向了同一个內存地址的同一对象。而通过clone方法赋值的对象跟原来的对象时同时独立存在的;

克隆的对象可能包含一些已经修改过的属性，而new出来的对象的属性都还是初始化时候的值，所以当需要一个新的对象来保存当前对象的“状态”就靠clone方法了


62. 如何实现对象克隆？
  1). 实现Cloneable接口并重写Object类中的clone()方法；

  2). 实现Serializable接口，通过对象的序列化和反序列化实现克隆，可以实现真正的深度克隆。可以解决多层克隆问题

63. 深拷贝和浅拷贝区别是什么？
两种不同的克隆方法，浅克隆(ShallowClone)和深克隆(DeepClone)。

在Java语言中，数据类型分为值类型（基本数据类型）和引用类型，值类型包括int、double、byte、boolean、char等简单数据类型，引用类型包括类、接口、数组等复杂类型。浅克隆和深克隆的主要区别在于是否支持引用类型的成员变量的复制

- 在浅克隆中，如果原型对象的成员变量是值类型，将复制一份给克隆对象；如果原型对象的成员变量是引用类型，则将引用对象的地址复制一份给克隆对象，也就是说原型对象和克隆对象的成员变量指向相同的内存地址。 简单来说，在浅克隆中，当对象被复制时只复制它本身和其中包含的值类型的成员变量，而引用类型的成员对象并没有复制。

- 在深克隆中，无论原型对象的成员变量是值类型还是引用类型，都将复制一份给克隆对象，深克隆将原型对象的所有引用对象也复制一份给克隆对象。简单来说，在深克隆中，除了对象本身被复制外，对象所包含的所有成员变量也将复制。

## 六. Java Web
64. jsp 和 servlet 有什么区别？

65. jsp 有哪些内置对象？作用分别是什么？

66. 说一下 jsp 的 4 种作用域？

67. session 和 cookie 有什么区别？

68. 说一下 session 的工作原理？

69. 如果客户端禁止 cookie 能实现 session 还能用吗？

70. spring mvc 和 struts 的区别是什么？
- Struts2是类级别的拦截， 一个类对应一个request上下文，SpringMVC是方法级别的拦截，一个方法对应一个request上下文
- 拦截器实现机制上，Struts2有以自己的interceptor机制，SpringMVC用的是独立的AOP方式
- SpringMVC的入口是servlet，而Struts2是filter（这里要指出，filter和servlet是不同的。以前认为filter是servlet的一种特殊），这就导致了二者的机制不同，这里就牵涉到servlet和filter的区别了
- SpringMVC集成了Ajax，使用非常方便，只需一个注解@ResponseBody就可以实现，然后直接返回响应文本即可，而Struts2拦截器集成了Ajax，在Action中处理时一般必须安装插件或者自己写代码集成进去，使用起来也相对不方便

71. 如何避免 sql 注入？

72. 什么是 XSS 攻击，如何避免？
- XSS 攻击，即跨站脚本攻击（Cross Site Scripting），它是 web 程序中常见的漏洞。 原理是攻击者往 web 页面里插入恶意的脚本代码（CSS代码、JavaScript代码等），当用户浏览该页面时，嵌入其中的脚本代码会被执行，从而达到恶意攻击用户的目的。如盗取用户cookie，破坏页面结构、重定向到其他网站等。
- 预防: 永远不要相信用户的输入，必须对输入的数据作过滤处理。对敏感字符进行转义(如 `<script>`标签的处理)
- HTTP-only Cookie: 禁止 JavaScript 读取某些敏感 Cookie，攻击者完成 XSS 注入后也无法窃取此 Cookie
首先是过滤。对诸如<script>、<img>、<a>等标签进行过滤。
其次是编码。像一些常见的符号，如<>在输入的时候要对其进行转换编码，这样做浏览器是不会对该标签进行解释执行的，同时也不影响显示效果。
最后是限制。通过以上的案例我们不难发现xss攻击要能达成往往需要较长的字符串，因此对于一些可以预期的输入可以通过限制长度强制截断来进行防御。


73. 什么是 CSRF 攻击，如何避免？
- CSRF（Cross-site request forgery）跨站请求伪造：攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

- Token是一个比较有效的CSRF防护方法，只要页面没有XSS漏洞泄露Token，那么接口的CSRF攻击就无法成功。
- 验证码和密码其实也可以起到CSRF Token的作用哦，而且更安全。


## 七. 异常

74. throw 和 throws 的区别？
- 区别一
throw 是语句抛出一个异常；throws 是方法抛出一个异常；

  throw语法：throw <异常对象>

  在方法声明中，添加throws子句表示该方法将抛出异常。

  throws语法：[<修饰符>]<返回值类型><方法名>（[<参数列表>]）[throws<异常类>]

  其中：异常类可以声明多个，用逗号分割。

- 区别二：

  throws可以单独使用，但throw不能；

- 区别三：

  throw要么和try-catch-finally语句配套使用，要么与throws配套使用。但throws可以单独使 用，然后再由处理异常的方法捕获。

```java

void doSomething() throws Exception1, Exception3 {
  try {
    // statement
  } catch(Exception1 e) {
    throw e;
  } catch(Exception2 e) {
    System.out.println("出错了");
  }
  
  if (a != b){
    throw new Exception3("自定义异常");
  }
}
```


75. final、finally、finalize 有什么区别？

- final 用于声明属性,方法和类, 分别表示属性不可变, 方法不可覆盖, 类不可继承.
- finally 是异常处理语句结构的一部分，表示总是执行.
- finalize 是Object类的一个方法，在垃圾收集器执行的时候会调用被回收对象的此方法，可以覆盖此方法提供垃圾收集时的其他资源回收，例如关闭文件等. JVM不保证此方法总被调用.


76. try-catch-finally 中哪个部分可以省略？

try语句后面是可以省略catch语句的，但是必须有finally语句。也可以省略finally语句，但是必须要有catch语句。也就是说try语句后面必须要有一个别的语句跟在后面。
有如下三种：
```
try-catch
try-finally
try-catch-finally
```

切记：catch和finally语句不能同时省略


77. try-catch-finally 中，如果 catch 中 return 了，finally 还会执行吗？
finally块中的内容会先于try中的return语句执行，如果finall语句块中也有return语句的话，那么直接从finally中返回了，这也是不建议在finally中return的原因

对于含有return语句的情况，这里我们可以简单地总结如下：

    try语句在返回前，将其他所有的操作执行完，保留好要返回的值，而后转入执行finally中的语句，而后分为以下三种情况：

    情况一：如果finally中有return语句，则会将try中的return语句”覆盖“掉，直接执行finally中的return语句，得到返回值，这样便无法得到try之前保留好的返回值。

    情况二：如果finally中没有return语句，也没有改变要返回值，则执行完finally中的语句后，会接着执行try中的return语句，返回之前保留的值。

    情况三：如果finally中没有return语句，但是改变了要返回的值，这里有点类似与引用传递和值传递的区别，分以下两种情况，：

        1）如果return的数据是基本数据类型或文本字符串，则在finally中对该基本数据的改变不起作用，try中的return语句依然会返回进入finally块之前保留的值。

        2）如果return的数据是引用数据类型，而在finally中对该引用数据类型的属性值的改变起作用，try中的return语句返回的就是在finally中改变后的该属性的值。



78. 常见的异常类有哪些？
- java.lang.NullPointerException 空指针异常
　 
- java.lang.ClassNotFoundException　指定的类不存在

- java.lang.NumberFormatException 字符串转换为数字异常

- java.lang.IndexOutOfBoundsException 数组下标越界异常

- java.lang.IllegalArgumentException  方法的参数错误

- java.lang.IllegalAccessException  没有访问权限

- java.lang.ArithmeticException  数学运算异常

- java.lang.ClassCastException  数据类型转换异常

- java.lang.FileNotFoundException  文件未找到异常

- java.lang.ArrayStoreException  数组存储异常

- java.lang.NoSuchMethodException  方法不存在异常

- java.lang.NoSuchFiledException  方法不存在异常

- java.lang.EOFException  文件已结束异常

- java.lang.InstantiationException  实例化异常

- java.lang.InterruptedException  被中止异常

- java.lang.CloneNotSupportedException  不支持克隆异常

- java.lang.OutOfMemoryException  内存不足错误

- java.lang.NoClassDefFoundException  未找到类定义错误

违背安全原则异常：SecturityException

操作数据库异常：SQLException

输入输出异常：IOException

通信异常：SocketException
> <https://www.cnblogs.com/ITtangtang/archive/2012/04/22/2465382.html>

## 八. 网络

79. http 响应码 301 和 302 代表的是什么？有什么区别？

80. forward 和 redirect 的区别？

81. 简述 tcp 和 udp的区别？

82. tcp 为什么要三次握手，两次不行吗？为什么？

83. 说一下 tcp 粘包是怎么产生的？

84. OSI 的七层模型都有哪些？
物理层,链路层,网络层, 传输层,会话层,表示层,应用层

85. get 和 post 请求有哪些区别？

86. 如何实现跨域？

87. 说一下 JSONP 实现原理？

## 九. 设计模式

88. 说一下你熟悉的设计模式？

89. 简单工厂和抽象工厂有什么区别？



## 参考
> [Java 最常见的 200+ 面试题：面试必备](https://blog.csdn.net/sufu1065/article/details/88051083)  
> [Java 面试笔记](https://dongchuan.gitbooks.io/java-interview-question/)
> <https://juejin.im/post/5c7cfdef6fb9a049f746ed47>  
> [Java提高篇——对象克隆（复制）](https://www.cnblogs.com/Qian123/p/5710533.html)

