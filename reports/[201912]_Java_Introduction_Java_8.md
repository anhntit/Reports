# Introduction Java 8
<p align="justify">
Chào mừng các bạn tới bài viết về Java 8. Bài viết này sẽ giới thiệu những tính năng mới có trong Java 8. Chúng ta sẽ cùng xem cách sử dụng deafault interface method, lambda expression, method reference và repeatable annotation. Ở cuối bài viết là những thay đổi về API như stream, functional interface, map và Date.

Chúng ta cùng bắt đầu nhé.    
</p>

## Default Methods for Interface
<p align="justify">

Java 8 cho phép chúng ta thêm một phương thức không trừu tượng bằng cách sử dụng `default` keyword. Tính năng này được biết tới tên gọi `Extension Methods`. 
</p>

```java
interface Formula {
    double calculate(int a);

    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}
```

<p align="justify">

Bên dưới abstract method `calculate` của interface `Formula` là default method `sqrt`. Tất cả các class implement interface này chỉ phải implement method `calculate`.
</p>

```java
Formula formula = new Formula() {
    @Override
    public double calculate(int a) {
        return sqrt(a * 100);
    }
};

formula.calculate(100);     // 100.0
formula.sqrt(16);           // 4.0
```

## Lambda expressions
<p align="justify">
Để bắt đầu chúng ta cùng xem xét một ví dụ về việc sắp xếp một list các phần tử có kiểu string trong Java như sau:
</p>

```java
List<String> names = Arrays.asList("peter", "anna", "mike", "xenia");

Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return b.compareTo(a);
    }
});
```

<p align="justify">

Method `Collections.sort` cho phép một list và một comparator để sắp xếp các element của nó. Thông thường chúng ta hay tạo một annonymous comparator và truyền chúng tới sort method.

Thay vì tạo một annonymous object. Java 8 có một cú pháp ngắn gọn hơn. `lambda expression`
</p>

```java
Collections.sort(names, (String a, String b) -> {
    return b.compareTo(a);
});
```

<p align="justify">
Như bạn có thể thấy đoạn code trên rất ngắn và cũng dễ hiểu. Nhưng nó có thể ngắn hơn nữa.
</p>

```java
Collections.sort(names, (String a, String b) -> b.compareTo(a));
```

<p align="justify">

Nếu body của method chỉ trên một dòng thì chúng ta có thể bỏ đi cặp ngoặc `{}` và `return` keyword.
</p>

```java
Collections.sort(names, (a, b) -> b.compareTo(a));
```

<p align="justify">
Java compiler tự nhận biết được kiểu parameter vì vậy bạn có thể bỏ qua chúng.
</p>

## Functional Interfaces
<p align="justify">

Mỗi lambda tương ứng với một kiểu và nó được xác định bởi một interface. `Functional interface` phải khai báo chính xác chỉ một abstract method. Mỗi lambda expression của kiểu đó sẽ được match với những abstract method. Vì default method không phải là abstract nên bạn có thể thêm các default method vào functional interface một cách thoải mái.
</p>

```java
@FunctionalInterface
interface Converter<F, T> {
    T convert(F from);
}
```

```java
Converter<String, Integer> converter = (from) -> Integer.valueOf(from);
Integer converted = converter.convert("123");
System.out.println(converted);    // 123
```

## Method and Constructor References
<p align="justify">
Ví dụ trên có thể viết lại một cách đơn giản hơn, sử dụng method reference như sau
</p>

```java
Converter<String, Integer> converter = Integer::valueOf;
Integer converted = converter.convert("123");
System.out.println(converted);   // 123
```

<p align="justify">

Java 8 cho phép bạn truyền một tham chiếu của method hoặc constructor thông qua `::` keyword. Ví dụ trên cho thấy cách làm thế nào để tham chiếu một methd. Nhưng chúng ta cũng có thể tham chiếu method của object.
</p>

```java
class Something {
    String startsWith(String s) {
        return String.valueOf(s.charAt(0));
    }
}
```

```java
Something something = new Something();
Converter<String, String> converter = something::startsWith;
String converted = converter.convert("Java");
System.out.println(converted);    // "J"
```

<p align="justify">

Hãy xem `::` keywork làm việc với constructor như thế nào.
</p>

```java
class Person {
    String firstName;
    String lastName;

    Person() {}

    Person(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
}
```

<p align="justify">
Chúng ta sẽ tạo ra một person factory insterface được sử dụng để tạo một new person
</p>

```java
interface PersonFactory<P extends Person> {
    P create(String firstName, String lastName);
}
```

