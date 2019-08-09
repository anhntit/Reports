## [Golang] Variadic Functions

<p align="justify">
Xin chào các bạn, hôm nay mình lại tiếp tục chia sẻ về Golang nhé.

Topic lần này là về  `Variadic Functions`
</p>

### What is a variadic function?
<p align="justify">

Các function thông thường chỉ chấp nhận một số lượng argument cố định. Một `variadic function` là một function mà chấp nhận một số lượng argument thay đổi. Nếu parameter của một function definition có tiền tố là `...`, thì function đó có thể chấp nhận bất kỳ số lượng argument cho parameter đó.
</p>

<p align="justify">

Chỉ parameter cuối cùng của một function có thể là `variadic`. Chúng ta sẽ biết lý do tại sao trong những phần tiếp theo của bài viết này. 
</p>

### Syntax
```golang
func hello(a int, b ...int) {

}
```
<p align="justify">

Trong function trên, parameter `b` là variadic vì nó có tiền tố là `...` mà nó có thể chấp nhận bất ký số lượng các argument. Function này có thể được gọi bằng cách sử dụng syntax
</p>

```golang
hello(1, 2) //passing one argument "2" to b  
hello(5, 6, 7, 8, 9) //passing arguments "6, 7, 8 and 9" to b 
```

<p align="justify">

Trong ví dụ đầu tiên, chúng ta gọi `hello` với một argument `2` cho parameter `b` avaf truyền 4 argument `6, 7, 8, 9` tới `b` trong ví dụ tiếp theo.

Chúng ta cũng có thể không truyền argument cho variadic function
</p>

```golang
hello(1)
```

<p align="justify">

Trong ví dụ trên, chúng ta gọi `hello` với zero argument cho `b`. Điều này hoàn toàn hợp lệ

Bây giờ tôi đoán rằng bạn đã hiểu lý do tại sao variadic parameter nên nằm cuối cùng

Hãy thử tạo parameter đầu tiên của `hello` function là variadic.

Cú pháp sẽ trông thế này
</p>

```golang
func hello(b ...int, a int) {  
}
```

<p align="justify">

Trong function trên, chúng ta không thể truyền parameter cho `a` bởi vì bất kỳ argument nào chúng ta truyền vào sẽ được assign cho parameter đầu tiên `b` vì nó là variadic. Do đó variadic parameter chỉ có thể nằm cuối cùng trong function definition. Function ở trên cũng sẽ compile fail với error `syntax error: cannot use ... with non-final parameter b`
</p>

### Examples and understanding how variadic functions work
<p align="justify">

Hãy thử tạo một variadic function. Chúng ta sẽ viết một chương trình đơn giản để tìm một số integer trong list các số integer đầu vào.
</p>

