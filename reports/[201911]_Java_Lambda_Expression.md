# Lambda Expression

<p align="justify">

Java là một ngôn ngữ lập trình hướng đối tượng. Điều này có nghĩa rằng tất cả mọi thứ trong lập trình Java đều xoanh quanh các Object. Các function trong Java đều là một phần của Class và chúng ta cần sử dụng class/object để có thể gọi chúng.

Nếu chúng ta xem xét những language như C++, Javascript thì chúng được gọi là functional programinh language bởi vì chúng ta có thể viết các function và sử dụng chúng khi cần thiết. Những ngôn ngữ này hỗ trọ cả Object Oriented Programming và Functional Programming.

Định hướng theo đối tượng không phải là xấu, nhưng nó mạng lại tính dài dòng cho chương trình. Ví dụ: giả sử chúng ta phải tạo một instance của Runnable, chúng ta có thể sử dụng anonymous class như sau
</p>

```java
Runnable r = new Runnable(){
			@Override
			public void run() {
				System.out.println("My Runnable");
			}};
```

<p align="justify">

Nếu như nhìn vào đoạn code trên, phần thực tế được sử dụng là đoạn code trong method `run()`. 

Java 8 Functional Interfaces và Lambda Expression sẽ giúp chúng ta viết code ít hơn và clear hơn.
</p>

## Functional Interface
<p align="justify">

Một interface chỉ có duy nhất một abstract method được gọi là Functional Interface. `@FunctionalInterface` annotation được thêm để đánh dấu một interface là functional interface.

Chúng ta không bắt buộc phải sử dụng annotation này nhưng chúng ta nên sử dụng với functional interface để tránh việc vô tình thêm những method bổ sung vào interface. Nếu interface sử dụng `@FunctionalInterface` annotation và interface đó có nhiều hơn một abstract method, nó sẽ ném một compiler error.

Lợi ích của functional interface là chúng ta có thể sử dụng lambda expression để khởi tạo chúng, tránh việc phải sử dụng anonymous class cồng kềnh.

Java 8 Collection API được viết lại và Stream API mới được giới thiệu có sử dụng rất nhiều functional interface. Java 8 đã định nghĩa rất nhiều functional interface trong `java.util.function` package. 
</p>

```java
interface Foo { boolean equals(Object obj); }
// Not functional because equals is already an implicit member (Object class)

interface Comparator<T> {
 boolean equals(Object obj);
 int compare(T o1, T o2);
}
// Functional because Comparator has only one abstract non-Object method

interface Foo {
  int m();
  Object clone();
}
// Not functional because method Object.clone is not public

interface X { int m(Iterable<String> arg); }
interface Y { int m(Iterable<String> arg); }
interface Z extends X, Y {}
// Functional: two methods, but they have the same signature

interface X { Iterable m(Iterable<String> arg); }
interface Y { Iterable<String> m(Iterable arg); }
interface Z extends X, Y {}
// Functional: Y.m is a subsignature & return-type-substitutable

interface X { int m(Iterable<String> arg); }
interface Y { int m(Iterable<Integer> arg); }
interface Z extends X, Y {}
// Not functional: No method has a subsignature of all abstract methods

interface X { int m(Iterable<String> arg, Class c); }
interface Y { int m(Iterable arg, Class<?> c); }
interface Z extends X, Y {}
// Not functional: No method has a subsignature of all abstract methods

interface X { long m(); }
interface Y { int m(); }
interface Z extends X, Y {}
// Compiler error: no method is return type substitutable

interface Foo<T> { void m(T arg); }
interface Bar<T> { void m(T arg); }
interface FooBar<X, Y> extends Foo<X>, Bar<Y> {}
// Compiler error: different signatures, same erasure
```

## Lambda Expression
<p align="justify">

Lambda Expression là cách mà chúng ta có thể hình dung functional programming trong thế giới hướng đối tượng java. Các object là core của ngôn ngữ lập trình java và chúng ta có thể không bao giờ có function mà nó không phải thuộc về một object, đó là lý do tại sao mà Java cung cấp hỗ trợ sử dụng lambda expression chỉ với functional interface.

Vì chỉ có duy nhất một abstract function trong các functional interface, nên sẽ không có sự nhầm lẫn khi sử dụng lambda expression. Cú pháp của lambda expression là `(argument) -> (body)`. 
</p>

```java
Runnable r1 = () -> System.out.println("My Runnable");
```

<p align="justify">

- Runnable là một functional interface, đó là lý do tại sao chúng ta có thể sử dụng lambda expression để create một instance của nó.
- Vì `run()` method không có bất kỳ argument nào nên lambda expression ở trên cũng không có argument.
</p>

### Why do we need lambda expression
<p align="justify">

1. Giảm số lượng code.
Một lơi ích của lambda expression là giảm số lượng code, chúng ta thấy rằng có thể dễ dàng create instance của functional interface bằng cách sử dụng lambda expression hơn là sử dụng annonymous class.

2. Hỗ trợ Sequential và Parallel Execution.
Một lợi ích khác khi sử dụng lambda expression là chúng ta có thể  hưởng lợi từ việc Stream API hỗ trợ sequential và parallel operation. Để giải thích điều này chúng ta hãy xem xét một ví dụ đơn giản sau
</p>

```java
//Traditional approach
private static boolean isPrime(int number) {		
	if(number < 2) return false;
	for(int i=2; i<number; i++){
		if(number % i == 0) return false;
	}
	return true;
}
```

