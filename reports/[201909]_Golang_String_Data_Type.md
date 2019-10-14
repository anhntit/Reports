# String Data Type in Go

<p align="justity">
Xin chào các bạn, một lần mình thực hiện thao tác lấy độ dài của một chuỗi tiếng Nhật trong Go và thấy nó không chính xác so với số ký tự của chuỗi đó. Mình rất thắc mắc tại sao lại như vậy và sau khi tìm hiểu mình đã có câu trả lời.

Hôm nay, chúng ta cùng tìm hiểu về String trong Go nhé.
</p>

```golang
package main

import (
	"fmt"
)

func main() {
	hello := "こんにちは"
	fmt.Println(len(hello))
}
```
Liệu kết quả của ví dụ trên có phải là `5`?


## String in Go

<p align="justify">
`Strings` trong Go khá đặc biệt vì nó được implement khác so với các ngôn ngữ khác.

Strings được định nghĩa giữa hai dấu nháy kép `"..."` chứ không phải dấu nháy đơn, không giống như Javascript. Strings trong Go mặc định được encode `UTF-8`. Vì `UTF-8` hỗ trợ `ASCII character set`, nên chúng ta không cần phải bận tâm về việc encode.

Hãy thử viết một chương trình đơn giản. Định nghĩa một empty variable có kiểu `string`, sử dụng `string` keyword.
</p>

