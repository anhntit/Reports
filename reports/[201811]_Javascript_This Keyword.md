# [Javascript] This Keyword

<p align="justify">
JavaScript là một trong những ngôn ngữ phổ biến, mạnh mẽ và có sức lan tỏa nhất. Thế giới của Javascript cũng đang phát triển một cách chóng mặt.

Học Javascript rất dễ, rất nhanh nhưng để thực sự hiểu nó thì không phải là một điều dễ dàng.

<i>this</i> - bạn đã bao giờ nghe tới từ khóa này chưa, nếu chưa thì chắc chắn bạn không phải là một lập trình viên. 

Chúng ta nên cẩn thận khi sử dụng từ khóa <i>this</i> khi viết code Javascript vì chúng rất dễ gây nhầm lẫn.

Bài viết này, tôi và các bạn sẽ cùng thảo luận về từ khóa <i>this</i> trong Javascript.

Trong Javascript, khi sử dụng từ khóa <i>this</i> có nghĩa rẳng chúng ta đang tham chiếu tới một Object - đối tượng gọi hàm và thực thi hàm.

Nói vậy có vẻ trừu tượng đúng không, chúng ta sẽ cùng xem những ví dụ cơ bản sau đây để có thể hiểu rõ hơn.
</p>

```javascript
1   candidate = {
2       fullName: "Nguyen Tuan Anh",
3       age: 25,
4       skills: ["Java", "Javascript"],
5       displayInfos: function() {
6           console.log(this.fullName + " - " + this.age);
7       }
8   }
9   //Ở đây, candidate là đối tượng call displayInfos()
10  //nên this trong function displayInfor chính là candidate
11  candidate.displayInfos(); //Nguyen Tuan Anh - 25
```
Dưới đây là một ví dụ khác:

```javascript
1   candidate = {
2       fullName: "Nguyen Tuan Anh",
3       age: 25,
4       skills: ["Java", "Javascript"],
5       displayInfos: function() {
6           console.log(this.fullName + " - " + this.age);
7       },
8       displaySkills: function() {
9           //this ở đây tham chiếu tới candidate
10          this.skills.forEach(function(skill) {
11          //this ở đây nằm trong inner function nên không thể tham chiếu tới this của outerfunction.
12          //không có object call hay được tham chiếu tới, this ở đây chính là Window object
13              console.log(this.fullName + "'s skill: " + skill);
14          })
15      }
16  }
17  //undefined's skill: Java
18  //undefined's skill: Javascript
19  candidate.displaySkills();
```
Một ví dụ khác thú vị hơn về việc thay đổi context của <i>this</i>:

```javascript
1   var fullName = "full name global";
2   var age = "age global";
3
4   candidate = {
5       fullName: "Nguyen Tuan Anh",
6       age: 25,
7       skills: ["Java", "Javascript"],
8       displayInfos: function() {
9           console.log(this.fullName + " - " + this.age);
10      }
11  }
12
13  //gán displayInfos function của candidate vào một biến
14  var displayInfosFn = candidate.displayInfos;
15  //execute displayInfosFn
16  //như đã nói ở ví dụ trên, ở đây không có đối tượng gọi hàm nên this sẽ là Window object
17  //khi đó nó sẽ tham chiếu được tới giá trị của các global variable được khai báo ở trên 
18  displayInfosFn(); //full name global - age global
```
Hay một ví dụ mà chúng ta bắt gặp rất nhiều và cũng sử dụng rất nhiều, nhưng có khi ta lại chưa hiểu bản chất của nó. Bạn có đoán được đó là ví dụ nào không? Chính là khi ta sử dụng this với Jquery:

```javascript
1   $("button").click(function(event) {
2       // $(this) sẽ tham chiếu tới đối tượng button 
3       // bởi vì đối tượng này gọi phương thức click()
4       console.log($(this).attr("id"));
5   });
```
<p align="justify">
Ở đây, tuy <i>this</i> được gọi trong anonymous function nhưng Jquery đã gán <i>$(this)</i> cho đối tượng gọi <i>click()</i> function (button element), dó đó <i>$(this)</i> sẽ tham chiếu tới button object.
<br/>
<br/>
Trên đây là một số ví dụ về từ khóa this trong Javascript, hy vọng nó sẽ giúp các bạn hiểu hơn về từ khóa này trong Javascript.

Hẹn gặp lại các bạn trong những bài viết chia sẻ tiếp theo. 
</p>