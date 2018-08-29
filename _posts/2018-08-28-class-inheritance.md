---
layout: post
title:  "Java编程的逻辑-类的继承"
date:   2018-08-22 22:39:54
tag:
- Java编程的逻辑
- 编程基础
---

* content
{:toc}


计算机程序经常使用类之间的**继承关系**来表示对象间的分类关系。

使用继承一方面可以复用代码，公共的属性和行为可以放到父类中，而子类只要关注子类特有的就可以了。

另一方面，不同子类的对象可以更为方便地被统一处理。

## 4.1 基本概念

- 每个类有且只有一个父类，没有声明父类的，其父类为Object。子类继承了父类非private的属性和方法，可以增加自己的属性和方法，以及重写父类的方法实现。
- new的过程中，父类先进行初始化。可以通过super调用父类的相应的构造方法，没有使用super的情形下，调用父类的默认构造方法。
- 子类变量和方法与父类重名的情况下，可通过super强制访问父类的变量和方法。
- 子类对象可以赋值给父类引用变量，这叫多态；实际执行调用的是子类实现，这叫动态绑定
	- 多态：一种类型的变量，可以引用多种实际类型的变量
	- 静态类型，动态类型
	- 多态和动态绑定是计算机程序的一种重要思维方式，使得操作对象的程序不需要关注对象的实际类型，从而可以统一处理不同的对象，但又能实现每个对象的特有行为。

## 4.2 继承的细节

### 4.2.1 构造方法
- 子类可以通过super调用父类的构造方法
- 如果子类没有通过super调用，则会自动调用父类的默认构造方法
- 如果父类没有默认构造方法，它的任何子类都必须在构造方法中通过super调用父类的构造方法

	~~~ java
	public class Base {
		private String member;
		public Base(String memeber) {
			this.member = member;
		}
	}
	
	public class Child extends Base {
		public Child(String member){
			super(member);
		}
	}
	~~~

- 如果在父类的构造方法中调用了可被重写的方法，是一种不好的实践，容易混淆，应该只用private方法。
	
	父类：
	
	~~~ java
	public class Base {
		public Base(){
			test();
		}
		public void test(){
		
		}
	}
	~~~
	子类：
	
	~~~ java
	public class Child extends Base {
		private int a = 123;
		public Child(){
			
		}
		
		public void test(){
			System.out.println(a);
		}
	}
	~~~
	调用：
	
	~~~ java
	public static void main(String[] args) {
		Child c = new Child();
		c.test();
	}
	
	~~~

	- 输出结果：0，123
	
	- *输出0的原因：在new过程中，首先是初始化父类，父类的构造函数调用test()方法，test()方法被子类重写，就会调用子类的test()方法，子类方法访问子类实例变量a，而这个时候子类实例变量的赋值语句还没有执行，所以输出的是其默认值0。*
	
	
### 4.2.2 重名与静态绑定
父类：

~~~ java
public class Base {
	public static String s = "static_base";
	public String m = "base";
	public static void staticTest(){
		System.out.println("base static: "+s);
	}
}
~~~
子类：

~~~ java
public class Child extends Base {
	public static String s = "child_base";
	public String m = "child";
	public static void staticTest(){
		System.out.println("child static: "+s);
	}
}
~~~
调用：

~~~ java
public static void main(String[] args) {
	Child c =  new Child();
	Base b = c;
	System.out.println(b.s);//static_base
	System.out.println(b.m);//base
	b.staticTest();//base static: static_base
	System.out.println(c.s);//child_base
	System.out.println(c.m);//child
	c.staticTest();//child static: child_base
}
~~~

- 通过b（静态类型Base）访问时，访问的是Base的变量和方法
- 通过c（静态类型Child）访问时，访问的是Child的变量和方法
- **实例变量、静态变量、静态方法、private方法静态绑定的**
- 静态绑定在程序编译节点即可决定，而动态绑定要等到程序运行时

### 4.2.3 重载和重写
重载是指方法名称相同但参数签名不同，重写是指子类重写与父类相同参数签名的方法。

- 当有多个重名函数时，在决定要调用哪个函数的过程中，首先是安装参数类型进行匹配，寻找在所有重载版本中最匹配的，然后才看变量的动态类型，进行动态绑定。