<p align="justify">
Thay vì implement factory một cách thông thường, chúng ta sẽ sử dụng constructor reference
</p>

```java
PersonFactory<Person> personFactory = Person::new;
Person person = personFactory.create("Peter", "Parker");
```

<p align="justify">

Chúng ta tạo một tham chiếu tới Person constructor thông qua `Person::new`. Java compiler sẽ tự động chọn đúng constructor bằng việc match với signature của `PersonFactory.create`
</p>

## Lambda Scopes
<p align="justify">
Việc truy cập các biến bên ngoài từ lambda expression tương tự như anonymous object. Chúng ta có thể truy cập tới các final variable từ phạm vi bên ngoài cục bộ cũng như các instance field và static variable.
</p>

### Accessing local variable
<p align="justify">
Chúng ta có thể đọc final local variable từ phạm vi cục bộ bên ngoài của lambda expression
</p>

```java
final int num = 1;
Converter<Integer, String> stringConverter =
        (from) -> String.valueOf(from + num);

stringConverter.convert(2);     // 3
```

<p align="justify">

Nhưng điểm khác với anonymous object là `num` không cần phải được khai báo final. 
</p>

```java
int num = 1;
Converter<Integer, String> stringConverter =
        (from) -> String.valueOf(from + num);

stringConverter.convert(2);     // 3
```

<p align="justify">

Tuy nhiên, một điều chú ý là `num` phải được ngầm định là final để code có thể được compile. Ví dụ đoạn code sau sẽ không được compile
</p>

```java
int num = 1;
Converter<Integer, String> stringConverter =
        (from) -> String.valueOf(from + num);
num = 3;
```

### Accessing fields and static variables
<p align="justify">

Các bạn có nhớ ví dụ `Formula` ở phần đầu tiên? Interface `Formula` định nghĩa một default method `sqrt` mà có thể được truy cập từ formula instance bao gồm các anonymous object. Điều này sẽ không làm việc trong lambda expression

Default method không thể được truy cập bên trong lambda expression. Code sau sẽ không được compile
</p>

```java
Formula formula = (a) -> sqrt( a * 100);
```

## Build-in Functional Interfaces
<p align="justify">

JDK 1.8 API có chứa rất nhiều build-in functional interface. Một số trong đó chúng ta biết tới từ những phiên bản trước là `Comparator` và `Runable`. Nhưng interface được mở rộng để mở Lambda expression thông qua `@FunctionalInterface` annotation.

Nhưng Java 8 API cũng có rất nhiều functional interface mới. 
</p>

### Predicates
<p align="justify">

Predicate là boolean-value function của một argument. Interface có chứa rất nhiều default method phong phú cho việc kết hợp các predicate để thực hiện những logic phức tạp (and, or, negate))
</p>

```java
Predicate<String> predicate = (s) -> s.length() > 0;

predicate.test("foo");              // true
predicate.negate().test("foo");     // false

Predicate<Boolean> nonNull = Objects::nonNull;
Predicate<Boolean> isNull = Objects::isNull;

Predicate<String> isEmpty = String::isEmpty;
Predicate<String> isNotEmpty = isEmpty.negate();
```

### Functions
<p align="justify">

Function chấp nhận một argument và đưa ra kết quả. Default method có thể được sử dụng để chain multiple các function cùng nhau (compose, andThen)
</p>

```java
Function<String, Integer> toInteger = Integer::valueOf;
Function<String, String> backToString = toInteger.andThen(String::valueOf);

backToString.apply("123");     // "123"
```

### Suppliers
<p align="justify">

Supplier cung cấp một kết quả của một kiểu chung chung. Không giống như Functions, Suppliers không chấp nhận đối số
</p>

```java
Supplier<Person> personSupplier = Person::new;
personSupplier.get();   // new Person
```

### Consumers
<p align="justify">

Consumers đại diện cho các hành động để thực hiện trên một đối số đầu vào
</p>

```java
Consumer<Person> greeter = (p) -> System.out.println("Hello, " + p.firstName);
greeter.accept(new Person("Luke", "Skywalker"));
```

### Comparator
<p align="justify">
Comparators được biết tới từ những phiên bản trước của Java. Java 8 thêm một số default method vào interface này.
</p>

```java
Comparator<Person> comparator = (p1, p2) -> p1.firstName.compareTo(p2.firstName);

Person p1 = new Person("John", "Doe");
Person p2 = new Person("Alice", "Wonderland");

comparator.compare(p1, p2);             // > 0
comparator.reversed().compare(p1, p2);  // < 0
```

### Optionals
<p align="justify">

