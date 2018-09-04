---
layout: post
title:  "Java编程的逻辑-泛型"
date:   2018-09-03 22:39:54
tag:
- Java编程的逻辑
---

* content
{:toc}



## 8.1 基本概念和原理
代码与它们能够操作的数据类型不再绑定，同一套代码可以用于多种数据类型。

- 复用代码
- 降低耦合
- 提高代码的可读性和安全性

### 8.1.1 一个简单的泛型类

1. 基本概念

	~~~ java
	public class Pair<T> {
		T first;
		T second;
		
		public Pair(T first, T second) {
			this.first = first;
			this.second = second;
		}
		public T getFirst() {
			return first;
		}
		public T getSecond() {
			return second;
		}
	}
	~~~

	- Pair就是一个泛型类，与普通类的区别体现在

		- 类名后面多了一个<T>
		
		- first和second的类型都是T

	- T表示类型参数，泛型就是**类型参数化**，处理的数据类型不是固定的，而是可以作为参数传入

	- Pair类的代码和它处理的数据类型不是绑定的，可以是Pair\<Integer>，也可以是Pair\<String>
	- 类型参数可以有多个
	
		~~~ java
		public class Pair<U, V> {
		    private U first;
		    private V second;
		
		    public Pair(U first, V second) {
		        this.first = first;
		        this.second = second;
		    }
		    public U getFirst() {
		        return first;
		    }
		    public V getSecond() {
		        return second;
		    }
		}
		~~~

2. 基本原理

	- Java泛型是用过擦除实现的，类定义中的类型参数会被替换为Object，并插入必要的强制类型转换。
	- Java虚拟机对泛型无感知。在程序运行过程中，不知道泛型的实际类型参数，比如Pair\<Integer>，运行中只知道Pair，而不知道Intger

3. 泛型的好处

	- 更好的安全性，使用泛型Java编译时会做检查，确保类型安全。
	- 更好的可读性

	
### 8.1.2 容器类

~~~
public class DynamicArray<E> {
    private static final int DEAFAULT_CAPACITY = 10;
    private int size;
    private Object[] elementData;

    public DynamicArray() {
        this.elementData = new Object[DEAFAULT_CAPACITY];
    }

    private void ensureCapacity(int minCapacity) {
        int oldCapacity = elementData.length;
        if(oldCapacity >= minCapacity) {
            return;
        }
        int newCapacity = oldCapacity * 2;
        if(newCapacity < minCapacity)
            newCapacity = minCapacity;
        elementData =  Arrays.copyOf(elementData, newCapacity);
    }
    public void add(E e) {
        ensureCapacity(size + 1);
        elementData[size++] = e;
    }
    public E get(int index) {
        return (E)elementData[index];
    }
    public int size() {
        return size;
    }
    public E set(int index, E e) {
        E oldValue = get(index);
        elementData[index] = e;
        return oldValue;
    }
~~~

### 8.1.3 泛型方法

~~~ java
public static <T> int indexOf(T[] arr, T ele) {
	for(int i = 0; i < arr.length; i++) {
		if(arr[i].equals(ele)) {
			return i;
		}
	}
	return -1;
}
~~~

~~~ java
indexOf(new Integer[]{1, 3, 5}, 10);
~~~

与泛型类不同，调用泛型方法时一般并不需要特意指定类型参数的实际类型，Java编译器会自动推断出来。

### 8.1.4 泛型接口

~~~ java
	public interface Comparable<T> {
		public int compareTo(T o);
	}
~~~

~~~ java
	public interface Comparator<T> {
		int compare(T o1, T o2);
		boolean equals(Object obj);
	}
~~~

### 8.1.5 类型参数的限定
1. 上界为某个具体类

	定义Pair的子类NumberPair，限定两个类型参数必须为Number
	
	~~~ java
		public class NumberPair<U extends  Number, V extends Number> extends Pair<U, V> {
		    public NumberPair(U first, V second) {
		        super(first, second);
		    }
		    public double sum(){
		        return getFirst().doubleValue() + getSecond().doubleValue();
		    }
		}	
	~~~
	
	- 限定类型后，就可以使用该类型的方法了。比如，对于NumberPair中的first和second就可以当做Number进行处理了。
	- 指定边界后，类型擦除时就不会转为Object，而是会转换为它的边界类型
	
2. 上界为某个接口

	限定类型必须实现Comparable接口

	~~~ java
	public static <T extends Comparable<T>> T max(T[] arr) {
		T max = arr[0];
		for(int i = 1; i < arr.length; i++) {
			if(arr[i].compareTo(max) > 0) {
				max = arr[i]; 
			}
		}
		return max;
	}
	~~~

3. 上界为其他类型参数

