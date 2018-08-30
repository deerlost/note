---
title: JavaWeb开发过程中常用的排序
tags: 排序,java,算法
grammar_cjkRuby: true
---
**wyoung**

# 前言 #
在web开发过程中，经常遇到排序功能。在此总结一下常用的排序方法，非常用的排序算法。主要涉及数组(Array)排序、引用数据类型数据排序、列表(List)排序、集(Set)排序、映射(Map)排序、数据库排序以及可能用到的排序算法。关于常用的排序算法，[访问此处]()。
## 数组(Array)排序 ##
### 基本数据类型数组排序 ###
基本数据类型在被创建时，在栈上给其划分一块内存，将数值直接存储在栈上。
使用Arrays类排序
Java API对Arrays类的说明是：此类包含用来操作数组（比如排序和搜索）的各种方法。
### 函数原型 ###
static void sort(int[] a)   对指定的 int 型数组按数字升序进行排序。
static void sort(int[] a, int fromIndex, int toIndex)  对指定 int 型数组的指定范围按数字升序进行排序。
### 代码范例 ###

``` java
public static void main(String[] args) {
		int[] a = { 1, 4, -1, 5, 0 };
		Arrays.sort(a);
		// 数组a[]的内容变为{-1,0,1,4,5}
		for (int i = 0; i < a.length; i++) {
			System.out.print(a[i] + "  ");
		}
	}

```
### 实现方式 ###
查看源代码发现Arrays.sort方法重载了七个基本数据类型【不含boolean】
使用DualPivotQuicksort【双轴快速排序】类的排序算法
### 说明 ###
(1)Arrays类中的sort()使用的是“经过调优的快速排序法”;
(2)比如int[]，double[]，char[]等基数据类型的数组，Arrays类之只是提供了默认的升序排列，没有提供相应的降序排列方法。
(3)要对基础类型的数组进行降序排序，需要将这些数组转化为对应的封装类数组，如Integer[]，Double[]，Character[]等，对这些类数组进行排序。(其实还不如先进行升序排序，自己在转为将序)
## 引用数据类型数据排序 ##
引用数据类型在被创建时，首先要在栈上给其引用（句柄）分配一块内存，而对象的具体信息都存储在堆内存上，然后由栈上面的引用指向堆中对象的地址。
### 函数原型 ###
(1)	public static <T> void sort(T[] a,Comparator c)
根据指定比较器产生的顺序对指定对象数组进行排序。
(2)	public static <T> void sort(T[] a,int fromIndex,int toIndex,Comparator c)
根据指定比较器产生的顺序对指定对象数组的指定范围进行排序。　
(3)	public static <T extends Comparable<? super T>> void sort(T[] a)
根据指定对象的compareTo方法对指定对象数组进行排序。　

### 代码范例[比较器] ###

``` java
比较器实现Comparator接口排序
	// 比较器，x坐标从小到大排序；x相同时，按照y从小到大排序
	class MyComprator implements Comparator<Point> {
		@Override
		public int compare(Point arg0, Point arg1) {
			Point t1 = (Point) arg0;
			Point t2 = (Point) arg1;
			if (t1.x != t2.x)
				return t1.x > t2.x ? 1 : -1;
			else
				return t1.y > t2.y ? 1 : -1;
		}
	}

	public static void main(String[] args) {
		Point[] ary = new Point[4];
		// 初始化，对象数组中的数据
		ary[0] = new Point(2, 1);
		ary[1] = new Point(2, 2);
		ary[2] = new Point(1, 2);
		ary[3] = new Point(0, 1);
		Arrays.sort(ary, new MyComprator()); // 使用指定的排序器，进行排序
		for (int i = 0; i < 4; i++) // 输出排序结果
			System.out.println("(" + ary[i].x + "," + ary[i].y + ")");
	}
```


### 代码范例[匿名内部类] ###
匿名内部类实现Comparator接口排序

