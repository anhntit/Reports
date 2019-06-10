# [Golang] Arrays And Slices (Part 1)

<p align="justify">Chúng ta sẽ cùng tìm hiểu về Arrays và Slice trong Go. Trong phần 1, tôi sẽ chia sẻ với các bạn về Arrays.</p>

## What is the arrays?

<p align="justify">
Một mảng là một tập hợp các phần tử có cùng kiểu dữ liệu. VD: Tập hợp các số nguyên 5, 8, 9, 79, 76 tạo thành một mảng. Việc trộn lẫn các kiểu dữ liệu khác nhau, VD một mảng chứa cả các phần tử có kiểu dữ liệu string và integer là không được phép trong Go.
</p>

## Declaration

<p align="justify">
Một mảng được biểu thị kiểu dạng <b>n[T]</b>. Trong đó, <b>n</b> là số lượng phần tử của mảng và <b>T</b> là kiểu dữ liệu của các phần tử trong mảng.

Có rất nhiều cách khác nhau để khai báo và khởi tạo một mảng. Chúng ta cùng xem dưới đây:
</p>

```golang
package main

import (  
    "fmt"
)


func main() {  
    var a [3]int //int array with length 3
    fmt.Println(a)
}
```
<sub>*[Run program in playground](https://play.golang.org/p/Zvgh82u0ej)*</sub>
<p align="justify">
<b>var a [3]int</b> khai bảo một mảng integer có độ dài 3. Tất cả các phần tử trong một mảng sẽ tự động mang giá trị <b>zero</b> của kiểu dữ liệu mảng. Trong trường hợp này, <b>a</b> là một mảng integer và do đó tất cả các phần tử của <b>a</b> sẽ mang giá trị <b>0</b>, <b>zero</b> value của integer. Chạy chương trình trên chúng ta sẽ thấy console in ra <b>[0, 0, 0]</b>
</p>
<p align="justify">
Index của mảng sẽ được bắt đầu từ <b>0</b> và kết thúc tại <b>length - 1</b>. Chúng ta sẽ assgin value cho từng phần tử của mảng ở trên.
</p>

```golang
package main

import (  
    "fmt"
)


func main() {  
    var a [3]int //int array with length 3
    a[0] = 12 // array index starts at 0
    a[1] = 78
    a[2] = 50
    fmt.Println(a)
}
```
<sub>*[Run program in playground](https://play.golang.org/p/WF0Uj8sv39)*</sub>
<p align="justify">
<b>a[0]</b> assign giá trị cho phần tử đầu tiên của mảng. Output của chương trình trên là <b>[12, 78, 50]</b>
</p>

<p align="justify">
Chúng ta sẽ tạo một mảng bằng cách sử dụng <b>short hand declaration</b>
</p>

```golang
package main 

import (  
    "fmt"
)

func main() {  
    a := [3]int{12, 78, 50} // short hand declaration to create array
    fmt.Println(a)
}
```
<sub>*[Run program in playground](https://play.golang.org/p/NKOV04zgI6)*</sub>
<p align="justify">
Chương trình trên sẽ có cùng output <b>[12, 78, 50]]</b>

Không nhất thiết tất cả các phần tử trong mảng phải được gán một giá trị khi sử dụng <b>short hand declaration</b>
</p>

```golang
package main

import (  
    "fmt"
)

func main() {  
    a := [3]int{12} 
    fmt.Println(a)
}
```
<sub>*[Run program in playground](https://play.golang.org/p/AdPH0kXRly)*</sub>

<p align="justify">
Trong chương trình trên, <b>a := [3]int{12} </b> khai báo một integer array có độ dài 3 và được cung cấp với duy nhất một giá trị <b>12</b>. Các phần tử còn lại trong mảng sẽ tự động mang <b>zero</b> value. Chương trình có output <b>[12, 0 0].</b>

Bạn thậm chí có thể bỏ qua độ dài của mảng trong khai báo và thay thế nó bằng <b>...</b> và để trình biên dịch tìm độ dài cho bạn. Điều này được thực hiện trong chương trình sau đây.
</p>

```golang
package main

import (  
    "fmt"
)

func main() {  
    a := [...]int{12, 78, 50} // ... makes the compiler determine the length
    fmt.Println(a)
}
```
<sub>*[Run program in playground](https://play.golang.org/p/_fVmr6KGDh)*</sub>
<p align="justify">
Kích thước của mảng là một phần của kiểu mảng. Do đó, <b>[5]int</b> và <b>[25]int</b> là hai kiểu khác nhau. Bới vì điều này nên mảng không thể thay đổi kích thước. 
</p>

```golang
package main

func main() {  
    a := [3]int{5, 78, 8}
    var b [5]int
    b = a //not possible since [3]int and [5]int are distinct types
}
```
<sub>*[Run program in playground](https://play.golang.org/p/kBdot3pXSB)*</sub>
<p align="justify">
Trong chương trình trên, chúng ta đang cố assign một biến có kiểu <b>[3]int</b> cho biến mang kiểu <b>[5]int</b> và điều này không được phép nên chạy chương trình trên ta sẽ thấy compiler sẽ throw một error:
</p>

```
main.go:6: cannot use a (type [3]int) as type [5]int in assignment.
```

## Arrays are value types

<p align="justify">
Array trong Go là một value type, không phải là reference type. Điều này có nghĩa rằng khi chúng ta assign mảng tới một new variable, một bản copy từ mảng ban đầu được assign cho new variable đó. Nếu có thay đổi trên new variable thì mảng ban đầu cũng sẽ không bị ảnh hưởng.
</p>

```golang
package main

import "fmt"

func main() {  
    a := [...]string{"USA", "China", "India", "Germany", "France"}
    b := a // a copy of a is assigned to b
    b[0] = "Singapore"
    fmt.Println("a is ", a)
    fmt.Println("b is ", b) 
}
```
<sub>*[Run program in playground](https://play.golang.org/p/-ncGk1mqPd)*</sub>
<p align="jusitfy">
Trong chương trình trên, một bản copy của mảng <b>a</b> được assign cho <b>b</b>. Sau đó, chúng ta thay đổi giá trị của phần tử đầu tiên trong <b>b</b> thành <b>Singapore</b>. Mảng <b>a</b> không bị thay đổi. Output của chương trình sẽ là:
</p>

```
a is [USA China India Germany France]  
b is [Singapore China India Germany France] 
```
<p align="justify">
Tương tự như khi truyền một mảng tới một function như một parameter.
</p>

```golang
package main

import "fmt"

func changeLocal(num [5]int) {  
    num[0] = 55
    fmt.Println("inside function ", num)

}
func main() {  
    num := [...]int{5, 6, 7, 8, 8}
    fmt.Println("before passing to function ", num)
    changeLocal(num) //num is passed by value
    fmt.Println("after passing to function ", num)
}
```
<sub>*[Run program in playground](https://play.golang.org/p/e3U75Q8eUZ)*</sub>

<p align="justify">
Trong chương trình trên, mảng <b>num</b> được truyền tới <b>changeLocal</b> function. Mảng <b>num</b> sẽ không có bất kỳ thay đổi nào. 
</p>

```
before passing to function  [5 6 7 8 8]  
inside function  [55 6 7 8 8]  
after passing to function  [5 6 7 8 8]  
```

## Length of an array

<p align="justify">
Chúng ta có thể tìm được độ dài của một mảng bằng cách sử dụng <b>len</b> function
</p>

```golang
package main

import "fmt"

func main() {  
    a := [...]float64{67.7, 89.8, 21, 78}
    fmt.Println("length of a is",len(a))

}
```
<sub>*[Run program in playground](https://play.golang.org/p/UrIeNlS0RN)*</sub>
<p align="align">
Output của chương trình trên là:
</p>

```
length of a is 4
```

## Iterating arrays using range

<p align="justify">
Vòng lặp <b>for</b> có thể được sử dụng để duyệt qua tất cả các phần tử của một mảng
</p>

```golang
package main

import "fmt"

func main() {  
    a := [...]float64{67.7, 89.8, 21, 78}
    for i := 0; i < len(a); i++ { //looping from 0 to the length of the array
        fmt.Printf("%d th element of a is %.2f\n", i, a[i])
    }
}
```
<sub>*[Run program in playground](https://play.golang.org/p/80ejSTACO6)*</sub>

<p align="jusitfy">
Chương trình trên sử dụng vòng lặp <b>for</b> để duyệt qua tất cả các phần tử của mảng <b>a</b> từ index <b>0</b> cho tới index <b>length of array - 1</b>. Chương trình này sẽ in ra console:
</p>

```
0 th element of a is 67.70  
1 th element of a is 89.80  
2 th element of a is 21.00  
3 th element of a is 78.00  
```

<p align="justify">
Go cung cấp cho chúng ta một cách tốt hơn để duyệt qua các phần tử có trong mảng, bằng cách sử dụng <b>range</b> form của vòng lặp <b>for</b>. <b>range</b> sẽ trả về cả index và giá trị của phần tử tại index đó.
</p>

```golang
package main

import "fmt"

func main() {  
    a := [...]float64{67.7, 89.8, 21, 78}
    sum := float64(0)
    for i, v := range a {//range returns both the index and value
        fmt.Printf("%d the element of a is %.2f\n", i, v)
        sum += v
    }
    fmt.Println("\nsum of all elements of a",sum)
}
```
<sub>*[Run program in playground](https://play.golang.org/p/Ji6FRon36m)*</sub>

<p align="justify">
<b>for i, v := range a</b> ở trên là một <b>range</b> form của vòng lặp <b>for</b>. Nó sẽ trả về index và giá trị tại index đó. Output của chương trình trên là:
</p>

```
0 the element of a is 67.70  
1 the element of a is 89.80  
2 the element of a is 21.00  
3 the element of a is 78.00

sum of all elements of a 256.5  
```

<p align="justify">
Trong một số trường hợp, chúng ta chỉ cần lấy giá trị các phần tử trong mảng mà không quan tâm tới index của chúng, chúng ta có thể thực hiện điều đó bằng cách thay thế index bằng <b>_</b> blank identifier
</p>

``` golang
for _, v := range a { //ignores index  
}
```

<p align="justify">
Chương trình trên chúng ta bỏ qua index, làm tương tự nếu muốn bỏ qua value
</p>

## Multidimensional arrays

<p align="justify">
Chúng ta có thể tạo một mảng đa chiều
</p>

```golang
package main

import (  
    "fmt"
)

func printarray(a [3][2]string) {  
    for _, v1 := range a {
        for _, v2 := range v1 {
            fmt.Printf("%s ", v2)
        }
        fmt.Printf("\n")
    }
}

func main() {  
    a := [3][2]string{
        {"lion", "tiger"},
        {"cat", "dog"},
        {"pigeon", "peacock"}, //this comma is necessary. The compiler will complain if you omit this comma
    }
    printarray(a)
    var b [3][2]string
    b[0][0] = "apple"
    b[0][1] = "samsung"
    b[1][0] = "microsoft"
    b[1][1] = "google"
    b[2][0] = "AT&T"
    b[2][1] = "T-Mobile"
    fmt.Printf("\n")
    printarray(b)
}
```
<sub>*[Run program in playground](https://play.golang.org/p/InchXI4yY8)*</sub>

<p align="justify">
Trong chương trình trên, một mảng <b>a</b> hai chiều được khởi tạo với cú pháp short hand declaration. Một mảng hai chiều <b>b</b> cũng được khởi tạo, và từng giá trị string được gán cho từng index. Đây là một cách khác để khởi tạo một mảng hai chiều.

Function <b>printarray</b> sử dụng hai vòng lặp <b>for range</b> để in nội dung của mảng hai chiều. Output của chương trình trên:
</p>

```
lion tiger  
cat dog  
pigeon peacock 

apple samsung  
microsoft google  
AT&T T-Mobile  
```

<p align="jusitfy">
Trên đây là những chia sẻ về  Array trong Go. Mặc dù array chưa đủ linh hoạt, hạn chế bởi chúng có độ dài cố định. Không thể tăng chiều dài của một mảng. Trong thực tế <b>Slices</b> sẽ được sử dụng nhiều hơn.

Vậy <b>Slices</b> là gì, nó có ưu điểm gì so với <b>Array</b>, mình sẽ chia sẻ với các bạn trong bài viết kế tiếp. Hẹn gặp lại.

Reference: https://golangbot.com/arrays-and-slices/
</p>