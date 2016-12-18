---
layout: post
title: 'SQL and Relational Theory: Kiểu dữ liệu và Miền giá trị'
---

Kiểu dữ liệu là một khái niệm cơ bản, nên những gì được thảo luận trong chương này cũng giúp loại bỏ một số những nhầm lẫn nhất định. Tuy nhiên, lý thuyết kiểu dữ liệu không phải là một chủ đề quá liên quan đến tính quan hệ, và những vấn đề liên quan đến kiểu dữ liệu dường như không liên quan đến cuộc sống thường nhật của SQL.

## Kiểu dữ liệu và quan hệ

Kiểu dữ liệu - Data type (viết tắt là type) là một khái niệm cơ bản của khoa học máy tính. Lý thuyết quan hệ cần có một lý thuyết kiểu dữ liệu hỗ trợ, bởi vì các quan hệ được định nghĩa trên kiểu &mdash; tức là, tất cả thuộc tính đều phải được định nghĩa có một kiểu dữ liệu nào đó, tất nhiên cũng tương tự với relvar. Ví dụ, giả sử rằng trong toàn bộ cuốn sách thuộc tính `STATUS` của relvar các nhà cung cấp `S` được định nghĩa có kiểu dữ liệu INTEGER. Với giả định đó, tất cả quan hệ mà có thể là giá trị của `S` phải có thuộc tính `STATUS` có kiểu dữ liệu INTEGER &mdash; nghĩa là đến lượt tất cả tuple trong một quan hệ như thế cũng phải có thuộc tính `STATUS` có kiểu dữ liệu INTEGER, và khi xét một tuple nhất định thì nó cũng phải có giá trị `STATUS` là một số INTEGER.

Một thuộc tính quan hệ (tức là, một thuộc tính của quan hệ hay relvar) có thể có bất kỳ kiểu dữ liệu nào &mdash; trừ một số ngoại lệ quan trọng, sẽ thảo luận sau &mdash; gồm cả những kiểu đơn giản và những kiểu dữ liệu có độ phức tạp tùy ý. Cụ thể, chúng có thể là kiểu của hệ thống hay của người dùng tự định nghĩa. Tuy nhiên, ta sẽ không nhắc nhiều tới kiểu dữ liệu người dùng tự định nghĩa, vì: Nói chung về kiểu dữ liệu do người dùng định nghĩa &mdash; từ góc độ của người sử dụng (hiếm khi dùng đến), tức là, trái ngược với người định nghĩa &mdash; thì chức năng của chúng cũng không khác so với kiểu dữ liệu của hệ thống.

Nên từ lúc này, ta sử dụng từ *kiểu dữ liệu* chỉ có nghĩa là kiểu dữ liệu do hệ thống định nghĩa, trừ trường hợp ngữ cảnh bắt buộc. Thực ra, mô hình quan hệ chỉ quy định một kiểu dữ liệu, BOOLEAN (kiểu cơ bản nhất). BOOLEAN chứa đúng hai giá trị: hai giá trị chân lý, cụ thể, ký hiệu bởi hai hằng số đúng - TRUE và sai - FALSE. Tất nhiên, các hệ thống trên thực tế hỗ trợ nhiều hơn, giả sử INTEGER (số nguyên), RATIONAL (số hữu tỉ), và CHAR (chuỗi ký tự có độ dài bất kỳ).

<div class="definition">
  <strong>Ngoài lề</strong>: Một số hữu tỉ là một số có thể biểu diễn được bằng tỉ lệ giữa hai số nguyên (ví dụ, 3/8, -4/5); một số vô tỉ là một số không thể biểu diễn được như thế (ví dụ, &Pi;, &radic;2). Ngày nay, rất nhiều ngôn ngữ lập trình hỗ trợ một kiểu dữ liệu số gọi là REAL. Một số thực có thể là một số hữu tỉ hay vô tỉ; tuy nhiên, việc tính toán của máy tính là có hạn, nên những số thực mà chúng biểu diễn thực ra là số hữu tỉ. Do đó, <strong>Tutorial D</strong> sử dụng từ khóa RATIONAL.
</div>

