# Authoritative guide to CORS (Cross-Origin Resource Sharing) for REST APIs

<p align="justify">

Các bạn đã bao giờ nghe thấy cum từ `CORS`, và đã bao giờ gặp phải lỗi về `CORS` chưa? 

`CORS` là gì vậy? Cách thức hoạt động của nó như thế nào? 
Hôm nay chúng ta cùng tìm hiểu về `CORS` nhé. 
</p>

## What is CORS?
<p align="justify">

CORS là một cơ chế bảo một cho phép một trang web từ một domain hoặc một `Origin` truy cập tới resource với một domain khác (<i>cross-domain request</i>). CORS là một sự nới lỏng của <a href="https://en.wikipedia.org/wiki/Same-origin_policy">same-origin policy</a> được implement trong các browser hiện đại. `Same-origin policy` nói rằng web browser cho phép các tập lệnh (scripts) trong một trang web có thể truy cập tới resource của một trang web khác khi hai trang web này có cùng `Origin`
</p>

### Why does same-origin exist?
<p align="justify">

Giống như rất nhiều trang web, có thể sử dụng cookie để theo dõi việc xác thực hoặc thông tin của session. Những cookie đó được liên kết với một domain xác định khi chúng được tạo ra. Trên mỗi HTTP call tới domain đó, browser sẽ attach theo cookie đã được tạo cho domain đó. 

Điều này có nghĩa rằng khi chúng ta login tới `https://examplebank.com`, một cookie được lưu trữ cho `https://examplebank.com`
</p>

### The cross-domain vulnerability
<p align="justify">

Giả sử nếu chúng ta truy cập một trang web độc hại `https://evilunicorns.com` trong khi đã login tới `https://examplebank.com`. Nếu không có `same-origin policy`, trang web hacker đó có thể thực hiện xác thực AJAX call đến `https://examplebank.com/api` tới `POST /withdraw` mặc dù trang web hacker không có quyền truy cập trực tiếp tới cookie của `bank`.

Điều này là do browser sẽ tự động thực hiện attach bất kỳ cookie nào được liên kết với `https://examplebank.com` cho bất cứ HTTP call tới domain đó, bao gồm cả AJAX call từ `https://evilunicorns.com` tới `https://examplebank.com`. Bằng việc hạn chế HTTP call chỉ tới những HTTP call có cùng origin, `same-origin policy` sẽ ngăn chặn một số tấn công chẳng hạn như <a href="https://en.wikipedia.org/wiki/Cross-site_request_forgery">Cross-Site Request Forgery (CSRF).</a>

CSRF là kỹ thuật tấn công bằng cách sử dụng quyền chứng thực của người dùng để thực hiện một số lệnh gây hại tới trang web. Kỹ thuật này còn được biết đến với tên gọi one-click, session riding, CSRF hay XSRF.
</p>

### How is Origin defined?
<p align="justify">

Ở trên chúng ta nghe rất nhiều tới `Origin`. Vậy `Origin` được định nghĩa như thế nào?
</br>
`Origin` là sự kết hợp của protocol, domain và port. Điều này có nghĩa rằng `https://api.mydomain.com` và `https://mydomain.com` thực sự là hai `origin` khác nhau và do đó nó sẽ bị ảnh hưởng bởi `same-origin policy`. Tương tự như `http://localhost:9000` và `http://localhost:8000` là hai `origin` khác nhau. Path hoặc query parameter sẽ bị bỏ qua khi xem xét về `origin`.
</p>

### How was CORS created?
<p align="justify">

Có những lý do để một website có thể tạo một cross-origin HTTP request. Có thể một single-page app tại `https://mydomain.com` thực hiện một AJAX call tới `https://api.mydomain.com` hoặc có thể `https://mydomain.com` kết hợp với font chữ của bên thứ 3. `Cross-Origin Resource Sharing (CORS)` sẽ cho phép cross-domain request để có thể truy cập được tài nguyên cần thiết ở một domain khác.
</p>

## How CORS works?
<p align="justify">

Có hai loại CORS request, `simple request` và `preflighted request`.
</p>

### Simple requests

<p align="justify">

Một `simple request` là một CORS request mà không yêu cầu một `preflight request` (preliminary check - kiểm tra sơ bộ) trước khi được bắt đầu

1. Một browser open `https://www.mydomain.com` bắt đầu một AJAX request `GET https://api.domain.com/widgets`

2. Cùng với việc add header như `Host`, browser sẽ tự động add thêm `Origin` Request Header cho cross-origin request:
</p>

```
GET /widgets/ HTTP/1.1
Host: api.mydomain.com
Origin: https://www.mydomain.com
[Rest of request...]
```

<p align="justify">

3. Server sẽ check `Origin` request header. Nếu `Origin` được cho phép, nó sẽ set `Access-Control-Allow-Origin` thành giá trị trong request header `Origin`.
</p>

```
HTTP/1.1 200 OK  
Access-Control-Allow-Origin: https://www.mydomain.com  
Content-Type: application/json
[Rest of response...]  
```
<p align="align">

