# Java 8 升Java 11 重要特性必读

## 语言新特性

### JDK9 - 允许在接口中使用私有方法

在如下代码中，buildMessage 是接口 SayHi 中的私有方法，在默认 sayHi 中被使用

```java
public interface SayHi {
	private String buildMessage() {
		return "hello";
	}
	void sayHi(final String message);
	default void sayHi() {
		sayHi(buildMessage());
	}
}
```

### JDK10 - 局部变量类型推断

局部变量类型推断是 Java10 中最值得开发人员注意的新特性，这是Java语言开发人员为了简化Java应用程序的编写而进行的又一重要改进

```java
// Java5 引入泛型
List<String> list = new ArrayList<String>();
Stream<String> stream = getStream();

// Java7 泛型<>操作符允许不绑定类型初始化List
List<String> list = new LinkedList<>();
Stream<String> stream = getStream();

// Java10 局部变量类型推断
var list = new ArrayList<String>(); // ArrayList<String>
var stream = list.stream(); // Stream<String>
```

### JDK11 - 用于 Lambda 参数的局部变量语法

在 Lambda 表达式中使用局部变量类型推断是 Java11 引入的唯一与语言相关的特性

可以使用关键字 var 声明局部变量，如下所示

```java
var s = "hello java11";
String.out.println(s);
```

但是在 Java10 中，还有下面几个限制

* 只能用于局部变量上
* 声明时必须初始化
* 不能用作方法参数
* 不能再 Lambda 表达式中使用

Java11 与 Java10 的不同指出在于允许开发者在 Lambda 表达式中使用var进行参数声明。

Lambda 表达式使用隐式类型定义，它形参的所有类型全部靠推断出来的。隐式类型 Lambda 表达式如下

``` java
(x, y) -> x.process(y)
```

Java 10 为局部变量提供隐式定义写法如下

``` java
var x = new Foo();
for (var x : xs) {...}
try (var x = ...) {...} catch ...
```

为了 Lambda 类型表达式中正式参数定义的语法与局部变量定义语法的不一致，且为了保持与其他局部变量用法上的一致性，希望能够使用关键字 var 隐式定义 Lambda 表达式的形参

```java
(var x, var y) -> x.process(y)
```

于是在 Java11 中将局部变量和 Lambda 表达式的用法进行了统一，并且可以将注释应用与局部变量和 Lambda 表达式

```java
@Nonnull var x = new Foo();
(@Nonnull var x, @Nullable var y) -> x.process(y)
```

## 新工具和库更新

#JDK9 - 集合、Stream 和 Optional 更新方法

在集合上，Java9 增加了 `List.of()`、`Set.of()`、`Map.of()`和`Map.ofEntries()`等工厂方法来创建不可变集合，如下所示

``` java
List.of();
List.of("hello", "world");
List.of(1, 2, 3);
Set.of();
Set.of("Hello", "world");
Set.of(1, 2, 3);
Map.of();
Map.of("hello", 1, "world", 2);
```

Stream 中增加了新的方法 ofNullable、dropWhile、takeWhile 和 iterate。在下面代码中，stream 中包含了从1到5的元素。断言检查元素是否为奇数。第一个元素1被删除，结果stream流中包含4个元素

``` java
@Test
public void testDropWhile() throws Exception {
    final long count = Stream.of(1, 2, 3, 4, 5)
        .dropWhile(i -> i % 2 != 0)
        .count();
    assertEquals(4, count);
}
```

Collectors 中增加了新的方法 filtering 和 flatMapping。在 如下代码 中，对于输入的 String 流 ，先通过 flatMapping 把 String 映射成 Integer 流 ，再把所有的 Integer 收集到一个集合中。