Đúng ra về mặt lịch sử, khi Codd lần đầu tiên định nghĩa mô hình quan hệ, ông ấy nói rằng quan hệ được định nghĩa trên *miền giá trị - domain*, không phải kiểu dữ liệu. Nhưng miền giá trị và kiểu dữ liệu thực ra là một. Đây là quan điểm cá nhân của tác giả, và dưới đây là hai chủ đề chứng minh điều đó:

 * *Phép so sánh bằng và "domain check override":* Chứng minh miền giá trị đúng là kiểu dữ liệu.
 * *Data value atomicity and first normal form - Tính nguyên tử của giá trị và dạng chuẩn thứ nhất:* Chứng minh kiểu dữ liệu có thể độ phức tạp tùy ý.

Quá trình chứng mình này sẽ bắt đầu với mô hình quan hệ gốc do Codd định nghĩa; do đó ta tạm thời sử dụng từ nguyên gốc *miền* thay cho *kiểu*.

## Phép so sánh bằng

Trong phần trước đã nói rằng hãy bỏ qua kiểu dữ liệu người dùng tự định nghĩa. Nhưng để cho dễ hiểu, trong phần này ta giả sử thuộc tính mã nhà cung cấp `SNO` trong relvar `S` và `SP` có kiểu dữ liệu người dùng tự định nghĩa &mdash; xin lỗi, miền giá trị, &mdash; và để cho đơn giản, cũng gọi là `SNO`. 

Như đã biết, hai giá trị bằng nhau trong mô hình quan hệ khi và chỉ khi cùng nằm trong cùng một miền giá trị. Ví dụ phép so sánh bằng như sau (có thể nằm trong câu truy vấn WHERE) hoàn toàn hợp lệ:

```
SP.SNO = S.SNO  /* OK */
```

Ngược lại, phép so sánh bằng này không hợp lệ:

```
SP.PNO = S.SNO  /* not OK */
```

*Lý do là*: Vì mã bộ phận và mã nhà cung cấp là hai thứ khác nhau &mdash; chúng nằm trên hai miền khác nhau. Do đó, nói chung DBMS nên từ chối tất cả thao tác quan hệ (join, union...) có liên quan đến, trực tiếp hay gián tiếp, một phép so sánh bằng giữa hai giá trị thuộc hai miền khác nhau. Ví dụ, giả sử người dùng muốn tìm các nhà cung cấp (chẳng hạn `S5`) hiện tại không cung cấp bộ phận nào. Anh ta tính toán câu truy vấn sau:

```
SELECT S.SNO , S.SNAME , S.STATUS , S.CITY
FROM S
WHERE NOT EXISTS
    ( SELECT *
      FROM SP
      WHERE SP.PNO = S.SNO )  /* not OK */
```

(Không có dấu chấm phảy kết thúc, vì đây là một biểu thức - expression, không phải lệnh - statement.)

Như comment đã chỉ ra, tính toán này không hợp lệ. Do trong dòng cuối cùng, anh ấy ý muốn nói `WHERE SP.SNO = S.SNO`, nhưng bị nhầm lẫn, lại viết thành <code>SP.<strong>PNO</strong> = S.SNO</code>. Trong trường hợp này, DBMS có thể xử lý một cách thân thiện như sau: ngăn người dùng tiếp tục nhập chữ và in nổi bật lỗi (bằng màu đỏ).

Ngày nay, tùy thuộc vào cách cài đặt DBMS mà một câu truy vấn như trên sẽ bị từ chối hay vẫn tiếp tục được thực hiện nhưng cho ra kết quả sai... Cũng có thể là một kết quả đúng cho một câu hỏi sai.