```golang
1.  package main
2.
3.  import (  
4.      "fmt"
5.  )
6.
7.  func find(num int, nums ...int) {  
8.      fmt.Printf("type of nums is %T\n", nums)
9.      found := false
10.      for i, v := range nums {
11.         if v == num {
12.             fmt.Println(num, "found at index", i, "in", nums)
13.             found = true
14.         }
15.     }
16.     if !found {
17.         fmt.Println(num, "not found in ", nums)
18.     }
19.     fmt.Printf("\n")
20. }
21. func main() {  
22.     find(89, 89, 90, 95)
23.     find(45, 56, 67, 45, 90, 109)
24.     find(78, 38, 56, 98)
25.     find(87)
26. }
```
<sub>*[Run program in playground](https://play.golang.org/p/7occymiS6s)*</sub>

<p align="justify">

Trong chương trình trên, `func find(num int, nums ...int)` cho phép một số lượng bất kỳ argument cho `nums` parameter. Bên trong function `find`, kiểu của `nums` là `[]int` (vd một integer slice)

Cách mà variadic function làm việc là convert số lượng các argument thành một slice có type của variadic parameter. Trong ví dụ trên, số lượng đối số truền vào `find` function là `89, 90, 95`. `Find` function expect một variadic `int` argument. Do đó ba argument này sẽ được convert bởi complier thành một slice của type int `[int]{89, 90, 95}` và sau đó nó sẽ được truyền tới `find` function

Trong line 10, vòng lặp `for` qua `nums` slice và in ra vị trí của `num` nếu nó xuất hiện trong slice. Nếu không, nó sẽ in ra thông báo number không được tìm thấy

Output của chương trình trên
</p>

```
type of nums is []int  
89 found at index 0 in [89 90 95]

type of nums is []int  
45 found at index 2 in [56 67 45 90 109]

type of nums is []int  
78 not found in  [38 56 98]

type of nums is []int  
87 not found in  []  
```

<p align="justify">

Trong line 25 của chương trình trên, `find` function chỉ được gọi với duy nhất một argument. chúng ta sẽ truyền bất kỳ argument nào tới variadic `nums ...int` parameter. Như đã thảo luận ở trên, điều này hoàn toàn hợp lề, trong trường hợp này `nums` sẽ là `nil` slice với length và capacity là `0`
</p>

### Slice arguments vs Variadic arguments
<p align="justify">

Trong phần trước chúng ta biết rằng variadic function sẽ được convert thành thành một slice. Vậy tại sao chúng ta cần variadic function trong khi chúng ta cũng có thể định nghĩa một function tương tự bằng cách sử dụng slice? Chúng ta sẽ viết lại chương trình trên bằng cách sử dụng slice như sau
</p>

```golang
1.  package main
2.
3.  import (  
4.      "fmt"
5.  )
6.
7.  func find(num int, nums []int) {  
8.      fmt.Printf("type of nums is %T\n", nums)
9.      found := false
10.     for i, v := range nums {
11.         if v == num {
12.             fmt.Println(num, "found at index", i, "in", nums)
13.             found = true
14.         }
15.     }
16.     if !found {
17.         fmt.Println(num, "not found in ", nums)
18.     }
19.     fmt.Printf("\n")
20. }
21. func main() {  
22.     find(89, []int{89, 90, 95})
23.     find(45, []int{56, 67, 45, 90, 109})
24.     find(78, []int{38, 56, 98})
25.     find(87, []int{})
26. }
```
<sub>*[Run program in playground](https://play.golang.org/p/rG-XRL3yycJ)*</sub>

<p align="justify">

Dưới đây sẽ là những lợi ích khi sử dụng variadic argument thay vì sử dụng slice.

1. Chúng ta không cần phải tạo ra một slice trong mỗi lần gọi hàm. Nếu bạn nhìn vào chương trình trên, chúng ta phải tạo slice mới cho mỗi lần gọi hàm tại line 22, 23, 24 và 25. Việc tạo slice có thể được loại bỏ khi sử dụng variadic function

2. Trong line 25 của chương trình trên, chúng ta đang tạo một empty slice chỉ để hợp lệ với `find` function definition. Điều này thực sự là không cần thiết khi sử dụng variadic function.

3. Theo cảm giác chủ quan thì chương trình sẽ dễ đọc hơn khi sử dụng variadic function hơn là sử dụng slice.
</p>

### Append is a variadic function
<p align="justify">

Có bao giờ chúng ta tự hỏi làm thế nào mà `append` function trong thư viện tiêu chuẩn golang được sử dụng để append bất kỳ đối số argument vào một slice. Bởi vì nó là một variadic function
</p>

```
func append(slice []Type, elems ...Type) []Type  
```

<p align="justify">

Ở trên chính là definition của `append` function. Trong definition này `elems` là một variadic parameter. Do đó append có thể chấp nhận bất kỳ số lượng argument
</p>

### Passing a slice to a variadic function
<p align="justify">

Hãy truyền một slice tới một variadic function và xem những gì xảy ra trong ví dụ dưới đây
</p>

```golang
1.  package main
2.  
3.  import (  
4.      "fmt"
5.  )
6.  
7.  func find(num int, nums ...int) {  
8.      fmt.Printf("type of nums is %T\n", nums)
9.      found := false
10.     for i, v := range nums {
11.         if v == num {
12.             fmt.Println(num, "found at index", i, "in", nums)
13.             found = true
14.         }
15.     }
16.     if !found {
17.         fmt.Println(num, "not found in ", nums)
18.     }
19.     fmt.Printf("\n")
20. }
21. func main() {  
22.     nums := []int{89, 90, 95}
23.     find(89, nums)
24. }
```
<sub>*[Run program in playground](https://play.golang.org/p/A-DNilpH2L)*</sub>

<p align="justify">

Trong line 23, chúng ta đang truyền một slice tới một function mà expect là một số lượng bất kỳ argument.

Chương trình trên sẽ compile fail với error `./prog.go:23:10: cannot use nums (type []int) as type int in argument to find`

Tại sao nó lại không làm viêc? Signature của `find` function như sau
</p>

```golang
func find(num int, nums ...int) 
```

<p align="justify">

Theo definition của variadic function, `nums ...int` có nghĩa rằng nó sẽ chấp nhận một số lượng bất kỳ argument có type là `int`

Trong line 23 của chương trình trên, `nums` ở đây là một `[]int` slice được truyền tới `find` function có expect là một variadic `int` argument. Như chúng ta đã thảo luận, những variadic argument sẽ được convert thành một slice của type `[int]` vì `find` expect variadic int argument. Trong trường hợp này, `nums` đã là một `[]int` slice và compiler sẽ tạo một new `[]int`, ví dụ
</p>

```golang
find(89, []int{nums})  
```
<p align="justify">

Điều này sẽ fail vì `nums` là một `[]int` không phải là `int`

Vậy có cách nào để truyền một slice tới một variadic function? Câu trả lời là có.

Có một cú pháp có thể được sử dụng để truyền một slice tới một variadic function. Chúng ta phải suffix slice với `...` Nếu điều này được thực hiện, slice được truyền trực tiếp tới funciton, sẽ không có một slice mới nào được tạo ra.

Trong chương trình trên nếu chúng ra replace `find(89, nums)` trong line 23 thành `find(89, nums...)`, chương trình sẽ compile và print output như sau
</p>

```
type of nums is []int  
89 found at index 0 in [89 90 95]  
```

<p align="justify">
Dưới đây là chương trình đầy đủ
</p>

```golang
1.  package main
2.  
3.  import (  
4.      "fmt"
5.  )
6.
7.  func find(num int, nums ...int) {  
8.      fmt.Printf("type of nums is %T\n", nums)
9.      found := false
10.     for i, v := range nums {
11.         if v == num {
12.             fmt.Println(num, "found at index", i, "in", nums)
13.             found = true
14.         }
15.     }
16.     if !found {
17.         fmt.Println(num, "not found in ", nums)
18.     }
19.     fmt.Printf("\n")
20. }
21. func main() {  
22.     nums := []int{89, 90, 95}
23.     find(89, nums...)
24. }
```
<sub>*[Run program in playground](https://play.golang.org/p/IvzwhzhFsT)*</sub>

###Gotcha
<p align="justify">

Chỉ chắc chắn rằng bạn đang thực hiện những gì khi thay đổi một slice bên trong một variadic function

Hãy cùng xem ví dụ sau
</p>

```golang
1.  package main
2.  
3.  import (  
4.      "fmt"
5.  )
6.  
7.  func change(s ...string) {  
8.      s[0] = "Go"
9.  }
10.
11. func main() {  
12.     welcome := []string{"hello", "world"}
13.     change(welcome...)
14.     fmt.Println(welcome)
15. }
```
<sub>*[Run program in playground](https://play.golang.org/p/R0GsuW7rdd)*</sub>

<p align="justify">

Bạn nghĩ output của chương trình trên là gì? Nếu bạn nghĩ nó sẽ là `[Go world]` thì chúc mừng, bạn đã hiểu được variadic function và slice. Nếu bạn hiểu sai, không sao tôi sẽ giải thích cho bạn.

Tại line 13 của chương trình trên, chúng ta đang sử dụng cú pháp `...` và đang truyền một slice như một variadic argument tới `change` function.

Như chúng ta đã thảo luận, nếu `...` được sử dụng, `welcome` slice sẽ được truyền như một argument mà không có một slice mới nào được tạo. Do đó `welcome` sẽ được truyền tới `change` function như argument.

Bên trong inside change function, phần tử đầu tiên của slice được thay đổi thành `Go`. Do đó output của chương trình sẽ là
</p>

```
[Go world]
```

<p align="justify">
Ở đây là một chương trình khác để hiểu về variadic function
</p>

```golang
1.  package main
2.  
3.  import (  
4.      "fmt"
5.  )
6.
7.  func change(s ...string) {  
8.      s[0] = "Go"
9.      s = append(s, "playground")
10.     fmt.Println(s)
11. }
12.
13. func main() {  
14.     welcome := []string{"hello", "world"}
15.     change(welcome...)
16.     fmt.Println(welcome)
17. }
```
<sub>*[Run program in playground](https://play.golang.org/p/WdbFIkdLoe)*</sub>

<p align="justify">
Theo bạn thì output của chương trình trên là gì? Hãy thử suy nghĩ xem nhé để hiểu hơn về variadic function.

Đây là tất cả về variadic function. Cảm ơn tất cả các bạn đã đọc bài viết. Hẹn gặp lại trong những bài viết sau.

<br/>
<br/>
<br/>
Reference: https://golangbot.com/variadic-functions/
</p>