``` java
public static void main(String[] args) {
		Point[] ary = new Point[4];
		// 初始化，对象数组中的数据
		ary[0] = new Point(2, 1);
		ary[1] = new Point(2, 2);
		ary[2] = new Point(1, 2);
		ary[3] = new Point(0, 1);
		Arrays.sort(ary, new Comparator<Point>() {
			@Override
			public int compare(Point arg0, Point arg1) {
				Point t1 = (Point) arg0;
				Point t2 = (Point) arg1;
				if (t1.x != t2.x)
					return t1.x > t2.x ? 1 : -1;
				else
					return t1.y > t2.y ? 1 : -1;
			}
		}); 
// 使用指定的排序器，进行排序
		for (int i = 0; i < 4; i++) // 输出排序结果
			System.out.println("(" + ary[i].x + "," + ary[i].y + ")");
	}
```


### 代码范例[自然排序] ###
引用数据类型实现Comparable接口排序

``` java
class MyPoint extends Point implements Comparable<Point> {
	public MyPoint(int x, int y) {
		this.x = x;
		this.y = y;
	}
	@Override
	public int compareTo(Point o) {
		if (x != o.x)
			return x > o.x ? 1 : -1;
		else
			return y > o.y ? 1 : -1;
	}
}

	public static void main(String[] args) {
		MyPoint[] ary = new MyPoint[4];
		// 初始化，对象数组中的数据
		ary[0] = new MyPoint(2, 1);
		ary[1] = new MyPoint(2, 2);
		ary[2] = new MyPoint(1, 2);
		ary[3] = new MyPoint(0, 1);
		Arrays.sort(ary); // 使用指定的排序器，进行排序
		for (int i = 0; i < 4; i++) // 输出排序结果
			System.out.println("(" + ary[i].x + "," + ary[i].y + ")");
	}
```


### 实现方式 ###
查看源代码发现Arrays.sort方法根据实现Comparator或Comparable接口的参数排序规则
使用legacyMergeSort【归并排序】函数或TimSort类或ComparableTimSort类的sort函数的排序算法

