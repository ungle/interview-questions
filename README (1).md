# interview-questions

## 2023.2.5 Toppan Ecquaria

### GET 和 POST的区别是什么
- GET不能带报文，POST可以
- 都可以和服务器通讯，基于tcp
- GET幂等，post非幂等

|HTTP方法名称	 |是否幂等 |是否安全|
|--|--|--|
|OPTIONS	 |Y |	Y|
|HEAD	 |Y |	Y|
|GET	 |Y	 |Y|
|PUT	 |Y	 |N|
|DELETE	 |Y	 |N|
|POST	 |N	 |N|
|PATCH	 |N	 |N|


### JSP的隐式对象

request：获取客户端请求信息，主要用于接受http协议传送到服务器的数据  常用

response：代表客户端的响应，主要用于将jsp容器处理过的对象传送到客户端 

pageContext：页面的上下文对象，可以从中获取到当前页面的其他信息，可以从中获取到其他8个隐含对象。常用

session：代表浏览器的一次回话。常用

application：代表当前的web应用类似于一个全局变量

config：可以获取服务器的配置信息

out：输出对象，可以把字符型打印到浏览器

page：指的是当前jsp对应的servlet对象的引用

Exception：用来处理jsp文件在执行时所产生的错误和异常


### 数组寻找第一第二大的数字

``` java
int max = Integer.MIN_VALUE;
int second = Integer.MIN_VALUE;
for(int i : arr){
  if(i > max){
    second = max;
    max =i;
  }
  if(i >= second && i < max){
     second = i;
  }
}
return max+second;
```

### 数组去重,不许用java.util.*

``` java

		boolean dup = false;
		int counter = 0;
		int[] resarr = new int[arr.length];
		for (int i = 0; i < arr.length; i++) {
			dup = false;

			for (int j = 0; j < arr.length; j++) {
				if (arr[i] == arr[j] && i != j) {
					dup = true;
					break;
				}
			}

			if (!dup) {
				resarr[counter] = arr[i];
				counter++;
			}

		}

		int[] res = new int[counter];
		for (int i = 0; i < counter; i++) {
			res[i] = resarr[i];
		}
    
    return res;

```


## 2023.2.13 Nextlab

### Java的访问级别有哪些

- public--都可访问(公有)
- protected--包内和子类可访问（保护）
- 不写(default)--包内可访问 （默认）
- private--类内可访问（私有）

### Rest哪些操作是幂等(Idempotent)的

- GET
- PUT
- DELETE
- HEAD
- OPTIONS


### @Controller 和 @RestController 的区别

@RestController注解等价于@ResponseBody ＋ @Controller。@RestController和@Controller的共同点是都用来表示Spring某个类是否可以接收HTTP请求，
二者区别： @RestController无法返回指定页面，而@Controller可以；前者可以直接返回数据，后者需要@ResponseBody辅助。


### spring依赖注入是什么，由什么组件负责的？

在依赖注入中，您不必创建对象，但必须描述如何创建它们。您不是直接在代码中将组件和服务连接在一起，而是描述配置文件中哪些组件需要哪些服务。由 IoC 容器将它们装配在一起。
组件是什么？

### 面向对象四特性
- Abstraction 抽象 抽象就是忽略关注主题与当前目标无关的信息，以便更关注于与当前目标有关的信息。抽象并不打算了解全部问题，而只关注于主题相关性较大的部分，且暂时不用关注部分细节。
- Encapsulation 封装 即隐藏对象的属性和实现细节，仅对外公开接口，控制在程序中属性的读和修改的访问级别。
- Inheritance 继承 继承，即子类继承父类的特征和行为，使得子类对象（实例）具有父类的实例域和方法，或子类从父类继承方法，使得子类具有父类相同的行为。
- Polymorphism 多态 即同一个行为具有多个不同表现形式或形态的能力。表现形式为，子类重写父类方法，实现类实现接口方法，子类重写抽象类方法。


### final对象可以怎么初始化，static final对象可以怎么初始化？
- *final对象* 可以通过构造方法初始化
- *static final 对象初始化*  通过static 方法， 通过变量直接声明，通过static代码块

### Runnable 和 Thread 的区别

多线程可以通过两种方式来创建。