Optionals không phải là functional interface, thay vào đó nó là một tiện ích để ngăn chặn `NullPointerException`. Optional đơn giản là một container của những giá trị có thể là null or non-null. Với method thì nó có thể trả về một kết quả non-null nhưng thỉnh thoảng không trả về gì cả. Thay vì việc return `null` bạn sẽ return một `Optional` trong java 8
</p>

```java
Optional<String> optional = Optional.of("bam");

optional.isPresent();           // true
optional.get();                 // "bam"
optional.orElse("fallback");    // "bam"

optional.ifPresent((s) -> System.out.println(s.charAt(0)));     // "b"
```

## Streams
<p align="justify">

Một `java.util.Stream` đại diện cho một chuỗi các element mà trên đó một hoặc nhiều hoạt động có thể được thực hiện. Stream operator có thể là `intermediate` hoặc `terminal`. Trong khi terminal operation trả về một kết quả của một loại nhất định thì intermediate operation trả về chính stream đó sao cho bạn có thể gọi liên tiếp các method trên một dòng. Stream được tạo ra trên một nguồn cụ thể, VD `java.util.Collection` như list hoặc set (map không được hỗ trợ). Strean operation có thể được executed một cách tuần tự hoặc đồng thời.

Hãy nhìn vào ví dụ đầu tiên để xem cách stream hoạt động tuần tự. Đầu tiên chúng ta sẽ tạo ra một list các phần tử có kiểu String
</p>

```java
List<String> stringCollection = new ArrayList<>();
stringCollection.add("ddd2");
stringCollection.add("aaa2");
stringCollection.add("bbb1");
stringCollection.add("aaa1");
stringCollection.add("bbb3");
stringCollection.add("ccc");
stringCollection.add("bbb2");
stringCollection.add("ddd1");
```

<p align="justify">

Collections trong Java 8 được mở rộng vì vậy bạn có thể tạo stream thông qua việc gọi `Collection.stream()` hoặc `Collection.parallelStream()`. Những phần tiếp theo sẽ giải thích hầu hết các stream operation thông thường.
</p>

### Filter
<p align="justify">

Filter chấp nhận một predicate to filter tất cả các element của stream. Những operation này là `intermediate` cái mà cho phép chúng ta gọi một stream operation khác (VD: `foreach`). ForEach chấp nhận một consumer để thực hiện trên tất cả các element trong stream được filter. ForEach là một `terminal` operation. Nó là `void`, vì thế chúng ta không thể gọi một stream operator khác.
</p>

```java
stringCollection
    .stream()
    .filter((s) -> s.startsWith("a"))
    .forEach(System.out::println);

// "aaa2", "aaa1"
```

### Sorted
<p align="justify">

Sorted là một `intermediate` operation trả về một sorted view của stream đó. Các element được sắp xếp theo tự nhiên trừ khi bạn có một custom `Comparator`
</p>

```java
stringCollection
    .stream()
    .sorted()
    .filter((s) -> s.startsWith("a"))
    .forEach(System.out::println);

// "aaa1", "aaa2"
```

<p align="justify">

Hãy nhớ rằng, `sorted` chỉ tạo một sorted view của stream mà không thực hiện sắp xếp thứ tự của collection. Thứ tự của `stringCollection` không bị ảnh hưởng
</p>

```java
System.out.println(stringCollection);
// ddd2, aaa2, bbb1, aaa1, bbb3, ccc, bbb2, ddd1
```

### Map
<p align="justify">

Intermediate operation `map` sẽ convert mỗi element thành một object khác thông qua function được truyền vào. Ví dụ sau chúng ta sẽ convert mỗi string thành một upper-case string. Nhưn gbanj cũng có thể sử dụng `map` để có thể chuyển đổi object thành một kiểu khác. Generic type của stream kết quả sẽ phụ thuộc vào generic type của function truyền vào `map`.
</p>

```java
stringCollection
    .stream()
    .map(String::toUpperCase)
    .sorted((a, b) -> b.compareTo(a))
    .forEach(System.out::println);

// "DDD2", "DDD1", "CCC", "BBB3", "BBB2", "AAA2", "AAA1"
```

### Match
<p align="justify">

Matching operator có thể được sử dụng để kiểm tra xem predicate có match với stream hay không. Tất cả những operator này là `terminal` và return một boolean
</p>

```java
boolean anyStartsWithA =
    stringCollection
        .stream()
        .anyMatch((s) -> s.startsWith("a"));

System.out.println(anyStartsWithA);      // true

boolean allStartsWithA =
    stringCollection
        .stream()
        .allMatch((s) -> s.startsWith("a"));

System.out.println(allStartsWithA);      // false

boolean noneStartsWithZ =
    stringCollection
        .stream()
        .noneMatch((s) -> s.startsWith("z"));

System.out.println(noneStartsWithZ);      // true
```