Tóm lại, nhìn chung, DBMS nên từ chối một so sánh không hợp lệ như `SP.PNO = S.SNO`. Tuy nhiên, Codd cảm thấy trong trường hợp này nên có một cách cho phép người dùng yêu cầu DBMS tiếp tục thực hiện phép so sánh, vì thực tế có đôi lúc người dùng biết nhiều hơn DBMS. Thẳng thắn mà nói điều này là vô lý &mdash; nhưng hãy thử. Giả sử ta cần thiết kế một cơ sở dữ liệu, ví dụ, khách hàng - customers và nhà cung cấp - suppliers; ta quyết định có một miền giá trị mã khách hàng và một miền giá trị mã nhà cung cấp; ta xây dựng và bắt đầu sử dụng cơ sở dữ liệu, và mọi thứ chạy ổn trong một hay hai năm. Rồi một ngày, có một người dùng muốn truy vấn một câu mà ta chưa từng nghe &mdash; đó là: "có khách hàng nào của tôi cũng là nhà cung cấp của tôi không?" Điều này hoàn toàn hợp lý, như thế cũng có nghĩa là ta cần so sánh bằng giữa mã khách hàng và mã nhà cung cấp (so sánh bằng trên hai miền giá trị khác nhau). Và hệ thống không có quyền ngăn ta làm điều đó (vì nó hợp lý).

Căn cứ vào luận điểm như thế, Codd đưa ra cái ông ấy gọi là phiên bản "domain check override" (DCO) cho một số toán tử quan hệ. Ví dụ, một phiên bản DCO của join sẽ thực hiện join ngay cả khi các thuộc tính nằm trên các miền khác nhau. Ta có thể tưởng tượng rằng có một mệnh đề mới, `IGNORE DOMAIN CHECKS` biểu diễn cho phiên bản DCO này:

```
SELECT ...
FROM   ...
WHERE  CNO = SNO
IGNORE DOMAIN CHECKS
```

Mệnh đề mới này nên được phân quyền &mdash; với đa số người dùng không nên cho phép sử dụng (có thể chỉ có DBA[^dba] mới được phép). 

Trước khi phân tích chi tiết khái niệm DCO, hãy xét một ví dụ đơn giản hơn. Giả sử có hai câu truy vấn SQL cho cơ sở dữ liệu nhà-cung-cấp-và-bộ-phận:

```
SELECT ...                    |  SELECT ...
FROM P , SP                   |  FROM P , SP
WHERE P.WEIGHT = SP.QTY       |  WHERE P.WEIGHT - SP.QTY = 0
```

Giả sử, rất hợp lý, rằng trọng lượng - weight và số lượng quantity được định nghĩa trên hai miền giá trị khác nhau, nên câu truy vấn bên trái rõ ràng không hợp lệ. Nhưng còn câu bên phải? Theo Codd, nó vẫn hợp lệ. Trong sách ông ta viết *The Relational Model for Database Management Version 2* (Addison-Wesley, 1990), trang 47, Codd giải thích rằng trong một trường hợp như bên phải, "DBMS kiểm tra và nhận thấy hai số hạng có cùng kiểu dữ liệu cơ bản"; "kiểu dữ liệu cơ bản" ở đây là các số, nên chúng cùng thuộc một miền giá trị và phép so sánh hợp lệ.

Việc kiểm tra miền giá trị như vậy là không thể chấp nhận được. Rõ ràng, biểu thức `P.WEIGHT = SP.QTY` và `P.WEIGHT - SP.QTY = 0` thực sự là cùng một thứ. Vì thế chắc chắn chúng phải cùng hợp lệ hoặc cùng không hợp lệ. Như vậy, ngay từ đầu, đã tồn tại một thứ rất lạ trong việc kiểm tra miền giá trị theo phong cách của Codd, còn chưa nói tới "kế thừa kiểm tra miền giá trị - domain check override". (Thực ra, về bản chất, việc kiểm tra miền giá trị theo cách của Codd chỉ xảy ra trong trường hợp rất đặc biệt, khi cả hai số hạng đều là tham chiếu tới thuộc tính. So sánh `P.WEIGHT = SP.QTY` rơi vào hạng mục đặc biệt này, còn `P.WEIGHT - SP.QTY = 0` thì không.)

Cùng xét một ví dụ đơn giản hơn nữa. Giả sử có các phép so sánh (có thể xuất hiện trong mệnh đề WHERE):

```
S.SNO = 'X4'    P.PNO = 'X4'    S.SNO = P.PNO
```

Hai so sánh đầu có vẻ hợp lệ (còn có thể cho kết quả TRUE), nhưng so sánh thứ ba thì không. Nếu như thế, thì ở đây cũng có một điều rất lạ đang diễn ra; rõ ràng, ta có ba giá trị `a`, `b`, và `c`, và có `a = c` là đúng và `b = c` là đúng, nhưng với `a = b` &mdash; ta thậm chí còn không thể thực hiện so sánh!

