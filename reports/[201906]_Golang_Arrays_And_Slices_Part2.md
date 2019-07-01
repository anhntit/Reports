# [Golang] Arrays And Slices (Part 2)

<p align="justify">
Trong <i><a href="">phần trước</a></i> mình đã chia sẻ với các bạn về Arrays. Phần này mình tiếp tuck chia sẻ về Slices nhé.
</p>

## What is the slices?
<p align="justify">
Một slice thực tế bao quanh một array, nó rất thuận tiện, linh hoạt và mạnh mẽ. Slices không sở hữu bất kỳ dữ liệu nào của riêng nó. Nó chỉ là một tham chiếu tới một array đã tồn tại.
</p>

## Creating a slice
<p align="justify">Một slice với các phần tử mang kiểu T được biểu thị dạng `[]T`</p>

```golang
1   package main
2   
3   import (  
4       "fmt"
5   )
6
7   func main() {  
8       a := [5]int{76, 77, 78, 79, 80}
9       var b []int = a[1:4] //creates a slice from a[1] to a[3]
10      fmt.Println(b)
11  }
```
<sub>*[Run program in playground](https://play.golang.org/p/Za6w5eubBB)*</sub>

<p align="justify">

Cú pháp `a[start:end]` sẽ tạo một slice từ array `a` bắt đầu từ index `start` đến index `end - 1`. Vì vậy tại line 9 trong chương trình trên `a[1:4]` sẽ tạo một biểu diễn slice cho array `a` bắt đầu từ index 1 tới index 3. Do đó slice `b` sẽ có các value `[77 78 79]` 

Chúng ta sẽ cũng xem một cách khác để tạo slice.
</p>

```golang
1   package main
2   
3   import (  
4       "fmt"
5   )
6  
7   func main() {  
8       c := []int{6, 7, 8} //creates and array and returns a slice reference
9       fmt.Println(c)
10  }
```
<sub>*[Run program in playground](https://play.golang.org/p/_Z97MgXavA)*</sub>
<p align="justify">

Trong chương trình trên tại line 9, `c := []int{6, 7, 8}` tạo một array với 3 integer và return một slice reference được lưu vào biến `c`
</p>

## Modifying a slice
<p align="justify">
Một slice không sở hữu bất kỳ một data nào của riêng nó. Nó chỉ là một đại diện cho một mảng bên dưới. Bất kỳ một thay đổi nào được thực hiện trên slice sẽ được phản ánh tới mảng bên dưới. Điều này có nghĩa rằng mảng đó cũng sẽ thay đổi.
</p>

```golang
1   package main
2   
3   import (  
4       "fmt"
5   )
6  
7   func main() {  
8       darr := [...]int{57, 89, 90, 82, 100, 78, 67, 69, 59}
9       dslice := darr[2:5]
10      fmt.Println("array before",darr)
11      for i := range dslice {
12          dslice[i]++
13      }
14      fmt.Println("array after",darr) 
15  }
```
<sub>*[Run program in playground](https://play.golang.org/p/6FinudNf1k)*</sub>
<p align="justify">

Tại line 9 của chương trình trên, chúng ta tạo `dslice` từ index 2, 3, 4 của array. Vòng lặp for tăng giá trị của các index lên 1. Khi chúng ta in array sau phòng lặp for (line 14). Output của chương trình sẽ là
</p>

```
array before [57 89 90 82 100 78 67 69 59]  
array after [57 89 91 83 101 78 67 69 59]  
```
<p align="justify">
Khi có các slice khác nhau đại diện cho cùng một mảng bên dưới, mỗi thay đổi trên slice đó sẽ đều được phản ảnh lại trên mảng.
</p>

```golang
1   package main
2  
3   import (  
4       "fmt"
5   )
6 
7   func main() {  
8       numa := [3]int{78, 79 ,80}
9       nums1 := numa[:] //creates a slice which contains all elements of the array
10      nums2 := numa[:]
11      fmt.Println("array before change 1",numa)
12      nums1[0] = 100
13      fmt.Println("array after modification to slice nums1", numa)
14      nums2[1] = 101
15      fmt.Println("array after modification to slice nums2", numa)
16  }
```
<sub>*[Run program in playground](https://play.golang.org/p/mdNi4cs854)*</sub>
<p align="justify">

Tại line 9, trong `numa[:]` các giá trị start index và end index không có. Theo mặc định các giá trị cho start và end sẽ là `0` và `len(nume)`. Cả 2 slice `nums1` và `nums2` chia sẻ cùng một array. Output của chương trình là
</p>

```
array before change 1 [78 79 80]  
array after modification to slice nums1 [100 79 80]  
array after modification to slice nums2 [100 101 80]  
```

<p align="justify">
Ví dụ trên giúp chúng ta clear hơn khi thực hiện thay đổi trên các slice chia sẻ cùng một array.
</p>

## Length and capacity of a slice
<p align="justify">

Length của một slice là số lượng phần tử có trong slice đó. `Capacity của slice là số lượng phần tử trong array bên dưới bắt đầu từ index mà từ đó slice được tạo ra. (có nghĩa rằng tính từ start index)`

Hãy xem ví dụ dưới để hiểu hơn nhé 
</p>

```golang
1   package main
2 
3   import (  
4       "fmt"
5   )
6
7   func main() {  
8       fruitarray := [...]string{"apple", "orange", "grape", "mango", "water melon", "pine apple", "chikoo"}
9       fruitslice := fruitarray[1:3]
10      fmt.Printf("length of slice %d capacity %d", len(fruitslice), cap(fruitslice)) //length of is 2 and capacity is 6
11  }
```
<sub>*[Run program in playground](https://play.golang.org/p/a1WOcdv827)*</sub>

<p align="justify">

Trong chương trình trên, `fruitslice` được tạo từ index 1 và 2 của `fruitarray`. Do đó lenth của `fruitslice` là 2.

Length của `fruitarray` là 7. `fruitslice` được tạo từ index `1` của `fruitarray`. Do đó capacity của `fruitslice` là số lượng phần tử trong `fruitarray` bắt đầu từ index `1`.
tức là từ `orange` và giá trị đó là `6`. Do đó capacity của fruitslice là 6. Ouput của chương trình `length của slice là 2 và capacity là 6`.

Một slice có thể được re-sliced cho tới capacity của nó. Nếu vượt quá capacity của nó sẽ dẫn tới chương trình throw một run time error.
</p>

```golang
1   package main
2   
3   import (  
4       "fmt"
5   )
6
7   func main() {  
8       fruitarray := [...]string{"apple", "orange", "grape", "mango", "water melon", "pine apple", "chikoo"}
9       fruitslice := fruitarray[1:3]
10      fmt.Printf("length of slice %d capacity %d\n", len(fruitslice), cap(fruitslice)) //length of is 2 and capacity is 6
11      fruitslice = fruitslice[:cap(fruitslice)] //re-slicing furitslice till its capacity
12      fmt.Println("After re-slicing length is",len(fruitslice), "and capacity is",cap(fruitslice))
13  }
```
<sub>*[Run program in playground](https://play.golang.org/p/GcNzOOGicu)*</sub>

<p align="justify">

Trong line 11 của chương trình trên, `fruitslice` được re-sliced tới capacity của nó. Chương trình trên sẽ có output:
</p>

```
length of slice 2 capacity 6  
After re-slicing length is 6 and capacity is 6  
```

## Creating a slice using make
<p align="justify">

<i>`func make([]T, len, cap) []T`</i> có thể được sử dụng để tạo một slice bằng việc truyền các giá trị của type, length và capacity. Capacity parameter là optional và mặc định là length. make function sẽ tạo ra một array và một slice tham chiếu tới nó.
</p>

```golang
1   package main
2
3   import (  
4       "fmt"
5   )
6
7   func main() {  
8       i := make([]int, 5, 5)
9       fmt.Println(i)
10  }
```
<sub>*[Run program in playground](https://play.golang.org/p/M4OqxzerxN)*</sub>

<p align="justify">

Các giá trị là zero theo mặc định khi một slice được tạo ra bằng cách sử dụng make. Chương trình trên có output `[0 0 0 0 0]`</p>

## Appending to a slice
<p align="justify">

Như chúng ta đã biết, một array có length cố định là length của chúng không thể tăng. Slice là động và các element mới có thể được append vào slice bằng cách sử dụng `append` function. Khai báo của append function là `func append(s [T], x ...T) [T]`.

`x ...T` trong function definition có nghĩa rằng function cho phép một số lượng argument cho đối số x. Những loại function này được gọi là <u>`variadic functions`</u>.

Một câu hỏi đặt ra là nếu slice là một đại diện cho một array và bản thân array có độ dài cố định vậy làm thế nào là một slice có dynamic length. Điều thực sự xảy ra bên dưới, khi có một element được append tới slice, một new array được tạo ra. Những element của array đang tồn tại sẽ được copy tới new array và một new slice tham chiếu tới new array này sẽ được return. Capacity của new slice sẽ được tăng gấp đôi so với old slice. 

Hãy cũng xem ví dụ dưới
</p>

```golang
1   package main
2
3   import (  
4       "fmt"
5   )
6
7   func main() {  
8       cars := []string{"Ferrari", "Honda", "Ford"}
9       fmt.Println("cars:", cars, "has old length", len(cars), "and capacity", cap(cars)) //capacity of cars is 3
10      cars = append(cars, "Toyota")
11      fmt.Println("cars:", cars, "has new length", len(cars), "and capacity", cap(cars)) //capacity of cars is doubled to 6
12  }
```
<sub>*[Run program in playground](https://play.golang.org/p/VUSXCOs1CF)*</sub>

<p align="justify">

Trong chương trình trên, capacity của `cars` được khởi tạo band đầu là 3. Chúng ta thực hiện append new element vào cars tại line 10 và assign slice được trả về bởi `append(cars, "Toyota")` tới cars một lần nữa. Bây giờ, capacity của cars được gấp đôi lên thành 6. Output của chương trình sẽ là
</p>

```
cars: [Ferrari Honda Ford] has old length 3 and capacity 3  
cars: [Ferrari Honda Ford Toyota] has new length 4 and capacity 6 
```

<p align="justify">

Zero value của một slice type là `nil`. Một `nil` slice có length và capacity là 0. Có thể append các giá trị tới một `nil` slice bằng cách sử dụng append function.
</p>

```golang
1   package main
2
3   import (  
4       "fmt"
5   )
6
7   func main() {  
8       var names []string //zero value of a slice is nil
9       if names == nil {
10          fmt.Println("slice is nil going to append")
11          names = append(names, "John", "Sebastian", "Vinay")
12          fmt.Println("names contents:",names)
13      }
14  }
```
<sub>*[Run program in playground](https://play.golang.org/p/x_-4XAJHbM)*</sub>

<p align="justify">

Trong chương trình trên `names` là nil và chúng ta đã append 3 string vào `names`. Output của program là
</p>

```
slice is nil going to append  
names contents: [John Sebastian Vinay]  
```

<p align="justify">

Chúng ta cũng có thể append một slice tới một slice khác bằng cách sử dụng `...` operator.
</p>

```golang
1   package main
2
3   import (  
4       "fmt"
5   )
6
7   func main() {  
8       veggies := []string{"potatoes","tomatoes","brinjal"}
9       fruits := []string{"oranges","apples"}
10      food := append(veggies, fruits...)
11      fmt.Println("food:",food)
12  }
```
<sub>*[Run program in playground](https://play.golang.org/p/UnHOH_u6HS)*</sub>
<p align="justify">

Tại line 10 của chương trình trên <i>food</i> được tạo ta bằng việc append `fruits` vào `veggies`. Ouput của chương trình là 
</p>

```
food: [potatoes tomatoes brinjal oranges apples]
```

##Passing a slice to a function

<p align="justify">
Slice có thể  được biểu diễn dưới dạng struct. Trông nó sẽ như thế này
</p>

```
type slice struct {  
    Length        int
    Capacity      int
    ZerothElement *byte
}
```
<p align="justify">
Một slice có chứa length, capacity và một pointer tới zero element của array. Khi một slice được truyền tới một function, mặc dù nó được truyền theo value, pointer variable sẽ tham chiếu tới cùng array bên dưới. Do đó khi một slice được truyền tới function như parameter, moi thay đổi được thực hiện bên trong function cũng sẽ visible với bên ngoài function đó.

Chúng ta sẽ cùng xem ví dụ sau để clear hơn:
</p>

```golang
1   package main
2
3   import (  
4       "fmt"
5   )
6
7   func subtactOne(numbers []int) {  
8       for i := range numbers {
9           numbers[i] -= 2
10      }
11
12  }
13  func main() {  
14      nos := []int{8, 7, 6}
15      fmt.Println("slice before function call", nos)
16      subtactOne(nos)                               //function modifies the slice
17      fmt.Println("slice after function call", nos) //modifications are visible outside
18  }
```
<sub>*[Run program in playground](https://play.golang.org/p/IzqDihNifq)*</sub>
<p align="justify">
Function call tại line 17 trong chương trình trên giảm giá trị của mỗi element đi 2. Khi slice được print sau khi function call, chúng ta có thể thấy tất cả thay đổi. Điều này khác với array, khi mọi thay đổi bên trong function sẽ không visible với bên ngoài hàm. Output của chương trình này sẽ là
</p>

```
slice before function call [8 7 6]  
slice after function call [6 5 4]  
```

## Multidimensional slices
<p align="justify">
Tương tư như array, slice có thể  có nhiều chiều
</p>

```golang
1   package main
2   
3   import (  
4       "fmt"
5   )
6
7
8   func main() {  
9        pls := [][]string {
10              {"C", "C++"},
11              {"JavaScript"},
12              {"Go", "Rust"},
13              }
14      for _, v1 := range pls {
15          for _, v2 := range v1 {
16              fmt.Printf("%s ", v2)
17          }
18          fmt.Printf("\n")
19      }
20  }
```
<sub>*[Run program in playground](https://play.golang.org/p/--p1AvNGwN)*</sub>

<p align="justify">
Output của chương trình là
</p>

```
C C++  
JavaScript  
Go Rust 
```

## Memory Optimisation
<p align="justify">
Slice nắm giữ một tham chiếu tới array bên dưới. Miễn slice còn trong memory thì array không thể bị garbage collect. Điều này có thể là điều cần lưu tâm khi quản lý memory. Giả sử rằng chúng ta có một array rất lớn và chúng ta chỉ quan tâm xử lý tới một phần rất nhỏ của nó. Do đó, chúng ta sẽ tạo một slice từ array đó và bắt đầu xử lý slice vừa được tạo ra. Điều quan trọng cần chú ý ở đây là array vẫn sẽ tồn tại trong memory vì slice đang tham chiều tới nó.

Một cách để giải quyết vấn đề này là sử dụng `copy` function `<i>func copy(dst, src []T) int</i>` để tạo ra một slice copy từ slice đó. Với cách này chúng ta có thể sử dụng new slice và original array có thể bị garbage collect
</p>

```golang
1   package main
2
3   import (  
4       "fmt"
5   )
6
7   func countries() []string {  
8       countries := []string{"USA", "Singapore", "Germany", "India", "Australia"}
9       neededCountries := countries[:len(countries)-2]
10      countriesCpy := make([]string, len(neededCountries))
11      copy(countriesCpy, neededCountries) //copies neededCountries to countriesCpy
12      return countriesCpy
13  }
14  func main() {  
15      countriesNeeded := countries()
16      fmt.Println(countriesNeeded)wwTại line 9, `neededCountries := countries[:len(countries)-2]` sẽ tạo ra một slice của `countries`. Line 11 chúng ta thực hiện copy `neededCountries` sang `countriesCpy` và return nó. Bây giờ `countries` array có thể bị garbage collect vì `neededCountries` không còn được tham chiếu.
</p>

<p align="justify">
Đây là tất cả những điều mình chia sẻ về Slice, hy vọng sẽ giúp các bạn có thể hiểu hơn về chúng. Hẹn gặp lại các bạn trong những bài viết tiếp theo.
<br/>
<br/>
<br/>
Reference: https://golangbot.com/arrays-and-slices/
</p>