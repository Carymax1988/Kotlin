# **Kotlin与Java应用的对比**
---
## **&emsp;1.Hello，world**
### *Java:*
```Java
  String start() {
    return "OK"
  }
```
### *Kotlin:*
```kotlin
  fun start(): String = "OK"
```

## **&emsp;2.数据类**
### *Java:*
```Java
  public class Person {
      private final String name;
      private final int age;

      public Person(String name, int age) {
          this.name = name;
          this.age = age;
      }

      public String getName() {
          return name;
      }

      public int getAge() {
          return age;
      }
  }
```
### *Kotlin:*
```kotlin
  data class Person(val name : String, val age : Int)
```

### *Kotlin:*
```kotlin
  class Person(name: String, age: Int?) {
        var name: String = ""
        var age: Int? = null
            set(a) {
                field = a
            }

        init {
            this.name = name
            this.age = age
        }
    }
```

## **&emsp;3.空安全**
### *Java:*
```Java
  public void sendMessageToClient(
      @Nullable Client client,
      @Nullable String message,
      @NotNull Mailer mailer
  ) {
      if (client == null || message == null) return;

      PersonalInfo personalInfo = client.getPersonalInfo();
      if (personalInfo == null) return;

      String email = personalInfo.getEmail();
      if (email == null) return;

      mailer.sendMessage(email, message);
  }
```
### *Kotlin:*
```kotlin
  fun sendMessageToClient(
          client: Client?, message: String?, mailer: Mailer
  ){
      val email = client?.personalInfo?.email
      if(message!= null &&  email != null){
          mailer.sendMessage(email, message)
      }
  }
```
### &emsp;&emsp;Elvis 操作符
### *Kotlin:*
```kotlin
  val l: Int = if (b != null) b.length else -1

  val l = b?.length ?: -1
```
### &emsp;&emsp;!! 操作符
```kotlin
  val l = b!!.length
```

## **&emsp;4.扩展**
### *Kotlin:*
```kotlin
  fun Int.r(): RationalNumber = RationalNumber(this, 1)
  fun Pair<Int, Int>.r(): RationalNumber = RationalNumber(first, second)

  data class RationalNumber(val numerator: Int, val denominator: Int)
```
### &emsp;&emsp;（1）可空接收者
### *Kotlin:*
```kotlin
    fun Any?.toString(): String {
        if (this == null) return "null"
        // 空检测之后，“this”会自动转换为非空类型，所以下面的 toString()
        // 解析为 Any 类的成员函数
        return toString()
    }
```
### &emsp;&emsp;（2）扩展属性
### *Kotlin:*
```kotlin
    val <T> List<T>.lastIndex: Int
        get() = size - 1
```
### &emsp;&emsp;（3）扩展的作用域
<p>&emsp;&emsp;&emsp;&emsp;大多数时候我们在顶层定义扩展，即直接在包里</p>

### *Kotlin:*
```kotlin
      package foo.bar

      fun Baz.goo() { …… }
```

<p>&emsp;&emsp;&emsp;&emsp;要使用所定义包之外的一个扩展，我们需要在调用方导入它</p>

### *Kotlin:*
```kotlin
      package com.example.usage

      import foo.bar.goo // 导入所有名为“goo”的扩展
                   // 或者
      import foo.bar.*   // 从“foo.bar”导入一切

      fun usage(baz: Baz) {
      baz.goo()
      }
```
### &emsp;&emsp;（4）动机
<p>&emsp;&emsp;&emsp;&emsp;在Java中，我们将类命名为“*Utils”：FileUtils、StringUtils 等，著名的 java.util.Collections 也属于同一种命名方式。关于这些 Utils-类的不愉快的部分是代码写成这样：</p>

### *Java:*
```Java
  Collections.swap(list, Collections.binarySearch(list,
  Collections.max(otherList)),
  Collections.max(list));
```