Trở lại giả sử `S.SNO` và `P.PNO` được định nghĩa trên hai miền `SNO` và `PNO`, và lập luận miền giá trị thực chất là kiểu dữ liệu; và giả sử hai miền `SNO` và `PNO` là hai kiểu dữ liệu người dùng tự định nghĩa. Có khả năng (thậm chí đúng) rằng hai kiểu dữ liệu này đều được biểu diễn vật lý theo kiểu CHAR của hệ thống; hãy giả sử như thế, để cho rõ ràng. Tuy nhiên, những biểu diễn này là thành phần của thực thi, không phải mô hình &mdash; chúng không liên quan đến người dùng. Vì thế, những toán tử áp dụng cho `SNO` và `PNO` là những toán tử được định nghĩa liên kết với hai kiểu dữ liệu này, không phải là các toán tử liên kết với CHAR. Ví dụ, ta có thể nối hai chuỗi ký tự, nhưng không thể nối hai mã nhà cung cấp (ta chỉ có thể thực hiện việc này sau khi định nghĩa toán tử nối cho kiểu dữ liệu `SNO`).

Như vậy, khi định nghĩa một kiểu dữ liệu, ta cũng phải định nghĩa các toán tử liên kết với giá trị và biến có kiểu dữ liệu đó. Và một toán tử ta *phải* định nghĩa đó là toán tử chọn - selector operator, cái cho phép ta chọn ra, hay chỉ ra một giá trị có kiểu dữ liệu đó. Trong trường hợp kiểu `SNO`, selector (thực tế hay được đặt tên cũng là `SNO`) cho phép ta chọn ra một giá trị `SNO` từ biểu diễn CHAR của nó. Ví dụ:

```
SNO('S1')
```

Biểu thức này là một lời gọi toán tử chọn `SNO`, và nó trả về một mã nhà cung cấp: là nhà cung cấp được biểu diễn bởi chuỗi ký tự `'S1'`. Tương tự như thế, biểu thức

```
PNO('P1')
```

là một lời gọi toán tử chọn `PNO`, và nó trả về một mã bộ phận: là bộ phận được biểu diễn bởi chuỗi ký tự `'P1'`. Nói cách khác, hai toán tử chọn `SNO` và `PNO` nhận một giá trị CHAR rồi chuyển đổi thành một giá trị `SNO` và `PNO`.

Trờ lại so sánh `S.SNO = 'X4'`. Hai số hạng ở đây có kiểu dữ liệu khác nhau (`SNO` và CHAR). Vì chúng có kiểu dữ liệu khác nhau, nên chắc chắn không bằng nhau (ngay từ lúc bắt đầu phần này đã nói rằng hai giá trị có thể được so sánh bằng "chỉ khi chúng nằm trong cùng miền giá trị"). Nhưng hệ thống đã đủ thông minh để tự định nghĩa một toán tử &mdash; toán tử chọn `SNO` &mdash; thực hiện việc chuyển đổi CHAR thành `SNO` (do số hạng bên phải chỉ là "kiểu dữ liệu cơ bản"). Vì thế nó có thể gọi toán tử này ra, gọi ngầm, để chuyển số hạng kiểu CHAR thành `SNO`, tức là hệ thống thay thế so sánh cũ bằng:

```
S.SNO = SNO('X4')
```

Lúc này, phép so sánh mã nhà cung cấp (giữa `SNO` và CHAR) là hợp lệ.

Tương tự, hệ thống cũng thay thế so sánh `P.PNO = 'X4'` bằng:

```
P.PNO = PNO('X4')
```

Nhưng với `S.SNO = P.PNO`, hệ thống không thể tự định nghĩa một toán tử để chuyển đổi mã mà cung cấp thành mã bộ phận, nên so sánh này gây ra *type error:* Hai số hạng có kiểu dữ liệu khác nhau, và không có cách khiến chúng có cùng kiểu.