<p align="justify">

Vấn đề với đoạn code trên là việc thực hiện tuần tự theo tự nhiên, nếu number quá lớn thì nó sẽ tốn rất nhiều time. Một vấn đề nữa với đoạn code này là nó có quá nhiều exit point và khó đọc. Hãy xem cách chúng ta sử dụng với lambda expression và stream API
</p>

```java
//Declarative approach
private static boolean isPrime(int number) {		
	return number > 1
			&& IntStream.range(2, number).noneMatch(
					index -> number % index == 0);
}
```

<p align="justify">

`IntStrean` là một chuỗi của các phần tử int value hỗ trợ sequential và parallel opwration. Đây là một int primitive đặc biệt của `Stream`

Để dễ đọc hơn, chúng ta có thể viết lại chương trình trên như sau
</p>

```java
private static boolean isPrime(int number) {
	IntPredicate isDivisible = index -> number % index == 0;
	
	return number > 1
			&& IntStream.range(2, number).noneMatch(
					isDivisible);
}
```

3. Truyền các behavior vào method
<p align="justify">

Hãy xem cách sử dụng lambda expression để truyền behavior tới method với một ví dụ đơn giản. 
</p>

```java
public static int sumWithCondition(List<Integer> numbers, Predicate<Integer> predicate) {
	    return numbers.parallelStream()
	    		.filter(predicate)
	    		.mapToInt(i -> i)
	    		.sum();
	}
```

```java
//sum of all numbers
sumWithCondition(numbers, n -> true)
//sum of all even numbers
sumWithCondition(numbers, i -> i%2==0)
//sum of all numbers greater than 5
sumWithCondition(numbers, i -> i>5)
```

4. Tăng hiệu suất với lazy
<p align="justify">

Một lợi ích nữa khi sử dụng lambda expression là lazy, ví dụ chúng ta cần viết một method tìm ra số  lẻ lớn nhất trong khoảng 3 tới 11 và trả về bình phương của nó.
</p>

```java
private static int findSquareOfMaxOdd(List<Integer> numbers) {
		int max = 0;
		for (int i : numbers) {
			if (i % 2 != 0 && i > 3 && i < 11 && i > max) {
				max = i;
			}
		}
		return max * max;
	}
```

<p align="justify">

Chương trình trên sẽ luôn chạy một cách tuần tự nhưng chúng ta có thể sử dụng lambada để thực hiện tương tự và lợi ích của Lazy. Chúng ta sẽ viết lại chương trình trên bằng cách sử dụng Stream API và lambda expression.
</p>

```java
public static int findSquareOfMaxOdd(List<Integer> numbers) {
		return numbers.stream()
				.filter(NumberTest::isOdd) 		//Predicate is functional interface and
				.filter(NumberTest::isGreaterThan3)	// we are using lambdas to initialize it
				.filter(NumberTest::isLessThan11)	// rather than anonymous inner classes
				.max(Comparator.naturalOrder())
				.map(i -> i * i)
				.get();
	}

	public static boolean isOdd(int i) {
		return i % 2 != 0;
	}
	
	public static boolean isGreaterThan3(int i){
		return i > 3;
	}
	
	public static boolean isLessThan11(int i){
		return i < 11;
	}
```

<p align="justify">

Nếu bạn ngạc nhiên với double colon `::` operator, nó được giới thiệu trong java8 và được sử dụng cho method reference. Java Compiler sẽ mapping các argument tới method được gọi. Nó là một cách viết tắt của lambda expression `i -> isGreaterThan3(i)` hay `i -> NumberTest.isGreaterThan3(i)`
</p>

## Lambda Expression Example
```java

() -> {}                     // No parameters; void result

() -> 42                     // No parameters, expression body
() -> null                   // No parameters, expression body
() -> { return 42; }         // No parameters, block body with return
() -> { System.gc(); }       // No parameters, void block body

// Complex block body with multiple returns
() -> {
  if (true) return 10;
  else {
    int result = 15;
    for (int i = 1; i < 10; i++)
      result *= i;
    return result;
  }
}                          

(int x) -> x+1             // Single declared-type argument
(int x) -> { return x+1; } // same as above
(x) -> x+1                 // Single inferred-type argument, same as below
x -> x+1                   // Parenthesis optional for single inferred-type case

(String s) -> s.length()   // Single declared-type argument
(Thread t) -> { t.start(); } // Single declared-type argument
s -> s.length()              // Single inferred-type argument
t -> { t.start(); }          // Single inferred-type argument

(int x, int y) -> x+y      // Multiple declared-type parameters
(x,y) -> x+y               // Multiple inferred-type parameters
(x, final y) -> x+y        // Illegal: can't modify inferred-type parameters
(x, int y) -> x+y          // Illegal: can't mix inferred and declared types

```

## Method và Contructor Reference
<p align="justify">

Một method reference được sử dụng để tham chiếu tới một method, một contructor reference tương tự cũng được sử dụng để tham chiếu tới một constructor
</p>

```java
System::getProperty
System.out::println
"abc"::length
ArrayList::new
int[]::new
```

<p align="justify">

Mình đã chia sẻ tất cả nhưng thứ về Java 8 Functional Interface và Lambda Expression. 

Hẹn gặp lại các bạn ở những bài viết tiếp theo nhé.
</p>