### 4.2.4 父子类型转换
- 向上转型：子类型的对象可以赋值给父类型的引用变量
- 向下转型：父类型的对象可以赋值给子类型的引用变量，能否转型成功取决于这个父类变量的动态类型是不是这个子类或者是这个子类的子类。
- instance of确保类型转换

### 4.2.5 继承访问权限protected

基类定义了表示对外行为的方法action，并定义了可以被子类重写的两个步骤step1()、step2()，以及被子类查看的变量currentStep。

~~~ java
public class Base {
	protected int currentStep;
	protected void step1(){
	
	}
	protected void step2(){
	
	}
	public void action(){
		this.currentStep = 1;
		step1();
		this.currentStep = 2;
		step2();
	}
}
~~~
子类通过重写protected方法step1()和step2()来修改对外的行为。

~~~ java
public class Child extends Base{
	protected void step1() {
		System.out.println("child step " + this.currentStep);
	}
	protected void step2() {
		System.out.println("child step " + this.currentStep);
	}
}
~~~

调用：

~~~ java
	public static void main(String[] args) {
		Child c = new Child();
		c.action();
	}
~~~

输出为：

child step 1

child step 2

- 这种思路和设计是一种设计模式，称之为**模板方法**。action方法就是一个模板方法，定义了实现的模板，具体实现由子类提供。

- 模板方法在很多框架中有广泛的应用，这是使用protected的一种常见场景。

### 4.2.6 可见性重写
- 重写时，子类方法不能降低父类方法的可见性
- 继承反映的是“is-a”的关系，即子类对象也属于父类，子类必须支持父类所有的对外行为，降低可见性就会减少子类对外的行为，从而破坏“is-a”的关系

### 4.2.7 防止继承final
- 一个Java类加了final关键字之后就不能被继承了

~~~ java
public final class Base {
	
}	
~~~
- 一个非final的类，实例方法加了final关键字之后就不能被重写了

~~~ java
public class Base {
	public final void test() {
		System.out.println("不能被重写");
	}
}
~~~

## 4.3 继承实现的基本原理
### 4.3.1 示例
~~~ java
public class Base {
	public static int s;
	private int a;
	static {
		System.out.println("基类静态代码块，s：" + s);
		s = 1;
	}
	{
		System.out.println("基类实例代码块，a：" + a);
		a = 1;
	}
	public Base(){
		System.out.println("基类构造方法，a：" + a);
		a = 2;
	}
	protected void step(){
		System.out.println("base s：" + s +"，a：" + a);
	}
	public void action(){
		System.out.println("start");
		step();
		System.out.println("end");
	}
}
~~~

~~~ java
public class Child extends Base {
	public static int s;
	private int a;
	static {
		System.out.println("子类静态代码块，s：" + s);
		s = 10;
	}
	{
		System.out.println("子类实例代码块，a：" + a);
		a = 10;
	}
	public Child(){
		System.out.println("子类构造方法，a：" + a);
		a = 20;
	}
	protected void step(){
		System.out.println("child s：" + s +"，a：" + a);
	}
	public void action(){
		System.out.println("start");
		step();
		System.out.println("end");
	}	
}
~~~

~~~ java
public static void main(String[] args) {
	System.out.println("---- new Child()");
	Child c = new Child();
	System.out.println("\n---- c.action()");
	c.action();
	Base b = c;
	System.out.println("\n---- b.action()");
	b.action();
	System.out.println("\n---- b.s：" + b.s);
	System.out.println("\n---- c.s：" + c.s);
}

~~~

### 4.3.2 类加载过程

- 类是动态加载，第一次使用的时候才会加载
- 加载一个类，会查看其父类有没有被加载，如果没有，会加载其父类
- 类的信息
	- 类变量
	- 类初始化代码
	- 类方法
	- 实例变量
	- 实例初始化代码
	- 实例方法
	- 父类信息引用
- 类初始化代码
	- 定义静态变量时的赋值语句
	- 静态初始化代码块
- 实例初始化代码
	- 定义实例变量时的赋值语句
	- 实例初始化代码块
	- 构造方法
- 类加载的过程
	- 分配内存保存类的信息
	- 给类变量赋默认值
	- 加载父类
	- 设置父子关系
	- 执行类初始化代码   

- 方法区：存放类的信息


### 4.3.3 对象创建的过程