*Chú ý:* Ngầm chuyển đổi kiểu dữ liệu như mô tả trong các ví dụ trước thường được gọi là *ép kiểu - coercion.* Trong ví dụ thứ nhất, ta có thể nói `'X4'` được ép kiểu thành `SNO`; trong ví dụ thứ hai là `PNO`.

Tiếp theo: Một toán tử khác ta phải định nghĩa sau khi định nghĩa một kiểu dữ liệu như `SNO` hay `PNO` là, toán tử `THE_` &mdash; trong trường hợp này &mdash; chuyển đổi một giá trị `SNO` hay `PNO` thành chuỗi ký tự dùng để biểu diễn nó. Giả sử đặt tên cho hai toán tử `THE_` cho kiểu `SNO` và `PNO` lần lượt là `THE_SC` và `THE_PC`. Sau đó, khi muốn so sánh bằng `S.SNO` và `P.PNO`, nghĩa là muốn kiểm tra hai biểu diễn CHAR tương ứng của chúng có bằng nhau hay không, ta có thể thực hiện như sau:

```
THE_SC ( S.SNO ) = THE_PC ( P.PNO )
```

Tức là: Chuyển mã nhà cung cấp về một chuỗi ký tự, chuyển mã bộ phận về một chuỗi ký tự, và so sánh hai chuỗi này.

Chắc bạn đã nhận ra, cơ chế được phác họa ở đây, bao gồm toán tử selector và toán tử `THE_`, đưa ra cả (a) cách kiểm tra miền giá trị - domain check và (b) domain check override, khi cần. Hơn nữa, cách này rõ ràng, hoàn toàn trực giao, không bột phát. Ngược lại, domain check override DCO của Codd không hoàn thành tốt nhiệm vụ; thực tế, nó hoàn toàn vô lý, vì làm xáo trộn kiểu dữ liệu và biểu diễn (kiểu dữ liệu là khái niệm của mô hình, biểu diễn là khái niệm của thực thi).

Những gì đang nói gọi là *strong typing* trong các ngôn ngữ lập trình, về cơ bản, có nghĩa là (a) tất cả mọi thứ &mdash; cụ thể là tất cả giá trị và tất cả biến &mdash; đều có một kiểu dữ liệu, và (b) khi muốn thực hiện một thao tác trên chúng, hệ thống kiểm tra các toán hạng có đúng kiểu dữ liệu hay không (hoặc, có khả năng, ép toán hạng về đúng kiểu). Và cơ chế này hoạt động với tất cả thao tác, không chỉ so sánh bằng. Ví dụ, xét hai biểu thức sau:

```
P.WEIGHT * SP.QTY
P.WEIGHT + SP.QTY
```

Biểu thức thứ nhất có thể hợp lệ (nó tạo ra một giá trị trọng lượng weight khác: là trọng lượng tổng của mỗi mua bán shipment). Ngược lại, biểu thức thứ hai không hợp lệ (cộng trọng lượng và số lượng sẽ tạo ra cái gì?).

Nhìn lại vai trò nền tảng &mdash; không chỉ trong lý thuyết kiểu &mdash; của toán tử so sánh bằng ("="). Không phải ngẫu nhiên mà các thảo luận trên tập trung vào vấn đề so sánh bằng giữa hai giá trị. Tính bằng nhau thực sự là trung tâm, và mô hình cần nó hỗ trợ cho tất cả các kiểu dữ liệu. Thật vậy, vì một kiểu dữ liệu cơ bản là một tập hợp các giá trị, không có toán tử "=", chúng ta thậm chí không thể diễn đạt những giá trị nào cấu thành nên kiểu dữ liệu!

Ngoài ra, mô hình quan hệ cũng định rõ nghĩa của toán tử "=" như sau: Nếu *v1* và *v2* là hai giá trị có cùng kiểu dữ liệu, thì *v1 = v2* là TRUE khi và chỉ khi *v1* và *v2* có cùng giá trị, nếu không là FALSE. Ngược lại, nếu *v1* và *v2* có kiểu dữ liệu khác nhau, thì *v1 = v2* không có nghĩa &mdash; nó không phải một phép so sánh hợp lệ &mdash; trừ khi *v1* có thể được ép thành kiểu dữ liệu của *v2* hay đại loại như vậy, nhưng kể cả trong trường hợp đó, ta cũng không thực sự đang nói về so sánh giữa *v1* và *v2*.