4. Khi browser nhận response, browser sẽ check `Access-Control-Allow-Origin` header để kiểm tra xem nó có khớp với `origin` hiện tại của tab hay không. Nếu không khớp thì response sẽ bị block. Việc kiểm tra sẽ pass nếu `Access-Control-Allow-Origin` khớp với `origin` hoặc có chứa `*`.

Một server mà `Acess-Control-Allow-Origin: *` cho phép tất cả origin, điều này có thể gây ra rủi ro về bảo mật. Chúng ta sẽ xem xét vấn đề này ở bên dưới.
</p>

### Intention
<p align="justify">

Như bạn có thể thấy, server sẽ kiểm soát việc có cho phép request hay không tùy thuộc vào `origin` của request. Browser đảm bảo rằng `Origin` request header được set là chính xác và tin cậy.
</p>

### Preglighted requests
<p align="justify">

Một `preflighted request` là một loại CORS request khác. Một `preflighted request` là một CORS request trong đó browser được yêu cầu send một `preflight request` trước khi send request ban đầu để yêu cầu sự cho phép từ server nếu CORS request ban đầu có thể được tiến hành. `Preflight request` bản thân nó là một `OPTIONS` request tới cùng một URL.

Vì CORS request ban đầu có một `preflight request` trước nó, nên chúng ta gọi là CORS request ban đầu được `preflighted`.

Bất kỳ một CORS nào đều phải được `preflighted` nếu:

- Nếu sử dụng các method khác ngoài `GET`, `HEAD` hay `POST`. Ngoài ra, nếu `POST` được sử dụng để send một request với `Content-Type` không phải là `application/x-www-form-urlencoded`, `multipart/form-data` hay `text/plain`. VD nếu `POST` request send một `XML` playload tới một server sử dụng `application/xml` hay `text/xml` thì request là `preflighted`.

- Nó set custom header trong request (vd: request sử dụng header như `X-PINGOTHER`)
</p>

### Typical cases requiring preflighted requests

<p align="justify">

1. Một website tạo một AJAX call để POST JSON data tới một REST API có nghĩa rằng `Content-Type` header là `applicaition/json`

2. Một website tạo một AJAX call tới một API mà sử dụng một token để xác thực API trong request header chẳng hạn như `Authorization`
</p>

### Example flow

<p align="justify">

1. Một browser tab open `https://www.mydomain.com` bắt đầu một AJAX request được xác thực `POST https://api.mydomain.com/widgets` với một `JSON` payload. Browser sẽ send `OPTIONS` request đầu tiên (còn gọi là `preflighted request`) với `Requested Method` và `Requested Headers` của request chính:

```
OPTIONS /widgets/ HTTP/1.1
Host: api.mydomain.com
Origin: https://www.mydomain.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: Authorization, Content-Type
[Rest of request...]
```

2. Server phản hồi lại chỉ định các HTTP method và header được phép. Nếu CORS request ban đầu dự định gửi một header hoặc HTTP method không nằm trong danh sách hợp lệ thì browser sẽ fail mà không cần thực hiện CORS request.

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://www.mydomain.com
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: Authorization, Content-Type
Content-Type: application/json
[Rest of response...]  
```

3. Vì header và method hợp lệ nên browser sẽ send CORS request ban đầu.

```
POST /widgets/ HTTP/1.1
Host: api.mydomain.com
Authorization: 1234567
Content-Type: application/json
Origin: https://www.mydomain.com
[Rest of request...]
```

4. Response có `Access-Control-Allow-Origin` header map với `origin` của tab nên việc check origin thành công.

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://www.mydomain.com
Content-Type: application/json
[Rest of response...] 
```
</p>

## Advanced Configuration

<p align="justify">

Chúng ta đã thấy một số header trong các ví dụ trước mà chúng ta sử dụng cho CORS chẳng hạn như `Access-Control-Allow-Origin` và `Access-Control-Allow-Methods`, nhưng có rất nhiều header để việc kiểm soát tốt hơn. Dưới đây là danh sách đầy đủ các header kiểm soát CORS.
</p>

### Request Headers

| Header Name | Example Value | Description | Used in preflight requests | Used in CORS requests |
|:-:|:-:|:-|:-:|:-:|
| Origin |  https://www.mydomain.com | Kết hợp protocol, domain, và port của brower tab được open | YES | YES |
| Access-Control-Request-Method | POST | Đối với `preflight request`, xác định method mà CORS request ban đầu sẽ sử dụng | YES | NO |
| Access-Control-Request-Headers | Authorization, X-PING | Đối với `preflight request`, danh sách được phân tách bởi dấu `,` xác định header nào mà CORS request ban đầu sẽ send  | YES | NO |

### Response Headers