	在DynamicArray容器中添加addAll方法，直观上应该如下编码。
	
	~~~ java
	public void addAll(DynamicArray<E> c) {
	    for(int i = 0; i < c.size; i++) {
	        add(c.get(i));
	    }
	}
	~~~

	这么写有一定的局限性，如下代码就会报编译错误。

	~~~ java
	DynamicArray<Number> numbers = new DynamicArray<Number>();
	DynamicArray<Integer> ints = new DynamicArray<Integer>();
	ints.add(0);
	ints.add(1);
	numbers.addAll(ints);	
	~~~ 
	
	虽然Integer是Number的子类，但是DynamicArray\<Integer>并不是DynamicArray\<Number>的子类，DynamicArray\<Integer>对象不能赋值给DynamicArray\<Number>
	
	**通过类型限定解决**
	
	~~~ java
	public <T extends E> void addAll(DynamicArray<T> c) {
		for(int i = 0; i < c.size; i++) {
			add(c.get(i));
		}
	}
	~~~

### 8.1.6 小结
- 泛型是计算机程序的一种重要思维方式，它将数据结构和算法与数据类型分离，使得同一套数据结构和算法能够用于各种各类型，而且可以保证类型安全，提高可读性。
- 泛型通过类型擦除实现，它是Java编译器的概念，Java虚拟机运行时对泛型基本一无所知。


## 8.2 通配符
### 8.2.1 更简洁的参数类型限定
~~~ java
public void addAll(DynamicArray<? extends E> c) {
	for(int i = 0; i < c.size; i++) {
		add(c.get(i));
	}
}
~~~

- 该方法**没有定义类型参数**，c的类型是DynamicArray<? extends E>，?表示通配符，<? extends E>表示有限定通配符，匹配E或E的某个子类型

- \<T extends E> vs <? extends E>

	- \<T extends E>用于定义类型参数，它声明了一个类型参数，可放在泛型类定义中类名后面、泛型方法返回值前面
	- <? extends E>用于实例化类型参数，它用于实例化泛型变量中的类型参数

	两种写法经常可以达成相同的目标：
	
	~~~ java
	public void addAll(DynamicArray<? extends E> c)
	~~~
	
	~~~ java
	public <T extends E> void addAll(DynamicArray<T> c)
	~~~
### 8.2.2 理解通配符

- 无限定通配符

	~~~ java
		public static int indexOf(DynamicArray<?> arr, Object ele) {
			for(int i = 0; i < arr.size(); i++) {
				if(arr.get(i).equals(ele)) {
					return i;
				}
			}
			return -1;
		}
	~~~
- 无限定通配符形式可以改为类型参数

	~~~ java
	public staitc <T> int indexOf(DynamicArray<T> arr, Object ele) {
	
	}
	~~~

- 通配符形式的局限性
	- 只能读，不能写
	
		~~~ java
		DynamicArray<Integer> ints = new DynamicArray<Intgeter>();
		DynamicArray<? extends Number> numbers = ints;
	 	Integer a = 200;
		numbers.add(a);//错误
		numbers.add((Number)a);//错误
		numbers.add((Object)a);//错误
		~~~
		
 		无论是Integer、Number、Object，编译器都会报错。
 	
	 	- ?表示安全类型未知。? extends Number表示是Number的某个子类型，但不知道具体子类型，如果允许写入的，Java就无法确保类型的安全，所以干脆禁止。
	 	-  大部分情况下这种限制是好的，但是使得一些理应的基本操作无法完成，比如交换两个元素的位置
 	
	 	~~~ java
	 	public static void swap(DynamicArray<?> arr, int i, int j) {
	 		Object tmp = arr.get(i);
	 		arr.set(i, arr.get(j));
	 		arr.set(j, tmp);
	 	}
	 	~~~
 	
		- 借助带类型参数的泛型方法，这个问题就可以解决
	
		~~~ java
		private static <T> void swapInternal(DynamicArray<T> arr, int i, int j) {
			T temp = arr.get(i);
			arr.set(i, arr.get(j));
			arr.set(j, temp);
		}
		
		public static void swap(DynamicArray<?> arr, int i, int j) {
			swapInternal(arr, i, j);
		}
		~~~
		
		- swap可以调用swapInternal，而带类型参数的swapInternal可以写入。
		- Java容器类中就有类似的方法，公用的API是通配符形式，内部调用带类型参数的方法