## Tính nguyên tử của giá trị - DATA VALUE ATOMICITY

Phần này sẽ xét vấn đề tính nguyên tử của giá trị và khái niệm liên quan first normal form (viết tắt là 1NF). Chương 1 nói rằng 1NF nghĩa là tất cả tuple chỉ chứa một giá trị đơn (có kiểu dữ liệu thích hợp) tương ứng với mỗi thuộc tính &mdash; và người ta thường nói kèm theo rằng những "giá trị đơn" này có tính chất "nguyên tử". Vậy thì: Tính nguyên tử của dữ liệu là gì?

Trong trang 6 của cuốn sách vừa kể (*The Relational Model for Database Management Version 2*), Codd định nghĩa tính nguyên tử của dữ liệu nghĩa là dữ liệu "không thể phân chia được thành các phần nhỏ hơn bởi DBMS." Định nghĩa này không rõ ràng. Ví dụ chuỗi ký tự, nó có tính nguyên tử hay không? Tất cả sản phẩm cơ sở dữ liệu hiện nay đều đưa ra rất nhiều toán tử &mdash; LIKE, SUBSTR (substring), vân vân &mdash; cùng dựa vào cơ sở rằng chuỗi ký tự có thể "phân chia được thành các phần nhỏ hơn."

Dưới đây là một số ví dụ khác:

 * Chuỗi bit.
 * Số hữu tỉ (có thể tách thành phần nguyên và phần thập phân).
 * Ngày tháng và thời gian (có thể tách thành năm / tháng / ngày và giờ / phút / giây).

Vân vân.

Trước khi đưa ra kết luận, cùng xét một ví dụ nữa. Xem hình 2.1 bên dưới. Quan hệ `R1` là bản rút gọn của quan hệ shipments; nó chỉ ra nhà cung cấp nào cung cấp bộ phận nào, và sử dụng một tuple cho mỗi kết hợp `{SNO,PNO}`. Tiếp theo, cùng thống nhất, để cho đơn giản, mã nhà cung cấp và mã bộ phận là nguyên tử; tức là, ít nhất là quan hệ `R1` có dạng 1NF.

<figure>
  <img src="{{ site.baseurl }}/images/post7_r1_r2_r3.png" alt="r1 r2 r3 relations" />
  <figcaption>
    H2.1: Relations R1, R2, and R3
  </figcaption>
</figure>

Giả sử thay `R1` bằng `R2`, `R2` vẽ ra nhà cung cấp mà cung cấp *nhóm* các bộ phận (thuộc tính `PNO` trong `R2` được một số tác giả gọi là *đa trị - multivalued*, và giá trị của nó là nhóm các sản phẩm). Thì đa số chắc chắn sẽ nói rằng `R2` không ở dạng 1NF; thực tế, nó trông giống trường hợp "nhóm lặp - repeating group", và nhóm lặp là một thứ mà tất cả mọi người đều đồng ý là cần phải triệt tiêu khỏi 1NF (bời vì một nhóm hiển nhiên là không nguyên tử &mdash; đúng không?).

Để cho đơn giản, hãy cùng thống nhất `R2` không ở dạng 1NF. Nhưng giả sử thay `R2` bằng `R3`. Thì *R3 đúng ở dạng 1NF!* Do:

 * Thứ nhất, để ý thuộc tính `PNO` đã được đổi tên thành `PNO_SET`, và ta đóng nhóm các bộ phận, là giá trị của `PNO_SET` vào hai ngoặc nhọn, để nhấn mạnh rằng một nhóm như thế thực sự là một giá trị đơn: một giá trị tập hợp &mdash; một tập hợp ở một mức trừu tượng nhất định, vẫn là một giá trị đơn.
 * Thứ hai, một tập hợp như `{P2,P4,P5}` *có tính nguyên tử giống như một chuỗi ký tự, không hơn không kém.*

