### [Database][MySQL] Generated Columns 

<p align="justify">

CREATE TABLE hỗ trợ xác định các đặc điểm của `generated column`. Giá trị của một `generated column` được tính toán từ biểu thức của các column được định nghĩa.

Generated columns được hỗ trợ bởi `NDB` storage engine bắt đầu với `MySQL NDB Cluster 7.5.3`.

Ví dụ đơn giản sau, một table sẽ lưu trữ độ dài các cạnh của một tam giác vuông `sidea` và `sideb`, và độ dài của cạnh huyền `sidec` (căn bậc 2 tổng bình phương 2 cạnh góc vuông):
</p>

```sql
CREATE TABLE triangle (
  sidea DOUBLE,
  sideb DOUBLE,
  sidec DOUBLE AS (SQRT(sidea * sidea + sideb * sideb))
);
INSERT INTO triangle (sidea, sideb) VALUES(1,1),(3,4),(6,8);
```

Thực hiện câu select và xem kết quả:

```sql
SELECT * FROM triangle;
+-------+-------+--------------------+
| sidea | sideb | sidec              |
+-------+-------+--------------------+
|     1 |     1 | 1.4142135623730951 |
|     3 |     4 |                  5 |
|     6 |     8 |                 10 |
+-------+-------+--------------------+
```

<p align="justify">

Bất kỳ ứng dụng sử dụng `triangle` table đều có quyền truy cập giá trị của cạnh huyền mà không cần phải tính toán chúng.

Syntax để định nghĩa một generated column:
</p>

```sql
col_name data_type [GENERATED ALWAYS] AS (expr)
  [VIRTUAL | STORED] [NOT NULL | NULL]
  [UNIQUE [KEY]] [[PRIMARY] KEY]
  [COMMENT 'string']
```

<p align="justify">

`AS (expr)` xác định biểu thức để tính toán gía trị của column.

`VITUAL` hoặc `STORED` keyword để xác định cách lưu trữ giá trị của column

- VIRTUAL: giá trị của column sẽ không được lưu trữ, nhưng sẽ được tính toán khi row được đọc hoặc ngay sau khi BEFORE trigger được kích hoạt. Một virtual column không được lưu trữ. InnoDB hỗ trợ secondary index trên virtual column.

- STORED: Giá trị của column được tính toán và được lưu trữ khi row được insert hoặc update. Một stored column cần một không gian lưu trữ và có thể được đánh index.

Mặc định sẽ là `VIRTUAL` nếu không có keyword được chỉ định.

Một table có thể chứa `VIRTUAL` và `STORED` column

Các biểu thức phải tuân thủ một số quy tăc sau. Nếu vi phạm thì sẽ có error xảy ra.

- Literal, deterministic built-in function và operator được phép sử dụng. Một function được coi là deterministic nếu cung cấp một dữ liệu trong table, với nhiều lần gọi khác nhau thì function vẫn cho cùng kết quả, độc lập với user được kết nối. VD một số function là nondeterministic, không thỏa mãn định nghĩa này: CONNECTION_ID(), CURRENT_USER(), NOW()

- Các store function và những function do người dùng định nghĩa là không được phép.

- Store procedure và các function parameter là không được phép.

- Variable (system variable, user-defined variable, và program local variable được lưu trữ) không được phép.

- Subquery không được phép

- Một generate column có thể tham chiếu tới một generate column khác, nhưng chỉ là những column được định nghĩa trước đó. Một generate column có thể tham chiếu tới bất kỳ base column (nongenerate) nào bất kể nó được định nghĩa trước hay sau.

- AUTO_INCREMENT attribute không thể sử dụng trong generate column definition

- AUTO_INCREMENT column không thể được sử dụng như một base column trong generate column definition

- MySQL 5.7.10, nếu expression gây ra truncate hoặc cung cấp input không chính xác tới một function thì CREATE TABLE statement sẽ terminate với một error và DDL operation bị reject.
</p>