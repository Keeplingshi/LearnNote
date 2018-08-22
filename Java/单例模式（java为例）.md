### 1、什么是单例模式？
单例模式是为确保一个类只有一个实例，并为整个系统提供一个全局访问点的一种模式方法。
###### 单例的特点：
1. 在任何情况下，单例类永远只有一个实例存在
2. 单例需要有能力为整个系统提供这一唯一实例  

### 2、对单例的实现可以分为两大类——懒汉式和饿汉式
1. 懒汉式：指全局的单例实例在第一次被使用时构建。（日常中使用较多的，毕竟按需加载才能做到资源的最大化利用）
2. 饿汉式：指全局的单例实例在类装载时构建。



### 3、实现方式
#### （1）饿汉式单例
- 饿汉式单例是指在方法调用前，实例就已经创建好了。
- 线程安全 但效率比较低 。。。一上来就new对象，真够饿的。
```
public class Singleton {

	private Singleton(){

	}

	private static final Singleton Singleton = new Singleton();

	public static Singleton getInstance(){
		return Singleton;
	}
}
```
#### （2）懒汉式单例
- 懒汉式单例是指在方法调用获取实例时才创建实例，因为相对饿汉式显得“不急迫”，所以被叫做“懒汉模式”。
- 懒汉式，非线程安全 。。。等老板来叫了，才去做事，小子真懒
```
public class Singleton {

	private Singleton(){

	}

	private static Singleton Singleton = null;

	public static Singleton getInstance(){
		if(Singleton==null){
			Singleton = new Singleton();
		}
		return Singleton;
	}
}
```

#### （3）线程安全的懒汉式单例
- 要保证线程安全，我们就得需要使用同步锁机制。出现非线程安全问题，是由于多个线程可以同时进入getInstance()方法，那么只需要对该方法进行synchronized的锁同步即可。
- 懒汉式，线程安全，但效率很低 。。。99%情况下不需要同步
```
public class Singleton {

	private Singleton(){}

	private static Singleton Singleton = null;

	public static synchronized Singleton getInstance(){	//方法上加synchronized同步
		if(Singleton==null){
			Singleton = new Singleton();
		}
		return Singleton;
	}
}
```

#### （4）双检查锁机制（Double Check Locking ）（推荐）
- 为避免双重检查锁存在的问题，加入了volatile，是保证变量修改的可见性
- 这里在声明变量时使用了volatile关键字来保证其线程间的可见性；在同步代码块中使用二次检查，以保证其不被重复实例化。集合其二者，这种实现方式既保证了其高效性，也保证了其线程安全性。
```
public class Singleton {

	private Singleton(){

	}

	private static volatile Singleton Singleton = null;

	public static Singleton getInstance(){
		if(Singleton==null){
			synchronized(Singleton.class){		//加synchronized关键字同步
				if(Singleton==null){
					Singleton = new Singleton();
				}
			}
		}
		return Singleton;
	}
}
```
#### （5）使用静态内置类实现单例模式
- 这种方式利用了classloder的机制来保证初始化instance时只有一个线程。Singleton类被装载了，instance不一定被初始化，因为SingletonHolder类没有被主动使用，只有显示通过调用getInstance方法时，才会显示装载 SingletonHolder类，从而实例化instance。
- DCL解决了多线程并发下的线程安全问题，其实使用其他方式也可以达到同样的效果
```
public class Singleton {

	private Singleton(){

	}

	private static class SingletonHolder {
		private static final Singleton singleton = new Singleton();
	}

	public static final Singleton getInstance(){
		return SingletonHolder.singleton;
	}
}
```

#### 还有类似于利用枚举实现单例、序列化与反序列化的单例模式实现、使用static代码块实现单例，等方式。不再一一列举，常用的是（1）-（4）
### 参考文档

博客 | url
---|---
高并发下线程安全的单例模式（最全最经典） | https://blog.csdn.net/cselmu9/article/details/51366946
java编程题：编写一个单例模式 | https://blog.csdn.net/min996358312/article/details/67640831
设计模式 - 单例模式（懒汉、饿汉、双检锁、静态内部类） | https://www.jianshu.com/p/ca0ee2382412