Rút cuộc, điều muốn nói ở đây là khái niệm nguyên tử *tuyệt đối không có ý nghĩa gì cả*; nó chỉ phụ thuộc vào cách chúng ta muốn thao tác với dữ liệu. Đôi khi ta muốn xử lý cả một tập hợp mã bộ phận như một giá trị đơn lẻ; đôi khi ta muốn xử lý độc lập từng phần tử trong tập hợp đó &mdash; nhưng cũng có lúc ta muốn dữ liệu có mức độ chi tiết thấp hơn, hoặc mức độ trừu tượng thấp hơn.

Quay lại với quan hệ `R3`. Hình 2.1 vẽ ra giá trị `PNO_SET` là tập hợp tổng quát. Nhưng thực tế sẽ hữu ích hơn nếu nó, cụ thể hơn, là quan hệ (xem hình 2.2, tên thuộc tính được đổi thành `PNO_REL`). Tại sao lại hữu ích hơn? Bởi vì quan hệ, không phải tập hợp tổng quát, là cốt lõi của mô hình quan hệ.[^relation] Từ đó, toàn bộ sức mạnh của đại số quan hệ ngay lập tức có thể áp dụng được &mdash; như restrict, project, join... Ngược lại, nếu vẫn muốn sử dụng tập hợp tổng quát, ta cần phải tạo ra các toán tử mới (set union, set intersection...) để thao tác với tập hợp.

<figure>
  <img src="{{ site.baseurl }}/images/post7_r4.png" alt="r4 relation" />
  <figcaption>
    H2.2: Relation R4 (a revised version of R3)
  </figcaption>
</figure>

*Thuật ngữ*: Thuộc tính `PNO_REL` trong hình 2.2 là một *thuộc tính giá trị quan hệ - relation-valued attribute* (RVA). RVA sẽ được nói thêm ở chương 7; nhưng ngay lúc này, hãy chú ý rằng SQL không hỗ trợ RVA. (Chính xác hơn, nó không hỗ trợ cái tương đương với RVA, *table-valued column*. Nhưng kỳ lạ, nó lại hỗ trợ cột có giá trị là mảng, và cột có giá trị là hàng, và thậm chí là cột có giá trị là "multiset của các hàng" &mdash; một *multiset*, còn gọi là *bag*, giống như tập hợp trừ việc nó cho phép các giá trị trùng nhau. Như thế, cột có giá trị là multiset của các hàng cũng có nét giống với "table-valued column"; nhưng nó không phải là table-valued column, vì giá trị của nó không thể thao tác được với các toán tử bảng của SQL.)

Tổng kết lại, quan hệ chứa RVA trông khá giống với "quan hệ chứa nhóm lặp", và ta có lẽ luôn luôn nghe thấy rằng nhóm lặp không có chỗ trong thế giới quan hệ. Nhưng qua các ví dụ, đã chứng minh rằng thuộc tính (cũng tức cả miền giá trị) có thể có giá trị mảng - array; hay bag (multiset); hay danh sách - list; hay hình ảnh; hay bản ghi âm thanh, video; hay tia X; hay dấu vân tay; hay XML document; hay bất kỳ những loại giá trị nào khác, "nguyên tử" hoặc "không nguyên tử". Thuộc tính, và miền giá trị, có thể chứa *bất kỳ thứ gì* (tức là, *bất kỳ giá trị gì*).

Thật tình cờ, từ một vài năm trước, chúng ta nghe thấy rất nhiều về các hệ thống "object-relational". Sau cùng, một hệ thống object-relational từ góc nhìn của người sử dụng chính là các giá trị thuộc tính có độ phức tạp tùy ý. Hay nói dễ hiểu hơn: Một hệ thống object-relational là một hệ thống relational có hỗ trợ kiểu dữ liệu (đặc biệt là kiểu dữ liệu đối tượng do người dùng định nghĩa) &mdash; do vậy nó chính là hệ thống quan hệ, không hơn không kém.

## Lưu ý

[^dba]: DBA = database administrator.
[^relation]: Sự khác biệt giữa quan hệ và tập hợp tổng quát là tập hợp có thể chứa bất kỳ thứ gì, còn quan hệ chỉ chứa tuple. Nhưng nó vẫn tương đồng với một tập hợp là có thể coi như một giá trị đơn.