| Header Name | Example Value | Description | Used in preflight requests | Used in CORS requests |
|:-:|:-:|:-|:-:|:-:|
| Access-Control-Allow-Origin | https://www.mydomain.com | Những `origin` được cho phép đối với request, được xác định bởi server. Nếu nó không khớp với `Origin` header và không phải là `*`, browser sẽ reject request đó. Nếu domain được xác định, protocol component được yêu cầu và có thể chỉ là single domain | YES | YES |
| Access-Control-Allow-Credentials | true | CORS request thông thường không bao gồm cookie để ngăn bị tấn công CSRF. Khi giá trị này được set `true`, request có thể được tạo với hay sẽ bao gồm thông tin xác thực (credentials) chẳng hạn như cookie. Nếu không có header này thì mặc định là `false`. | YES | YES |
| Access-Control-Expose-Headers | Date, X-Device-Id | Danh sách được ngăn cách bởi dấu `,` những header được phép hiển thị với browser ngoài những default header | NO | YES |
| Access-Control-Max-Age | 600 | Gía trị này được tính bằng giây, khoảng thời gian mà result của `preflight request` được cache (ví dụ dữ liệu của `Access-Control-Allow-Headers` và `Access-Control-Allow-Methods` headers). Firefox tối đa là 24 giờ và Chrome tối đa là 10 phút. Nếu set giá trị lớn hơn thì cũng không có tác dụng | YES | NO |
| Access-Control-Allow-Methods | GET, POST, PUT, DELETE | Có thể là `*` để cho phép tất cả các method. Dấu `,` được sử dụng để ngăn cách danh sách các method được phép sử dụng cho CORS request | YES | NO |
| Access-Control-Allow-Headers | Authorization, X-PING | Có thể là `*` để cho phép bất kỳ header. Dấu `,` được sử dụng để ngăn cách danh sách những header có thể được sử dụng cho CORS request | YES | NO |

<p align="justify">

Không phải tất cả browser hiện nay đều cho phép sử dụng Wilcard (dấu `*`) trong `Access-Control-Allowed-Headers` , `Access-Control-Allow-Methods`, and `Access-Control-Expose-Headers`.

Sử dụng danh sách các header hoặc method sẽ tốt hơn là sử dụng `*`.
</p>

## Common Pitfalls
### 1. Using * operator for Acess-Control-Allow-Origin
<p align="justify">

CORS là một sự nới lỏng của `same-origin` policy trong khi vẫn đảm bảo được sự an toàn. Việc sử dụng `*` sẽ phá vỡ hầu hết các quy tắc bảo mật của CORS. Sẽ có những trường hợp sử dụng `*` là ổn, chẳng hạn như một open API được tích hợp vào nhiều website của bên thứ 3. 

Chúng ta có thể cải thiện bảo mật bằng cách có API trên một domain khác. Ví dụ, open API `https://api.mydomain.com` có thể phản hồi với `Access-Control-Allow-Origin: *`, nhưng API của website chính `https:\\www.mydomain.com/api` vẫn phản hồi với `Access-Control-Allow-Origin: https://www.mydomain.com`
</p>

### 2. Returning multiple domains for Access-Controll-Allow-Origin.
<p align="justify">

Không được phép `Access-Control-Allow-Origin: https://mydomain.com, https://www.mydomain.com`. Server chỉ có thể phản hồi với một domain duy nhất hoặc `*`
</p>

### 3. Using wildcard selection like *.mydomain.com
<p align="justify">

Điều này là không được phép trong CORS. `*` chỉ có thể sử dụng để ngụ ý rằng tất cả các domain đều được phép.
</p>

### 4. Not including protocol or non-standart ports.
<p align="justify">

`Access-Control-Allow-Origin: mydomain.com` là không hợp lệ vì nó không bao gồm protocol, bạn sẽ gặp vấn đề tương tự với `Access-Control-Allow-Origin: http://localhost` trừ khi server thực sự đang chạy trên standard HTTP port `:80`
</p>

### 5. Not including Origin in the Vary response header
<p align="justify">

Hầu hết CORS framework tự động thực hiện điều này, chúng ta phải xác định cho client rằng các response của server sẽ khác nhau dựa trên `origin` của request.
</p>

### 6. Not specifiyng the Access-Control-Expose-Headers
<p align="justify">

Nếu không bao gồm những header bắt buộc trong CORS request thì request vẫn sẽ pass, nhưng header của response sẽ không có trong danh sách và sẽ bị ẩn khỏi browser tab. Những header mặc định của response sẽ luôn luôn được hiển thị đối với CORS request là:

- Cache-Control
- Content-Language
- Content-Type
- Expries
- Last-Modified
- Pragma
</p>

### 7. Using wildcard when Access-Control-Allow-Credentials is set to <i>true</i>
<p align="justify">

Nếu reponse có `Access-Control-Allow-Credentials: true` thì wildcard operator (`*`) không thể được sử dụng cho bất kỳ header nào của response, ví dụ như `Access-Control-Allow-Origin`
</p>

<p align="justify">

Trên đây là những chia sẻ của mình về CORS. Hẹn gặp lại các bạn ở những bài viết tiếp theo nhé

<br/>
<br/>
<br/>
Reference: 

- https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/
- https://www.html5rocks.com/en/tutorials/cors/
- https://codeaholicguy.com/2018/05/07/cors-la-gi/
- https://topdev.vn/blog/cors-la-gi/
</p>