	- 参数类型之间有依赖关系，也只能用类型参数
	
		~~~ java
		public static <D, S extends D> void copy(DynamicArray<D> dest, DynamicArray<S> src) {
			for(int i = 0; i < src.size(); i++) {
				dest.add(src.get(i));
			}
		}
		~~~
	
		可以通过通配符，使得两个参数简化为一个,但是无法替代。
	
		~~~ java
		public static <D> void copy(DynamicArray<D> dest, DynamicArray<? extends D> src) {
			for(int i = 0; i < src.size(); i++) {
				dest.add(src.get(i));
			}
		}
		~~~
	
	- 如果返回值依赖于类型参数，也不能用通配符。
	
		~~~ java
		public static <T extends Comparable<T>> T max (DynamicArray<T> arr){
			T max = arr.get(0);
			for(int i = 0; i < arr.size(); i++) {
				if(arr.get(i).compareTo(max) > 0) {
					max = arr.get(i);
				}
			}
			return max;
		} 	
	~~~
	
总结：

- 通配符形式都可以通类型参数来代替，通配符能做的，用类型参数都能做。
- 通配符形式可以减少类型参数，形式上更简单，可读性也更好，因此能用通配符就用通配符
- 如果类型参数之间有依赖关系，或者返回值依赖类型参数，或者需要写操作，则只能用类型参数
- 通配符形式和类型参数往往配合使用，比如copy方法，定义必要的类型参数，使用通配符表达依赖，并接受更广泛的数据类型

### 8.2.3 超类型通配符
- <? super E>，表示E的某个父类型
- 更灵活的写入
	给DynamicArray容器增加一个copyTo方法，直观上应该如下编码
	
	~~~ java
	public void copyTo(DynamicArray<E> dest) {
		for(int i = 0; i < size; i++) {
			dest.add(get(i));
		}
	}
	~~~
	但是下面的代码会编译错误。
	
	~~~ java
	DynamicArray<Integer> ints = new DynamicArray<Integer>();
	ints.add(100);
	ints.add(34);
	DynamicArray<Number> numbers = new DynamicArray<Number>();
	ints.copyTo(numbers);
	~~~
	Integer是Number的子类，将Integer对象拷入Number容器，这种方法应该是合情合理的。此种情况，就可使用超类型通配符

	~~~ java
	public void copyTo(DynamicArray<? super E> dest) {
		for(int i = 0; i < size; i++) {
			dest.add(get(i));
		}
	}
	~~~

- 超类型通配符另一个常用的场合是Comparable/Comparator接口。

	给DynamicArray增加一个max方法，直观思考可以进行如下方法声明
	
	~~~ java
	public static <T extends Comparable<T>> T max(DynamicArray<T> arr)
	~~~
	
	此种声明方法存在一定的限制：
	
	Base实现了Comparable接口
	
	~~~ java
	class Base implements Comparable<Base> {
		private int sortOrder;
		public Base(int sortOrder) {
			this.sortOrder = sortOrder;
		}
		@Override
		public int compareTo(Base o) {
			if(sortOrder < o.sortOrder) {
				return -1;
			} else if (sortOrder > o.sortOrder) {
				return 1;
			} else {
				return 0;
			}
		}
	}
	~~~
	
	Child继承了Base，但是没有重写Base的compareTo方法
	
	~~~ java
	class Child extends Base {
		public Child(int sortOrder) {
			super(sortOrder);
		}
	}
	~~~
	调用
	
	~~~ java
	DynamicArray<Child> childs = new DynamicArray<Child>();
	childs.add(new Child(20));
	childs.add(new Child(80));
	Child maxChild = max(childs);
	~~~

	编译错误，类型不匹配。因为类型参数的限定是extends Comparable\<T>，而Child没有实现Comparable\<Child>，它实现的是Comparable\<Base>。

	修改max的声明
	
	~~~ java
	public static <T extends Comparable<? super T>> T max(DynamicArray<T> arr)
	~~~

- 类型参数限定只有extends形式，没有super形式

- 超类型通配符，无法用类型参数代替。

8.2.4 通配符比较

<?>、<? extends E>、<? super E>

- 它们的目的都是为了使方法接口更为灵活，可以接受更广泛的类型
- <? super E>用于灵活写入或比较，使得对象可以写入父类型的容器，使得父类型的比较方法可以应用于子类对象，它不可以被类型参数形式代替
- <?> 和 <? extends E>用于灵活读取，使得方法可以读取E或E的任意子类型的容器对象，它们可以用类型参数的形式代替，但是通配符的形式更为简洁


## 8.3 细节和局限性

### 8.3.1 使用泛型类、方法和接口
- 基本类型不能用于实例化类型参数
	- 解决办法是使用基本类型对应的包装类 
- 运行时类型信息不适用于泛型
	- 类型信息Class类，本身也是一个泛型类，每个类的类型对象可以通过<类名>.class引用
	- 类型对象也可以通过对象的getClass()方法引用
	