<p>&emsp;&emsp;&emsp;&emsp;这些类名总是碍手碍脚的，我们可以通过静态导入达到这样效果：</p>

### *Java:*
```Java
  swap(list, binarySearch(list, max(otherList)), max(list));
```

<p>&emsp;&emsp;&emsp;&emsp;这会变得好一点，但是我们并没有从 IDE 强大的自动补全功能中得到帮助。如果能这样就更好了：</p>

### *扩展写法:*
```kotlin
  list.swap(list.binarySearch(otherList.max()), list.max())
```

## **&emsp;5.对象表达式及Lambda**
### &emsp;&emsp;（1）对象表达式对应java的匿名内部类
### *Java:*
```Java
  public List<Integer> getList() {
        List<Integer> arrayList = new ArrayList<>(Arrays.asList(1, 5, 2));
        Collections.sort(arrayList, new Comparator<Integer>() {
            @Override
            public int compare(Integer x, Integer y) {
                return y - x;
            }
        });
        return arrayList;
  }
```
### *Kotlin:*
```kotlin
  fun getList(): List<Int> {
      val arrayList = arrayListOf(1, 5, 2)
      Collections.sort(arrayList, object : Comparator<Int>{
          override fun compare(x:Int, y:Int) = y - x
      })
      return arrayList
  }
```

### &emsp;&emsp;（2）Lambda

### *Java:*
```Java
  public List<Integer> getList() {
        List<Integer> arrayList = new ArrayList<>(Arrays.asList(1, 5, 2));
        Collections.sort(arrayList, (x, y) -> y - x);
        return arrayList;
  }
```
### *Kotlin:*
```kotlin
  fun getList(): List<Int> {
    val arrayList = arrayListOf(1, 5, 2)
    Collections.sort(arrayList, { x, y -> y - x })
    return arrayList
  }
```

## **&emsp;6.集合操作**
### *Java:*
```Java
  public Collection<String> doSomethingStrangeWithCollection(
        Collection<String> collection
        ) {
      Map<Integer, List<String>> groupsByLength = new HashMap();
      for (String s : collection) {
          List<String> strings = groupsByLength.get(s.length());
          if (strings == null) {
              strings = new ArrayList();
              groupsByLength.put(s.length(), strings);
          }
          strings.add(s);
      }

      int maximumSizeOfGroup = 0;
      for (List<String> group : groupsByLength.values()) {
          if (group.size() > maximumSizeOfGroup) {
              maximumSizeOfGroup = group.size();
          }
      }

      for (List<String> group : groupsByLength.values()) {
          if (group.size() == maximumSizeOfGroup) {
              return group;
          }
      }
      return null;
  }
```
### *Kotlin:*
```kotlin
  fun doSomethingStrangeWithCollection(collection: Collection<String>): Collection<String>? {

    val groupsByLength = collection.groupBy { s -> s.length }

    val maximumSizeOfGroup = groupsByLength.values.map { group -> group.size }.max()

    return groupsByLength.values.firstOrNull { group -> group.size == maximumSizeOfGroup }
  }
```

### *Java8:*
```java
  public Collection<String> doSomethingStrangeWithCollection(
        Collection<String> collection
        ) {
          Map<Integer, List<String>> groupsByLength = collection.stream()
                .collect(groupingBy(s -> s.length()));

          final int maximumSizeOfGroup = groupsByLength.values().stream().map(group -> group.size()).max((o1, o2) -> o1.compareTo(o2)).get();

          return (groupsByLength.values().stream().filter(group -> group.size() == maximumSizeOfGroup)).findFirst().get();
  }
```

---
#### 抛砖引玉，详细内容请查看参考资料
#### 参考资料：
<p>Kotlin语言官网：https://kotlinlang.org/</p>
<p>Kotlin语言中文站：https://www.kotlincn.net/</p>
<p>Kotlin心印学习：https://play.kotlinlang.org/koans/Introduction/</p>
