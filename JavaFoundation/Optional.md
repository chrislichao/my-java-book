# Optional

##### 1，Optional的产生
- Java8新特性
- 用来简化代码并高效处理空指针异常
##### 2，Optional对象创建
```java
// 创建包装对象值为null的Optional对象
Optional<String> optEmp = Optional.empty();
// 创建包装对象值非空的Optional对象
Optional<String> optOf = Optional.of("optional");
// 创建包装对象值可空可不空的Optional对象
Optional<String> optNull1 = Optional.ofNullable(null);
Optional<String> optNull2 = Optional.ofNullable("optional");
```
##### 3，Optional常用方法
- Optional.get()方法
  - 如果实际对象不为空，则返回对象的值
  - 如果实际对象为空，则抛出异常"No value present"
- Optional.isPresent()方法
  - 判读是否为空
  - 如果实际对象不为空，返回true
  - 如果实际对象为空，返回false
- Optional.ifPresent()方法
  - 判读是否为空并返回函数
  - 如果对象非空，则运行函数体
```java
Person person = getFromDB();
Optional.ofNullable(person).ifPresent(p -> System.out.println("年龄：" + p.getAge()));
```

- Optional.filter()方法
  - 过滤对象
  - 接收一个对象，然后对他将进行过滤，如果条件符合则返回Optional对象本身，如果不符合则返回空Optional
```java
Person person = getFromDB();
Optional.ofNullable(person).filter(p -> p.getAge() > 50);
```

- Optional.map()方法
  - 对象进行二次包装
```java
Person person = getFromDB();
Optional.ofNullable(person).map(p -> p.getName());
```

- Optional.orElse()方法
  - 为空返回对象
```java
Person person = getFromDB();
Optional.ofNullable(person).orElse(new Person());
```

- Optional.orElseGet()方法
  - 为空返回Supplier对象
```java
Optional<Supplier<Person>> sup = Optional.ofNullable(Person::new);
// 调用get()方法，此时才会调用对象的构造方法，即获得到真正对象
Optional.ofNullable(person).orElseGet(sup.get());
```

- Optional.orElseThrow()方法
  - 为空返回异常
``` java
//简单的一个查询
Person person = getFromDB();
Optional.ofNullable(person).orElseThrow(() -> new ServiceException("没有查询的相关数据"));
```

##### 4，总结
- 虽然好用，但不是可以完全替换if判断
- 看业务场景，可读性不一定比if强