### 说明 ###
如果不需要复用，建议直接使用匿名内部类方式。
如果排序对象经常需要排序，可以使用比较器或者排序对象实现接口
## 列表(List)排序 ##
集合类型主要有3种：set(集）、list(列表）和map(映射)。
集合类存放的都是对象的引用，而非对象本身，所以列表只能存储引用类型
### 函数原型 ###
根据指定比较器产生的顺序对指定对象列表进行排序。
public static <T> void sort(List<T> list, Comparator<? super T> c) 
根据指定对象的compareTo方法对指定对象列表进行排序。　
public static <T extends Comparable<? super T>> void sort(List<T> list)
### 代码范例[比较器] ### 
比较器实现Comparator接口排序
	

``` java
// 比较器，x坐标从小到大排序；x相同时，按照y从小到大排序
	class MyComprator implements Comparator<Point> {
		@Override
		public int compare(Point arg0, Point arg1) {
			Point t1 = (Point) arg0;
			Point t2 = (Point) arg1;
			if (t1.x != t2.x)
				return t1.x > t2.x ? 1 : -1;
			else
				return t1.y > t2.y ? 1 : -1;
		}
	}
		public static void main(String[] args) {
		ArrayList<Point> list = new ArrayList<Point>();
		// 初始化，对象数组中的数据
		list.add(new Point(2, 1));
		list.add(new Point(2, 2));
		list.add(new Point(1, 2));
		list.add(new Point(0, 1));
		Collections.sort(list, new MyComprator()); // 使用指定的排序器，进行排序
		for (int i = 0; i < 4; i++) // 输出排序结果
			System.out.println("(" + list.get(i).x + "," + list.get(i).y + ")");
	}
```


### 代码范例[匿名内部类] ###
匿名内部类实现Comparator接口排序

``` java
public static void main(String[] args) {
		ArrayList<Point> list = new ArrayList<Point>();
		// 初始化，对象数组中的数据
		list.add(new Point(2, 1));
		list.add(new Point(2, 2));
		list.add(new Point(1, 2));
		list.add(new Point(0, 1));
		Collections.sort(list, new Comparator<Point>() {
			@Override
			public int compare(Point arg0, Point arg1) {
				Point t1 = (Point) arg0;
				Point t2 = (Point) arg1;
				if (t1.x != t2.x)
					return t1.x > t2.x ? 1 : -1;
				else
					return t1.y > t2.y ? 1 : -1;
			}
		}); // 使用指定的排序器，进行排序
		for (int i = 0; i < 4; i++) // 输出排序结果
			System.out.println("(" + list.get(i).x + "," + list.get(i).y + ")");
	}
```
	
### 代码范例[自然排序] ###
引用数据类型实现Comparable接口排序

``` java
class MyPoint extends Point implements Comparable<Point> {
	public MyPoint(int x, int y) {
		this.x = x;
		this.y = y;
	}
	@Override
	public int compareTo(Point o) {
		if (x != o.x)
			return x > o.x ? 1 : -1;
		else
			return y > o.y ? 1 : -1;
	}
}
	public static void main(String[] args) {
		ArrayList<MyPoint> list = new ArrayList<MyPoint>();
		// 初始化，对象数组中的数据
		list.add(new MyPoint(2, 1));
		list.add(new MyPoint(2, 2));
		list.add(new MyPoint(1, 2));
		list.add(new MyPoint(0, 1));
		Collections.sort(list); // 使用指定的排序器，进行排序
		for (int i = 0; i < 4; i++) // 输出排序结果
			System.out.println("(" + list.get(i).x + "," + list.get(i).y + ")");
	}
```
### 实现方式 ###
查看源代码发现Arrays.sort方法根据实现Comparator或Comparable接口的参数排序规则
使用legacyMergeSort【归并排序】函数或TimSort类或ComparableTimSort类的sort函数的排序算法
### 说明 ###
如果不需要复用，建议直接使用匿名内部类方式。
如果排序对象经常需要排序，可以使用比较器或者排序对象实现接口
## 集(Set)排序 ##
Set接口最长用的两大实现：HashSet和TreeSet
TreeSet:会将里面的元素默认排序。
### 函数原型 ###
根据指定比较器产生的顺序对TreeSet中的对象进行排序。
public TreeSet(Comparator<? super E> comparator) 
根据指定对象的compareTo方法对TreeSet中的对象进行排序。　
public <E extends Comparable<? super E>> boolean add(E t)
### 代码范例[比较器] ###

``` java
// 比较器，x坐标从小到大排序；x相同时，按照y从小到大排序
	class MyComprator implements Comparator<Point> {
		@Override
		public int compare(Point arg0, Point arg1) {
			Point t1 = (Point) arg0;
			Point t2 = (Point) arg1;
			if (t1.x != t2.x)
				return t1.x > t2.x ? 1 : -1;
			else
				return t1.y > t2.y ? 1 : -1;
		}
	}
	public static void main(String[] args) {
		TreeSet<Point> set = new TreeSet<Point>(new MyComprator());
		// 初始化，对象数组中的数据
		set.add(new Point(2, 1));
		set.add(new Point(2, 2));
		set.add(new Point(1, 2));
		set.add(new Point(0, 1));
		// 输出排序结果
		for (Point point : set) {
			System.out.println("(" + point.x + "," + point.y + ")");
		}
	}
```
	
### 代码范例[匿名内部类] ###
匿名内部类实现Comparator接口排序

``` java
public static void main(String[] args) {
		TreeSet<Point> set = new TreeSet<Point>(new Comparator<Point>() {
			@Override
			public int compare(Point arg0, Point arg1) {
				Point t1 = (Point) arg0;
				Point t2 = (Point) arg1;
				if (t1.x != t2.x)
					return t1.x > t2.x ? 1 : -1;
				else
					return t1.y > t2.y ? 1 : -1;
			}
		});
		// 初始化，对象数组中的数据
		set.add(new Point(2, 1));
		set.add(new Point(2, 2));
		set.add(new Point(1, 2));
		set.add(new Point(0, 1));
		// 输出排序结果
		for (Point point : set) {
			System.out.println("(" + point.x + "," + point.y + ")");
		}
	}
```
	
### 代码范例[自然排序] ###
引用数据类型实现Comparable接口排序

``` java
class MyPoint extends Point implements Comparable<Point> {
	public MyPoint(int x, int y) {
		this.x = x;
		this.y = y;
	}
	@Override
	public int compareTo(Point o) {
		if (x != o.x)
			return x > o.x ? 1 : -1;
		else
			return y > o.y ? 1 : -1;
	}
}

	public static void main(String[] args) {
		TreeSet<MyPoint> set = new TreeSet<MyPoint>();
		// 初始化，对象数组中的数据
		set.add(new MyPoint(2, 1));
		set.add(new MyPoint(2, 2));
		set.add(new MyPoint(1, 2));
		set.add(new MyPoint(0, 1));
		// 输出排序结果
		for (Point point : set) {
			System.out.println("(" + point.x + "," + point.y + ")");
		}
	}
```
### 实现方式 ###
使用TreeMap实现的Set有序
### 说明 ###
要么TreeSet构造方法传入比较器，要么泛型实现Comparable接口
否则抛出异常java.lang.ClassCastException
## 映射(Map)排序 ##
### 函数原型 ###
根据指定比较器产生的顺序对TreeMap中的key进行排序。
public TreeMap(Comparator<? super K> comparator) 
根据指定key的compareTo方法对TreeMap中的key进行排序。　
public V put(K key, V value) 
### 代码范例[比较器] ###
比较器实现Comparator接口排序
注意比较器必须有返回值0的情况，否则get方法无法获取到value的值

``` java
// 比较器，x坐标从小到大排序；x相同时，按照y从小到大排序
	class MyComprator implements Comparator<Point> {
		@Override
		public int compare(Point arg0, Point arg1) {
			Point t1 = (Point) arg0;
			Point t2 = (Point) arg1;
			if (t1.x != t2.x)
				return t1.x > t2.x ? 1 : -1;
			else (t1.y != t2.y)
				return t1.y > t2.y ? 1 : -1;
			else
				return 0;
		}
	}
	public static void main(String[] args) {
		TreeMap<Point, Integer> map = 
new TreeMap<Point, Integer>(new MyComprator());
		// 初始化，对象数组中的数据
		map.put(new Point(2, 1), 4);
		map.put(new Point(2, 2), 1);
		map.put(new Point(1, 2), 3);
		map.put(new Point(0, 1), 2);
		// 输出排序结果
		for (Point point : map.keySet()) {
			System.out.println("(" + point.x + "," + point.y + ")," 
+ map.get(point).intValue());
		}
	}
```
	
### 代码范例[匿名内部类] ###
匿名内部类实现Comparator接口排序

``` java
public static void main(String[] args) {
		TreeMap<Point, Integer> map = new TreeMap<Point, Integer>(
				new Comparator<Point>() {
			@Override
			public int compare(Point arg0, Point arg1) {
				Point t1 = (Point) arg0;
				Point t2 = (Point) arg1;
				if (t1.x != t2.x)
					return t1.x > t2.x ? 1 : -1;
				else if (t1.y != t2.y)
					return t1.y > t2.y ? 1 : -1;
				else
					return 0;
			}
		});
		// 初始化，对象数组中的数据
		map.put(new Point(2, 1), 4);
		map.put(new Point(2, 2), 1);
		map.put(new Point(1, 2), 3);
		map.put(new Point(0, 1), 2);
		// 输出排序结果
		for (Point point : map.keySet()) {
			System.out.println("(" + point.x + "," + point.y + ")," 
+ map.get(point).intValue());
		}
	}
```
	
### 代码范例[自然排序] ###
引用数据类型实现Comparable接口排序
注意引用数据类型的compareTo方法必须有返回值0的情况，否则get方法无法获取到value的值

``` java
class MyPoint extends Point implements Comparable<Point> {
	public MyPoint(int x, int y) {
		this.x = x;
		this.y = y;
	}
	@Override
	public int compareTo(Point o) {
		if (x != o.x)
			return x > o.x ? 1 : -1;
		else if (y != o.y)
			return y > o.y ? 1 : -1;
		else
			return 0;
	}
}
	public static void main(String[] args) {
		TreeMap<MyPoint, Integer> map = new TreeMap<MyPoint, Integer>();
		// 初始化，对象数组中的数据
		map.put(new MyPoint(2, 1), 4);
		map.put(new MyPoint(2, 2), 1);
		map.put(new MyPoint(1, 2), 3);
		map.put(new MyPoint(0, 1), 2);
		// 输出排序结果
		for (MyPoint point : map.keySet()) {
			System.out.println("(" + point.x + "," + point.y + ")," 
+ map.get(point).intValue());
		}
	}
```
### 实现方式 ###
查看源代码发现TreeMap的put方法根据红黑树数据结构构建
### 说明 ###
要么TreeMap构造方法传入比较器，要么泛型实现Comparable接口
否则抛出异常java.lang.ClassCastException

比较器的compare方法或者key的compareTo方法必须含有1，0，-1三种返回值
否则没有0，则get方法返回null
没有1和-1，排序方法无效
## 数据库排序 ##
ORDER BY [列名,列别名,列序号] [ASC, DESC];
## 相关算法 ##
### 集合类型 ###
集合类型主要有3种：set(集）、list(列表）和map(映射)。
集合类存放的都是对象的引用，而非对象本身，所以列表只能存储引用类型
集合接口分为：Collection和Map，list、set实现了Collection接口

### 单轴快速排序（SinglePivotQuickSort）
步骤如下：
如果待排序的数组项数为0或1，直接返回。(递归出口)
在待排序的数组中任选一个元素，作为中心点(pivot)。
将小于pivot的元素，大于pivot的元素划分为开来。也就是将小于中心点的元素放在中心点前面，大于中心点的元素放在中心点后面。
对前面小于pivot的元素进行快速排序，对大于pivot的元素进行快速排序。
![enter description here](https://github.com/deerlost/note/raw/master/source/img/1.png?raw=true)
 
### 双轴快速排序（DualPivotQuickSort）
双轴快速排序，顾名思义，取两个中心点pivot1，pivot2，且pivot≤pivot2，可将序列分成三段：x<pivot1、pivot1≤x≤pivot2，x<pivot2，然后分别对三段进行递归。基本过程如下图：
![enter description here](https://github.com/deerlost/note/raw/master/source/img/2.png?raw=true)
 
### 归并排序（MergeSort）
合并排序是将两个（或两个以上）有序表合并成一个新的有序表，即把待排序序列分为若干个子序列，每个子序列是有序的。然后再把有序子序列合并为整体有序序列。
合并排序是采用分治法（Divide and Conquer）的一个非常典型的应用，且各层分治递归可以同时进行。
![enter description here](https://github.com/deerlost/note/raw/master/source/img/3.png?raw=true)
 
归并排序的核心思想是将两个有序的数列合并成一个大的有序的序列。通过递归，层层合并，即为归并。
如图，从下到上，每一步都需要将两个已经有序的子数组合并成一个大的有序数组
![enter description here](https://github.com/deerlost/note/raw/master/source/img/4.png?raw=true)
 
### 红黑树（Red-Black tree）
红黑树（Red Black Tree） 是一种自平衡二叉查找树
红黑树是每个节点都带有颜色属性的二叉查找树，颜色或红色或黑色。
在二叉查找树强制一般要求以外，对于任何有效的红黑树我们增加了如下的额外要求:

性质1 节点是红色或黑色。
性质2 根节点是黑色。
性质3 每个叶节点（NIL节点，空节点）是黑色的。
性质4 每个红色节点的两个子节点都是黑色。(从每个叶子到根的所有路径上不能有两个连续的红色节点)
性质5. 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点。
#### 左旋
左旋的过程是将x的右子树绕x逆时针旋转，使得x的右子树成为x的父亲，同时修改相关节点的引用。旋转之后，二叉查找树的属性仍然满足。
![enter description here](https://github.com/deerlost/note/raw/master/source/img/5.png?raw=true)
 
#### 右旋
右旋的过程是将x的左子树绕x顺时针旋转，使得x的左子树成为x的父亲，同时修改相关节点的引用。旋转之后，二叉查找树的属性仍然满足。
![enter description here](https://github.com/deerlost/note/raw/master/source/img/6.png?raw=true)
 