```golang
package main

import "fmt"

func main() {
	var s string

	s = "Hello World"

	fmt.Println(s)
}
```
<sub>*[Run program in playground](https://play.golang.org/p/vMDoeaV3RCY)*</sub>

<p align="justify">

Để tìm độ dài của một chuỗi, chúng ta có thể sử dụng `len` function. `len` function được cung cấp trong Go runtime, vì vậy mà chúng ta không cần phải import chúng.
</p>

```golang
package main

import "fmt"

func main() {
	s := "Hello World"

	fmt.Println(len(s))
}
```
<sub>*[Run program in playground](https://play.golang.org/p/Kqj-TJMFyXP)*</sub>

<p align="justify">

Chương trình trên sẽ in `11` ra console, đây chính là độ dài của `s`, space cũng được coi như một ký tự. Tất cả các ký tự trong chuỗi `Hello World` đều là những ký tự `ASCII` hợp lệ, do đó chúng ta thấy rằng mỗi character chỉ chiếm 1 byte trong bộ nhớ (<i>các ký tự ASCII trong UTF-8 sẽ chiếm 8 bit hoặc 1 byte</i>)
</p>

```golang
package main

import "fmt"

func main() {
	s := "Hello World"

	for i := 0; i < len(s); i++ {
		fmt.Print(s[i], " ")
	}

	fmt.Println()
}
```
<sub>*[Run program in playground](https://play.golang.org/p/cE32NenaYmN)*</sub>

```
72 101 108 108 111 32 87 111 114 108 100 
```

<p align="justify">

Chắc chúng ta đang hy vọng rằng `s[i]` sẽ là từng chữ cái trong chuỗi `s`. Vậy đây là gì? Đây là giá trị decimal của các ký tự ASCII/UTF-8 trong chuỗi `Hello World` (xem thêm http://www.asciichart.com)

Trong Go, một chuỗi là một read-only slice của các byte. Do đó trong trường hợp trên, chúng ta thấy rằng các giá trị byte của chuỗi `s` được chứa bên trong một slice. Và vì vậy `s[i]` sẽ in giá trị decimal của các byte tương ứng với từng ký tự. Để in ra ký tự chúng ta có thể sử dụng `%c` format trong `Printf` statement. Chúng ta cũng có thể sử dụng `%v` format để biết giá trị byte hay `%T` để biết được kiểu dữ liệu
</p>

```golang
package main

import "fmt"

func main() {
	s := "Hello World"
	fmt.Println("len(s)", len(s))
	
	for i := 0; i < len(s); i++ {
		fmt.Printf("%c ", s[i])
	}

	fmt.Println("")

	for i := 0; i < len(s); i++ {
		fmt.Printf("%v ", s[i])
	}

	fmt.Println("")

	for i := 0; i < len(s); i++ {
		fmt.Printf("%x ", s[i])
	}

	fmt.Println("")
        for i := 0; i < len(s); i++ {
		fmt.Printf("%T ", s[i])
	}

	fmt.Println("")
}
```
<sub>*[Run program in playground](https://play.golang.org/p/wwqhgHcTeIU)*</sub>

```
len(s) 11
H e l l o   W o r l d 
72 101 108 108 111 32 87 111 114 108 100 
48 65 6c 6c 6f 20 57 6f 72 6c 64 
uint8 uint8 uint8 uint8 uint8 uint8 uint8 uint8 uint8 uint8 uint8
```

<p align="justify">

Chúng ta có thể thấy rằng mỗi chữ cái hiển thị số thập phân chiếm 8 bit hay 1 byte trong memory và chúng có kiểu `uint8`.

Ký tự UTF-8 có thể được định nghĩa size trong memory từ 1 byte (ASCII compatible) cho tới 4 byte. Do đó trong Go, tất cả các ký tự đều được thể hiện dưới kiểu dữ liệu `int32` (size of 4 byte). 

Một `code unit` là một số lượng bit mã hóa cho một single unit cell. Vì vậy UTF-8 sử dụng 8 bit và UTF-16 sử dụng 16 bit cho một `code unit`, điều này nghĩa rằng UTF-8 cần ít nhất 8 bit hay 1 byte để biểu diễn ký tự.

Một `code point` là một giá trị số bất kỳ định nghĩa ký tự và nó được biểu diễn bởi một hoặc nhiều `code unit` tùy thuộc vào encoding. Vì UTF-8 tương thích với ASCII, tất cả các ký tự ASCII được biểu diễn trong một byte (8 bits), do đó UTF-8 chỉ cần một `code unit` để thể hiện chúng.

Nhưng có một câu hỏi lớn hơn, nếu tất cả các ký tự trong UTF-8 được thể hiện dạng `int32` thì tại sao chúng ta lại thấy kiểu `uint8` trong ví dụ trên. Như chúng ta đã biết, trong Go, chuỗi là một read-only slice của các byte. Khi chúng ta sử dụng `len` function, nó sẽ tính toán độ dài của slice đó. Khi chúng ta sử dụng vòng lặp `for`, nó sẽ lặp qua slice và trả về một byte hoặc một `code unit` tại một thời điểm. 

Tất cả các ví dụ trên, chúng ta đều sử dụng các ký tự trong bộ ký tự ASCII, byte được cung cấp bởi vòng lặp `for` là một ký tự hợp lệ hoặc một `code unit`. Do đó, `%c` trong `Printf` statement có thể in ra một ký tự từ giá trị byte đó. Tuy nhiên, UTF-8 `code point` hoặc character value có thể được biểu diễn bởi một hoặc nhiều byte (max 4 byte), vậy thì điều gì sẽ xảy ra với các ví dụ ở trên nếu chúng ta sử dụng các ký tự không phải trong bộ ký tự ASCII?

Hãy thử thay thế `o` trong `Hello World` thành `õ` (LATIN SMALL LETTER O WITH TILDE, http://www.utf8-chartable.de) có Unicode code point đại diện cho `U+00F5` và nó được biểu diễn bởi 2 code unit (2 byte) `c3 b5` (hexadecimal representation). Vì vậy, thay vì giá trị `6f` cho ký tự `o`, chúng ta hy vọng `c3 b5` cho ký tự `õ`.
</p>

```golang
package main

import "fmt"

func main() {
    s := "Hellõ World"
	fmt.Println("len(s)", len(s))
	
	for i := 0; i < len(s); i++ {
		fmt.Printf("%c ", s[i])
	}
	
	fmt.Println("")

	for i := 0; i < len(s); i++ {
		fmt.Printf("%v ", s[i])
	}

	fmt.Println("")

	for i := 0; i < len(s); i++ {
		fmt.Printf("%x ", s[i])
	}

	fmt.Println("")
}
```
<sub>*[Run program in playground](https://play.golang.org/p/rhueGpn4pDc)*</sub>

```
len(s) 12
H e l l Ã µ   W o r l d 
72 101 108 108 195 181 32 87 111 114 108 100 
48 65 6c 6c c3 b5 20 57 6f 72 6c 64
```

<p align="justify>

Kết quả trên ta thấy `c3 b5` đã thay thế `6f` tuy nhiên các ký tự của `Hellõ World` không được in ra một cách chính xác. Chúng ta cũng có thể thấy rằng `len(s)` trả về `12` bởi vì `len` đếm số lượng byte trong chuỗi. Do đó `c3 (decimal 195` trong UTF-8 đại diện cho `Ã` và `b5 (decimal 181)` đại diện cho `µ` (<a  href="https://www.obliquity.com/computer/html/unicode0000.html">check here</a>)

Để tránh điều trên xảy ra, Go giới thiệu kiểu dữ liệu `rune` (đồng nghĩa với `code point`) là một alias của `int32`
</p>

```golang
package main

import "fmt"

func main() {
	s := "Hellõ World"
	r := []rune(s)

	fmt.Println("len(r)", len(r))

	for i := 0; i < len(r); i++ {
		fmt.Printf("%c ", r[i])
	}
	fmt.Println("")

	for i := 0; i < len(r); i++ {
		fmt.Printf("%v ", r[i])
	}
	fmt.Println("")

	for i := 0; i < len(r); i++ {
		fmt.Printf("%x ", r[i])
	}
	fmt.Println("")

	for i := 0; i < len(r); i++ {
		fmt.Printf("%T ", r[i])
	}
	fmt.Println("")
}
```
<sub>*[Run program in playground](https://play.golang.org/p/ELgL-upVnz_r)*</sub>

```
len(r) 11
H e l l õ   W o r l d 
72 101 108 108 245 32 87 111 114 108 100 
48 65 6c 6c f5 20 57 6f 72 6c 64 
int32 int32 int32 int32 int32 int32 int32 int32 int32 int32 int32 
```

<p align="justify">

Chúng ta đã chuyển đổi một chuỗi thành một slice rune bằng cách sử dụng `type conversion`. Kết qủa trên chúng ta thấy `f5` thay vì `c3 b5` vì chúng ta đang duyệt qua `rune` và code point của `õ` trong UTF-8 là `f5`. Và chúng ta thấy length của `s` chính xác là `11`, vì có 11 rune trong slice (hoặc 11 code point hay 11 ký tự). Và chúng ta cũng thấy rằng code point hay ký tự trong Go được biểu diễn theo kiểu dữ liệu `int32`
</p>

## for loop on string
<p align="justify">

Nếu chúng ta sử dụng `range` trong vòng lặp `for` thì nó sẽ trả về `rune` và byte index của ký tự
</p>

```golang
package main

import "fmt"

func main() {
	s := "Hellõ World"

	for index, char := range s {
		fmt.Printf("character at index %d is %c\n", index, char)
	}
}
```
<sub>*[Run program in playground](https://play.golang.org/p/Xet2cJbywLH)*</sub>

```
character at index 0 is H
character at index 1 is e
character at index 2 is l
character at index 3 is l
character at index 4 is õ
character at index 6 is  
character at index 7 is W
character at index 8 is o
character at index 9 is r
character at index 10 is l
character at index 11 is d
```

<p align="justify">

Trong chương trình trên, chúng ta thấy không có index `5` là vì byte thứ 5 là hai `code unit` của ký tự `õ`. 
</p>

## What is rune
<p align="justify">

String là một slice của byte. Khi chúng ta sử dụng vòng lặp `for` với `range`, chúng ta sẽ get được `rune` bởi vì mỗi ký tự trong chuỗi được thể hiện bởi kiểu dữ liệu `rune`. Trong Go, một ký tự được biểu diễn giữa dấu nháy đơn. Do đó, bất kỳ một ký tự UTF-8 hợp lệ bên trong một dấu nháy đơn `(')` là một `rune` và nó có kiểu `int32`
</p>

```golang
package main

import "fmt"

func main() {
	r := 'õ'
	fmt.Printf("%x ", r)
	fmt.Printf("%v ", r)
	fmt.Printf("%T", r)

	fmt.Println()
}
```
<sub>*[Run program in playground](https://play.golang.org/p/QNBsDunKTrJ)*</sub>

<p align="justify">

Chương trình trên sẽ in ra `f5 245 int32`, đây là giá trị hexadecimal/decimal và kiểu dữ liệu của `code point`
</p>

## Strings are immutable
<p align="justify">

Như chúng ta đã biết, chuỗi trong Go là một read-only slice của byte, do đó nếu chúng ta cố gắng thay đổi byte trong slice thì compiler sẽ throw một error.
</p>

```golang
package main

import "fmt"

func main() {
	s := "Hello World"

	s[0] = 'F'
	
	fmt.Println(s)
}
```
<sub>*[Run program in playground](https://play.golang.org/p/9Uu5LqNqVkb)*</sub>

<p align="justify">

Chương trình trên sẽ không compile và compiler sẽ throw một error `cannot assign to s[0]` 
</p>

## String literals using backtick
<p align="justify">

Thay vì sử dụng dấu nháy kép `(")` chúng ta cũng có thể sử dụng backtick ``(`)`` để thể hiện một string trong Go. Trong `(")` chúng ta phải escape newline, tabs và các ký tự khác không cần phải escape trong backtick. Nếu chúng ta có một line break trong một backtick string, nó sẽ được hiểu là ký tự `\n`. (xem thêm https://golang.org/ref/spec#String_literals)

Hãy xem ví dụ sau
</p>

```golang
package main

import "fmt"

func main() {
	s := `Hello,\n
		My Big Blue
	"World"!`

	fmt.Println(s)
}
```
<sub>*[Run program in playground](https://play.golang.org/p/9Ir-0Lxx0u3)*</sub>

```
Hello,\n
		My Big Blue
	"World"!
```

<p align="justify">

Chúng ta thấy rằng định dạng ban đầu của chuỗi với newline, tab hoặc dấu nháy kép vẫn xuất hiện trong output và ký tự newline `\n` không thực hiện gì cả.
</p>

## Character comparison
<p align="justify">

Một ký tự được thể hiện trong một dấu nháy đơn trong Go là `rune` và rune có thể được so sánh bởi thì nó đại diện cho Unicode code point (`int32` value). Do đó nếu một ký tự có nhiều hơn một decimal value, nó sẽ lớn hơn ký tự có ít value hơn.

Hãy cùng xem ví dụ
</p>

```golang

package main

import (
	"fmt"
)

func main() {
	fmt.Printf("value of character a is %v of type %T\n", 'a', 'a')
	fmt.Printf("value of character b is %v of type %T\n", 'b', 'b')
	fmt.Println("hence 'b' > 'a' is", 'b' > 'a')
}
```
<sub>*[Run program in playground](https://play.golang.org/p/lxGiJzNeNWO)*</sub>

```
value of character a is 97 of type int32
value of character b is 98 of type int32
hence 'b' > 'a' is true
```

<p align="justify">

Vì giá trị `int32` của `b` lớn hơn `a`, nên expression `'b' > 'a'` sẽ là `true`.
</p>

```golang
package main

import (
	"fmt"
)

func main() {
	fmt.Printf("value of character a is %v of type %T\n", 'a', 'a')
	fmt.Printf("value of character A is %v of type %T\n", 'A', 'A')
	fmt.Println("hence 'A' > 'a' is", 'A' > 'a')

	fmt.Printf("\nvalue of character ℻ is %v of type %T\n", '℻', '℻')
	fmt.Printf("value of character ™ is %v of type %T\n", '™', '™')
	fmt.Println("hence '℻' > '™' is", '℻' > '™')
}
```
<sub>*[Run program in playground](https://play.golang.org/p/aw8Sv8Vto-c)*</sub>

```
value of character a is 97 of type int32
value of character A is 65 of type int32
hence 'A' > 'a' is false

value of character ℻ is 8507 of type int32
value of character ™ is 8482 of type int32
hence '℻' > '™' is true
```

<p align="justify">Một ví dụ khác</p>

```golang
package main

import (
	"fmt"
)

func main() {
	for i := 'a'; i < 'g'; i++ {
		fmt.Printf("character = '%c' with decimal value %v\n", i, i)
	}
}
```

```
character = 'a' with decimal value 97
character = 'b' with decimal value 98
character = 'c' with decimal value 99
character = 'd' with decimal value 100
character = 'e' with decimal value 101
character = 'f' with decimal value 102
```

<p align="justify">

Đây là những điều cơ bản về string trong Go, nó có rất nhiều function hữu ích được cung cấp bởi `strings` package, chúng ta có thể sử dụng để thực hiện thao tác với `string` như `join`, `replace`, `search` ...

Hẹn gặp lại các bạn ở bài viết sau nhé.
</p>