- 通过继承Thread类

- 通过实现Runnable接口

就是接口和类的区别：

1. Runnable的实现方式是实现其接口即可
2. Thread的实现方式是继承其类
3. Runnable接口支持多继承，但基本上用不到
4. Thread实现了Runnable接口并进行了扩展，而Thread和Runnable的实质是实现的关系，不是同类东西，所以Runnable或Thread本身没有可比性。

### 什么时候用runnable好呢

只想要实现run行为的时候


### String不可变性

- 不变性：String 是只读字符串，是一个典型的 immutable 对象，对它进行任何操作，其实都是创建一个新的对象，再把引用指向该对象。不变模式的主要作用在于当一个对象需要被多线程共享并频繁访问时，可以保证数据的一致性；

- 常量池优化：String 对象创建之后，会在字符串常量池中进行缓存，如果下次创建同样的对象时，会直接返回缓存的引用；

- final：使用 final 来定义 String 类，表示 String 类不能被继承，提高了系统的安全性。


### 受检异常和非受检异常

在异常处理的时候，都会接触到受检异常（checked exception）和非受检异常（unchecked exception）这两种异常类型。
 非受检异常指的是java.lang.RuntimeException和java.lang.Error类及其子类，所有其他的异常类都称为受检异常。两种类型的异常在作用上并没有差别。

两者的区别主要在：受检的异常是由编译器强制执行的,必须捕获，用于指示不受程序控制的异常情况（例如，I/O 错误），而非受检的异常在运行时发生，用于指示编程错误（例如，空指针。正因为如此，受检异常在使用的时候需要比非受检异常更多的代码来避免编译错误。

|uncheckedExcepiton(RuntimeException) |CheckedException|
|--|--|
|Java.lang.ArithmeticException  　 　Java.lang.ArrayStoreExcetpion  　　Java.lang.ClassCastException  　　Java.lang.EnumConstantNotPresentException  　　Java.lang.IllegalArgumentException  　　Java.lang.IllegalThreadStateException  　　Java.lang.NumberFormatException  　　Java.lang.IllegalMonitorStateException  　　Java.lang.IllegalStateException  　　Java.lang.IndexOutOfBoundsException  　　Java.lang.ArrayIndexOutOfBoundsException  　　Java.lang.StringIndexOutOfBoundsException  　　Java.lang.NegativeArraySizeException’  　　Java.lang.NullPointerException  　　Java.lang.SecurityException  　　Java.lang.TypeNotPresentException  　　Java.lang.UnsupprotedOperationException| Java.lang.ClassNotFoundException  　　Java.lang.CloneNotSupportedException  　　Java.lang.IllegalAccessException  　　Java.lang.InterruptedException  　　Java.lang.NoSuchFieldException  　　Java.lang.NoSuchMetodException|


### Arraylist 和 linkedlist 的区别和各自优势

- 是否保证线程安全： ArrayList 和 LinkedList 都是不同步的，也就是不保证线程安全；
- 底层数据结构： Arraylist 底层使用的是Object数组；LinkedList 底层使用的是双向循环链表数据结构；
- 插入和删除是否受元素位置的影响： ArrayList 采用数组存储，所以插入和删除元素的时间复杂度受元素位置的影响。 比如：执行add(E e)方法的时候， ArrayList 会默认在将指定的元素追加到此列表的末尾，这种情况时间复杂度就是O(1)。但是如果要在指定位置 i 插入和删除元素的话（add(int index, E element)）时间复杂度就为 O(n-i)。因为在进行上述操作的时候集合中第 i 和第 i 个元素之后的(n-i)个元素都要执行向后位/向前移一位的操作。 LinkedList 采用链表存储，所以插入，删除元素时间复杂度不受元素位置的影响，都是近似 O（1）而数组为近似 O（n）。
- 是否支持快速随机访问： LinkedList 不支持高效的随机元素访问，而ArrayList 实现了RandomAccess 接口，所以有随机访问功能。快速随机访问就是通过元素的序号快速获取元素对象(对应于get(int index)方法)。
- 内存空间占用： ArrayList的空 间浪费主要体现在在list列表的结尾会预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素都需要消耗比ArrayList更多的空间（因为要存放直接后继和直接前驱以及数据）。