### Count
<p align="justify">

Count là một `terminal` operation trả về số lượng element trong một stream
</p>

```java
long startsWithB =
    stringCollection
        .stream()
        .filter((s) -> s.startsWith("b"))
        .count();

System.out.println(startsWithB);    // 3
```

### Reduce
<p align="justify">

Đây là `terminal` operator thực hiện việc giảm trên các element của stream với một function được truyền vào. Kết quả là một `Optional`
</p>

```java
Optional<String> reduced =
    stringCollection
        .stream()
        .sorted()
        .reduce((s1, s2) -> s1 + "#" + s2);

reduced.ifPresent(System.out::println);
// "aaa1#aaa2#bbb1#bbb2#bbb3#ccc#ddd1#ddd2"
```

## Parallel Streams
<p align="justify">

Như đã đề cập ở trên, streams có thể là sequential hoặc parallel. Các operator trên sequential stream được thực trên một single thread trong khi các operator trên parallel stream được thực hiện một cách đồng thời trên multiple thread.

Ví dụ sau sẽ cho thấy việc tăng hiệu suất khi sử dụng parallel stream.

Đầu tiên chúng ta sẽ tạo một list có kích thước lớn chứa các phần tử riêng biệt
</p>

```java
int max = 1000000;
List<String> values = new ArrayList<>(max);
for (int i = 0; i < max; i++) {
    UUID uuid = UUID.randomUUID();
    values.add(uuid.toString());
}
```

### Sequential Sort

```java
long t0 = System.nanoTime();

long count = values.stream().sorted().count();
System.out.println(count);

long t1 = System.nanoTime();

long millis = TimeUnit.NANOSECONDS.toMillis(t1 - t0);
System.out.println(String.format("sequential sort took: %d ms", millis));

// sequential sort took: 899 ms
```

### Parallel Sort

```java

long t0 = System.nanoTime();

long count = values.parallelStream().sorted().count();
System.out.println(count);

long t1 = System.nanoTime();

long millis = TimeUnit.NANOSECONDS.toMillis(t1 - t0);
System.out.println(String.format("parallel sort took: %d ms", millis));

// parallel sort took: 472 ms
```

<p align="justify">

Như bạn có thể thấy hai đoạn code gần như giống hệt nhau nhưng parallel sort nhanh hơn 50%. Đó chính là sự thay đổi khi chúng ta sử dụng `parallelStream()` thay vì `stream()`
</p>

## Map
<p align="justify">

Map không hỗ trợ stream. Thay vào đó, map hỗ trợ rất nhiều method hữu ích
</p>

```java
Map<Integer, String> map = new HashMap<>();

for (int i = 0; i < 10; i++) {
    map.putIfAbsent(i, "val" + i);
}

map.forEach((id, val) -> System.out.println(val));
```

<p align="justify">

`putIfAbsent` nếu một key đã tồn tại và chúng ta cố gắng put một giá trị gía trị khác cho key đó thì giá trị cũ của key đó vẫn được giữ nguyên. `forEach` chấp nhận một consumer để thực hiện duyệt qua các element của map.

Ví dụ sau sẽ show những function hữu ích khác
</p>

```java

map.computeIfPresent(3, (num, val) -> val + num);
map.get(3);             // val33

map.computeIfPresent(9, (num, val) -> null);
map.containsKey(9);     // false

map.computeIfAbsent(23, num -> "val" + num);
map.containsKey(23);    // true

map.computeIfAbsent(3, num -> "bam");
map.get(3);             // val33
```

<p align="justify">

Tiếp theo chúng ta sẽ xem làm thế nào để có thể remove giá trị cho một key xác định
</p>

```java

map.remove(3, "val3");
map.get(3);             // val33

map.remove(3, "val33");
map.get(3);             // null
```

<p align="justify">

Một method hữu ích khác
</p>

```java
map.getOrDefault(42, "not found");  // not found
```

<p align="justify">

Việc merge các entry của map cũng khá dễ dàng
</p>

```java
map.merge(9, "val9", (value, newValue) -> value.concat(newValue));
map.get(9);             // val9

map.merge(9, "concat", (value, newValue) -> value.concat(newValue));
map.get(9);             // val9concat
```

<p align="justify">

Merge sẽ put key/value vào map nếu không có entry tương ứng tốn tại trong map và nó sẽ thay đổi value đã tồn tại
</p>

## Date API
<p align="justify">


</p>