- 分配内存
- 对所有实例变量赋默认值
- 执行实例初始化代码

每个对象除了保存类的**实例变量**之外，还保存着类的**实际类信息**的引用。

### 4.3.4 方法调用的过程

- 寻找要执行的实例方法的时候，是从对象的实际类型信息开始查找的，找不到的时候再去查找父类类型信息
- **动态绑定**实现的机制就是根据对象的实际类型查找要执行的方法，子类型中找不到的时候再查找父类
- 如果继承的层次较深，则使用**虚方法表**来优化调用的效率。
- 在类加载的时候，为每个类创建一个表，记录该类的对象所有动态绑定的方法（包括父类的方法）及其地址

### 4.3.5 变量访问的过程

对象变量的访问时静态绑定的，无论是类变量还是实例变量。

## 4.4 为什么说继承是把双刃剑

### 4.4.1 继承破坏封装

- 封装就是隐藏实现的细节，提供简化接口
- 继承可能破坏封装是因为子类和父类之间可能存在着实现细节的依赖。如果子类不知道父类方法的实现细节，就不能正确的扩展

Base提供了两个方法，add和addAll，addAll调用了add方法

~~~ java
public class Base {
	private static final int MAX_NUM = 1000;
	private int[] arr = new int[MAX_NUM];
	private int count;
	
	public void add(int number) {
		if(count < MAX_NUM) {
			arr[count++] = number;
		}
	}
	
	public void addAll(int[] numbers) {
		for(int num : numbers) {
			add(num);
		}
	}
}
~~~
子类重写了基类的add和addAll方法，在添加数字的同时汇总数字，存储汇总的值到实例变量sum中。并提供了getSum方法获取sum的值。

~~~ java
public class Child extends Base {
	private long sum;
	
	@Override
	public void add(int number) {
		super.add(number);
		sum += number;
	}
	
	@Override
	public void addAll(int[] numbers) {
		super.addAll(numbers);
		for(int i = 0; i < numbers.length; i++) {
			sum += number[i];
		}
	}	
	
	public long getSum() {
		return sum;
	}
}	
~~~
期望的输出值为6，实际却为12。这是因为子类的addAll方法首先调用了父类的addAll方法，而父类的addAll方法通过add方法添加，由于动态绑定，子类的add方法会执行，子类的add也会做汇总操作，因此同一个数字被汇总的两次。

~~~ java
public static void main(String[] args) {
	Child c =  new Child();
	c.addAll(new int[]{1,2,3});
	Systen.out.println(c.getSum);
}
~~~
如果子类不知道基类方法的实现细节，它就不能正确的进行扩展。修改addAll方法：

~~~ java
@Override
public void addAll(int[] numbers) {
	super.addAll(numbers);
}	
~~~
但是如果基类修改addAll方法的实现，子类的方法又会有错误

~~~ java
@Override
public void addAll(int[] numbers) {
	for(int num : numbers) {
		if(count < MAX_NUM) {
			arr[count++] = num;
		}
	}
}	
~~~

子类和父类之间是细节依赖，子类扩展父类，仅仅知道父类能做什么是不够的，还需要知道父类是怎么做的。而且父类的实现细节也不能随意修改，否则可能影响子类

进一步而言：

- 子类需要知道父类的可重新方法之间的依赖关系
- 而且这种依赖关系，父类不能随意改变
- 即使依赖关系不变，封装还是可能被破坏
	- 父类不能随意增加公开方法，因为父类增加就是给所有子类增加，而子类可能必须要重写该方法才能确保方法的正确性

总结：

- 对于子类而言，通过继承实现是没有安全保障的，因为父类修改内部的实现细节，它的功能就可能被破坏 
- 对于基类而言，让子类继承和重写方法，就可能丧失随意修改内部实现的自由

### 4.4.3 继承没有反映is-a关系

现实中，设计完全符合is-a关系的继承关系是困难的。

### 4.4.4 如何应对继承的双面性

1. 使用final避免继承
	- 给方法增加final修饰符，父类就保留了随意修改这个方法内部实现的自由
	- 各类加final修饰符，父类就保留了随意修改这个类实现的自由
2. 优先使用组合
	- 使用组合可以抵挡父类变化对子类的影响，从而保护子类
	- 子类对象不能当做积累对象来统一处理。解决方法：**使用接口**

3. 正确使用继承
	 