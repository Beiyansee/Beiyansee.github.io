## java 一个List给另一个list 赋值问题

> 当我们想要仅仅复制一个List的值到另一个List时
> 你也许会这样做：

```
List<String> list1 = new ArrayList<>();
List<String> list1 = new ArrayList<>();
list1=list2
```

> 我们来测试一下结果

```
List<String> list1 = new ArrayList<>();
list1.add("1");
list1.add("2");
list1.add("3");
list1.add("4");
List<String> list2 = new ArrayList<>();
list2 = list1;
System.out.println("移除第一个值前:");
System.out.println("list1:"+list1);
System.out.println("list2:"+list2);
list2.remove(list2.get(0));
System.out.println("移除第一个值后:");
System.out.println("list1:"+list1);
System.out.println("list2:"+list2);
```

> 输出结果：

```
移除第一个值前:
list1:[1, 2, 3, 4]
list2:[1, 2, 3, 4]
移除第一个值后:
list1:[2, 3, 4]
list2:[2, 3, 4]
```

> 我们可以看到对list2进行操作时list1的值也被修改了
> 其实list1只是对list2的引用，并没有重新new一个空间去存放list1的值

### 下面介绍几个仅仅复制list2值的方法
#### 1. 给list1初始化时赋值

```
List<String> list1 = new ArrayList<>();
list1.add("1");
list1.add("2");
list1.add("3");
list1.add("4");
List<String> list2 = new ArrayList<>(list1);
System.out.println("移除第一个值前:");
System.out.println("list1:"+list1);
System.out.println("list2:"+list2);
list2.remove(list2.get(0));
System.out.println("移除第一个值后:");
System.out.println("list1:"+list1);
System.out.println("list2:"+list2);
```

>输出结果

```
移除第一个值前:
list1[1, 2, 3, 4]
list2[1, 2, 3, 4]
移除第一个值后:
list1[1，2, 3, 4]
list2[2, 3, 4]
```
> 这样list1就会重新new一个空间去存放list1的值，实现数据的拷贝

#### 2. list.addAll()方法

```java
List<String> list1 = new ArrayList<>();
list1.add("1");
list1.add("2");
list1.add("3");
list1.add("4");
List<String> list2 = new ArrayList<>();
list2.addAll(list1);
System.out.println("移除第一个值前:");
System.out.println("list1:"+list1);
System.out.println("list2:"+list2);
list2.remove(list2.get(0));
System.out.println("移除第一个值后:");
System.out.println("list1:"+list1);
System.out.println("list2:"+list2);

```
>输出结果

```java
list1:[1, 2, 3, 4]
list2:[1, 2, 3, 4]
移除第一个值后:
list1:[1, 2, 3, 4]
list2:[2, 3, 4]
```