		~~~ java
		Class<?> cls = "hello".getClass();
		~~~
	- **类型对象只有一份**，与泛型无关。Java不支持如下写法。
	
		~~~ java
		Pair<Integer>.class
		~~~ 
	- 一个泛型对象getClass方法的返回值与原始类型对象是相同的

		~~~ java
		Pair<Integer> p1 = new Pair<Integer>(1, 100);
		System.out.println(Pair.class == p1.) 
		~~~
	- instanceOf是运行时判断，与泛型无关。
		- Java不支持如下写法
	
			~~~ java
			if(p1 instanceOf Pair<Integer>)
			~~~
		
		- 但是支持如下写法：

			~~~ java
			if(p1 instanceOf Pair<?>)
			~~~
- 类型擦除可能会引发一些冲突
	- 子类和父类同时实现Comparable接口，报错
	
		~~~ java
		class Base implements Comparable<Base>
		~~~
	
		~~~ java
		class Child extends Base implements Comparable<Child>
		~~~
	- 泛型的重载方法报错
	
		~~~ java
		public static void test(DynamicArray<Integer> intArr)
		public static void test(DynamicArray<String> strArr)
		~~~ 		
		
### 8.3.2 定义泛型类、方法和接口
- 不能通过类型参数创建对象
	- 非法的写法
	
	~~~ java
	T elm = new T();
	T[] arr = new T[10];
	~~~
	
	- 如何根据类型创造对象：需要设计API接受类型对象，即Class对象，并使用Java的反射机制。

	~~~ java
	public static <T> T create(Class<T> type) {
		try {
			return type.newInstance();
		} catch(Exception e) {
			return null;
		}
	}
	~~~
	
- **泛型类**类型参数不能用于静态变量和方法
	- 非法

	~~~ java
	public class Singleton<T> {
		private staitc T instance;
		public synchronized static T getInstance(){
			if(instance == null) {
				//创建实例
			}
			return instance;
		}
	}
	~~~
	
	- 对于静态方法，它可以是泛型方法，可以声明自己的类型参数，这个类型参数与泛型类的类型参数无关
- 多个限定类型的语法
 
 ~~~ java
 T extends Base & Comparable & Serializable
 ~~~
 
### 8.3.3 泛型与数组

- Java不支持创建泛型数组
	- 编译报错
	
	~~~ java
	Pair<Object, Integer>[] option = new Pair<Object, Integer>[]{
		new Pair<"1元", 7>, new Pair<"2元", 2>, new Pair<"10元", 1>
	}
	~~~
- 如果要存放泛型对象，可以使用原始类型的数组，或者使用泛型容器
	- 使用原始类型的数组：

		~~~ java
		Pair[] option = new Pair[]{
			new Pair<"1元", 7>, new Pair<"2元", 2>, new Pair<"10元", 1>
		}
		~~~ 

	- 使用泛型容器：DynamicArray\<Pair\<Object, Integer>>
- 泛型容器内部使用Object数组，如果要转换泛型容器为对应类型的数组，需要使用反射
	- 给泛型容器添加toArray()方法
		- 编译时没有错误，但是运行时会抛出ClassCastException，原因Object类型的数组无法转换为Integer类型的数组

		~~~ java
		public E[] toArray(){
			Object[] copy = new Object[size];
			System.arraycopy(elementData, 0, copy, 0, size);
			return (E[])copy;
		}
		~~~  
	
		~~~ java
		public E[] toArray(){
			return (E[])Arrays.copyOf(elementData, size);
		}
		~~~
		- 解决方案：可以利用Java中的运行时信息类型和反射机制，Java必须在运行的时候要知道转换成的数组类型
	
		~~~ java
		public E[] toArray(Class[E] type) {
			Object copy = Array.newInstance(type, size);
			System.arrayCopy(elementData, 0, copy, 0, size);
			return (E[])copy;
		}
		~~~
### 8.3.4 小结
泛型的局限性主要是由于Java泛型的实现机制引起的。

局限性主要包括：

- 不能使用基本类型
- 没有运行时类型信息
- 类型擦除会引发一些冲突
- 不同通过类型参数创建对象
- 不能用于静态变量

	 