# `java`泛型中`extends`和`super`的区别

## 问题复现

```java
class Fruit {}
class Apple extends Fruit {}
```

```java
class Plate<T>{
    private T item;
    public Plate(T t){item=t;}
    public void set(T t){item=t;}
    public T get(){return item;}
}
```

​		现定义一个水果盘，逻辑上可以装水果：

```java
Plate<Fruit> p = new Plate<Apple>(new Apple());
```

**此时编译无法通过。**

## 问题分析

**类型擦除**

​		`java`虚拟机会在编译时就处理泛型，例如`List<Object>和List<String>`经过虚拟机编译后，会都称为`List`无区别。在程序运行时，无法真正获取泛型的真实类型信息。

**擦除过程**

​		首先找到用来替换类型参数的具体类，一般是`Object`类型，如果指定了类型参数的上界，则使用这个上界，把代码中的类型参数替换成具体的类。

## 类型上界

```java
Plate<? extends Fruit>
```

**一个能放水果及其水果派生类的盘子**

```java
Plate<？extends Fruit> p = new Plate<Apple>(new Apple());
```

如此编译就可以通过

## 类型下界

```java
Plate <? super Fruit>
```

**一个能放水果及其水果父类的盘子**

## 上下界的副作用

**上界<? extends T>不能存只能取**：

1. 上界会使`set()`方法失效
2. `get()`取到的东西会向上转型

> ```java
> Plate<？extends Fruit> p = new Plate<Apple>(new Apple());
> ```
>
> 编译器在看到`new Plate<Apple>`赋值后，这个盘子里并没有标识这是个苹果，而是一个**标志一个占位符**，用来表示捕获了一个Fruit或者其子类，**具体不知道什么类**。所以无论插入什么数据，编译器都无法得到能否与占位符匹配到，所以不允许插入数据。

**下界<? super T>只能存，不能取**

> 因为规定下界，不清楚上界，只能转化为`Object`类型。

## 总结

1. 泛型在编译时就会进行类型擦除
2. `extends`规定类型上界，只能`get()`，不能`set()`
3. `super`规定类型下界，只能`set()`，不能`get()`