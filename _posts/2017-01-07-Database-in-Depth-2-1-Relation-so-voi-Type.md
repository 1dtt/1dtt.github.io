---
layout: post
title: 'Database in Depth 2.1: Relation so với Type'
---

Mô hình quan hệ chắc chắn cần một hệ thống mà có hỗ trợ kiểu dữ liệu. Bởi vì quan hệ (và relvar) được định nghĩa dựa trên kiểu dữ liệu; tức là, *mọi thuộc tính, của mọi quan hệ (và mọi relvar) đều được định nghĩa có kiểu dữ liệu nào đó*. Ví dụ, thuộc tính `STATUS` của suppliers relvar `S` có thể có kiểu dữ liệu INTEGER. Nếu như thế, thì tất cả quan hệ `s`, là giá trị của relvar `S`, cũng phải có thuộc tính `STATUS` có kiểu dữ liệu là INTEGER&mdash;đến lượt tất cả tuple trong quan hệ `s` cũng phải có giá trị `STATUS` là một số integer.

Một thuộc tính quan hệ (tức là, một thuộc tính của quan hệ hay relvar) có thể có *bất kỳ kiểu dữ liệu nào*&mdash;trừ một số ngoại lệ quan trọng mà tôi sẽ thảo luận sau&mdash;gồm cả những kiểu cơ bản và cả những kiểu dữ liệu có độ phức tạp tùy ý. Cụ thể, những kiểu dữ liệu này có thể do hệ thống định nghĩa (tức là, có sẵn) hay do người dùng định nghĩa. Cho ví dụ tiếp theo, tôi giả sử các thuộc tính có kiểu dữ liệu như sau như sau:

![types]({{ site.baseurl }}/images/2017_01_07_types.png)

Tôi cũng giả sử rằng kiểu INTEGER (số nguyên) và CHAR (chuỗi ký tự có độ dài tùy ý) do hệ thống định nghĩa, các kiểu còn lại do người dùng định nghĩa.

Đúng ra về mặt lịch sử, khi Codd lần đầu tiên định nghĩa mô hình quan hệ, ông ấy nói rằng quan hệ được định nghĩa dựa trên *miền giá trị* (*domain*), không phải kiểu dữ liệu. Tuy nhiên, thực tế miền giá trị và kiểu dữ liệu *đúng là một*. Bây giờ, có thể bạn cho rằng tuyên bố này chỉ là quan điểm cá nhân của tôi, nếu bạn muốn, nhưng hãy để tôi trình bày các lý lẽ của mình ở hai chủ đề tiếp theo đây. Để chứng minh. tôi sẽ đi từ mô hình quan hệ do Codd định nghĩa; vì vậy tôi sẽ tạm thời sử dụng thuật ngữ miền, không phải kiểu, cho đến khi tôi thông báo lại. Hai chủ đề tôi muốn thảo luận đó là:

 * *Domain-constrained comparisons and "domain check override"*: Tôi hi vọng phần này sẽ thuyết phục bạn rằng miền thực sự là kiểu.
 * *Data value atomicity and first normal form*: Chứng minh kiểu dữ liệu có thể độ phức tạp tùy ý.

## So sánh có kiểm tra miền giá trị (Domain-Constrained Comparisons)

Như tất cả mọi người đều biết (hoặc nên biết), trong mô hình quan hệ, hai giá trị có thể so sánh được với nhau khi và chỉ khi chúng đến từ cùng một miền giá trị. Phép so sánh sau&mdash;có thể nằm trong mệnh đề WHERE&mdash;hiển nhiên là hợp lệ:

```
SP.SNO = S.SNO  /* OK */
```

Ngược lại, phép so sánh sau không hợp lệ:

```
SP.PNO = S.SNO  /* not OK */
```

Lý do là part number và supplier number là hai loại khác nhau, chúng thuộc hai miền khác nhau. Do đó, ý tưởng chung là các hệ thống quản trị cơ sở dữ liệu (DBMS) nên từ chối thực hiện các tính toán&mdash;join, union, divide, vân vân&mdash;mà tường minh hay ngầm, có chứa một phép so sánh giữa hai giá trị thuộc hai miền khác nhau. Ví dụ, chúng ta có một truy vấn SQL mà người dùng muốn tìm ra các suppliers không cung cấp một parts nào:

```
SELECT S.SNO , S.SNAME , S.STATUS , S.CITY
FROM S
WHERE NOT EXISTS
    ( SELECT *
      FROM SP
      WHERE SP.PNO = S.SNO )  /* not OK */
```

(Ở đây không có dấu chấm phảy kết thúc, vì đây là một biểu thức (expression), không phải lệnh (statement).)

Như phần comment nói, truy vấn này không hợp lệ. Lý do là, trong dòng cuối cùng, người dùng ý muốn nói `WHERE SP.SNO = S.SNO`, nhưng do nhầm lẫn&mdash;có thể gõ nhầm&mdash;lại viết thành <code>SP.<strong>PNO</strong> = S.SNO</code>. Trong trường hợp này, DBMS có thể xử lý một cách thân thiện như sau: ngăn người dùng tiếp tục nhập ký tự và in nổi bật lỗi (bằng màu đỏ).

Hiện nay, tôi không tìm thấy bất kỳ sản phẩm thương mại nào ứng xử theo cách tôi vừa đưa ra; mà chúng, phụ thuộc vào cách bạn cài đặt cơ sở dữ liệu, hoặc là câu truy vấn thất bại hoặc là nó trả về một kết quả sai. Hay, cũng có thể là một kết quả đúng cho một câu hỏi sai.

Do đó, xin nhắc lại, nhìn chung, DBMS nên từ chối một so sánh không hợp lệ như `SP.PNO = S.SNO`. Nhưng Codd cảm thấy, nên có một cách cho phép người dùng ép DBMS tiếp tục thực hiện một phép so sánh như thế, kể cả nó không hợp lệ, do thực tế có đôi lúc người dùng biết nhiều hơn DBMS. Lúc này, có chút khó khăn cho tôi khi phải đánh giá ý tưởng của Codd, bởi vì tôi không đồng ý với nó&mdash;nhưng hãy thử.

Giả sử công việc của bạn là thiết kế một cơ sở dữ liệu, ví dụ customers-and-suppliers, và bạn quyết định có một miền customer number và một miền khác supplier number. Bạn xây dựng cơ sở dữ liệu như vậy và triển khai nó, mọi thứ hoạt động trơn tru trong một hay hai năm. Cho đến một ngày, có một người dùng của bạn yêu cầu một câu truy vấn mà bạn chưa từng nghe, đó là "Có khách hàng nào của tôi cũng là nhà cung cấp của tôi không?" Thấy rằng đây là câu truy vấn hoàn toàn hợp lý; và cũng thấy rằng nó *có lẽ* cần đến một phép so sánh giữa customer number và supplier number (một so sánh trên hai miền giá trị khác nhau), để kiểm tra sự bằng nhau.

Căn cứ vào luận điểm trên, Codd đưa ra một thứ ông ấy gọi là "domain check override" version (DCO) cho một số toán tử quan hệ. Ví dụ, một phiên bản DCO của join sẽ thực hiện join ngay cả khi các thuộc tính nằm trên các miền khác nhau. Xét trong SQL, chúng ta có thể tưởng tượng rằng phiên bản này được thể hiện bằng một mệnh đề mới, `IGNORE DOMAIN CHECKS`, được thêm vào truy vấn SQL, như sau:

```
SELECT ...
FROM   ...
WHERE  CNO = SNO
IGNORE DOMAIN CHECKS
```

Mệnh đề mới này nên được phân quyền&mdash;với đa số người dùng không nên cho phép sử dụng (có thể chỉ có DBA[^dba] mới được phép).

Trước khi phân tích chi tiết ý tưởng DCO, tôi muốn xét một ví dụ đơn giản hơn. Xét hai truy vấn sau:

```
SELECT ...                    |  SELECT ...
FROM P , SP                   |  FROM P , SP
WHERE P.WEIGHT = SP.QTY       |  WHERE P.WEIGHT - SP.QTY = 0
```

Giả sử, rất hợp lý, rằng trọng lượng - weight và số lượng - quantity được định nghĩa trên hai miền khác nhau, nên câu truy vấn bên trái rõ ràng không hợp lệ. Nhưng còn câu bên phải? Theo Codd, nó hợp lệ. Trong cuốn sách ông ấy viết *The Relational Model for Database Management Version 2* (Addison-Wesley, 1990), Codd giải thích rằng trong trường hợp bên phải, "DBMS kiểm tra và nhận thấy hai toán hạng có cùng kiểu dữ liệu cơ bản"; "kiểu dữ liệu cơ bản" ở đây là các số, nên chúng cùng thuộc một miền giá trị và phép so sánh hợp lệ.

Việc kiểm tra miền giá trị như vậy là không thể chấp nhận được. Rõ ràng, biểu thức `P.WEIGHT = SP.QTY` và `P.WEIGHT - SP.QTY = 0` thực sự là một. Vì thế chắc chắn chúng phải cùng hợp lệ hoặc cùng không hợp lệ. Vì thế đối với tôi, dường như có một thứ rất lạ tồn tại trong cách kiểm tra miền (domain check) của Codd, còn chưa nói đến "domain check override". (Thực ra, về cơ bản, việc kiểm tra miền giá trị theo cách của Codd chỉ xảy ra trong trường hợp rất đặc biệt, khi cả hai toán hạng đều là tham chiếu tới thuộc tính. So sánh `P.WEIGHT = SP.QTY` rơi vào hạng mục đặc biệt này, còn `P.WEIGHT - SP.QTY = 0` thì không.)

Cùng xét một ví dụ đơn giản hơn nữa. Giả sử có các so sánh sau (có thể xuất hiện trong mệnh đề WHERE):

```
S.SNO = 'X4'    P.PNO = 'X4'    S.SNO = P.PNO
```

Tôi hi vọng bạn đồng ý rằng ít nhất hai so sánh đầu có khả năng là hợp lệ, so sánh thứ ba thì không. Nhưng nếu như thế, thì tôi cũng hi vọng bạn đồng ý rằng có gì đó rất lạ đang diễn ra; rõ ràng, chúng ta có ba giá trị `a`, `b`, và `c`, và có `a = c` là đúng và `b = c` là đúng, nhưng với `a = b`&mdash;thậm chí còn không thể thực hiện so sánh! Vậy điều gì đang diễn ra?

Tôi trở lại với luận điểm `S.SNO` và `P.PNO` lần lượt được định nghĩa trên hai miền `SNO` và `PNO`, và với tuyên bố của tôi miền thực ra là kiểu; thực tế, tôi đã nói từ đầu rằng `SNO` và `PNO` là hai kiểu do người dùng định nghĩa. Thấy rằng, có vẻ như cả hai kiểu này đều được lưu vật lý theo kiểu CHAR&mdash;nhưng biểu diễn vật lý là thành phần của thực thi, không phải mô hình; nó bị ẩn khỏi người dùng. Nên những toán tử áp dụng cho `SNO` và `PNO` là những toán tử được định nghĩa liên kết với hai kiểu dữ liệu này, không phải các toán tử liên kết với CHAR. Ví dụ, chúng ta có thể nối hai chuỗi ký tự, nhưng không thể nối hai supplier number (chúng ta có thể thực hiện việc này sau khi định nghĩa toán tử nối liên kết với kiểu `SNO`).

Như vậy, khi định nghĩa một kiểu dữ liệu, chúng ta cũng phải định nghĩa các toán tử liên kết với giá trị và biến có kiểu dữ liệu đó. Và một toán tử *phải* định nghĩa đó là selector operator, cái cho phép ta chọn ra, hay chỉ ra một giá trị có kiểu dữ liệu đó. Ví dụ selector cho `SNO` (thực tế cũng hay được đặt tên là `SNO`) cho phép chúng ta chọn ra một giá trị `SNO` từ biểu diễn CHAR của nó. Ví dụ:

```
SNO('S1')
```

Biểu thức này là một lời gọi `SNO` selector, và nó trả về một supplier number: là mã nhà cung cấp được biểu diễn bởi chuỗi ký tự `'S1'`. Tương tự như thế, biểu thức:

```
PNO('P1')
```

là một lời gọi `PNO` selector, và nó trả về một part number: là mã bộ phận được biểu diễn bởi chuỗi ký tự `'P1'`. Nói cách khác, hai `SNO` và `PNO` selectors hoạt động bằng cách *chuyển đổi* (*convert*) một giá trị CHAR thành một giá trị `SNO` và `PNO` tương ứng.

Trờ lại so sánh `S.SNO = 'X4'`. Hai toán hạng ở đây có kiểu khác nhau (`SNO` và CHAR). Vì chúng có kiểu khác nhau, nên chắc chắn không bằng nhau. Nhưng hệ thống đã đủ thông minh để tự định nghĩa một toán tử&mdash;`SNO` selector&mdash;thực hiện việc chuyển đổi CHAR thành `SNO` (do số hạng bên phải chỉ là "kiểu dữ liệu cơ bản"). Vì thế nó có thể gọi toán tử này ra, gọi ngầm, để chuyển số hạng kiểu CHAR thành `SNO`, tức là hệ thống thay thế so sánh cũ bằng:

```
S.SNO = SNO('X4')
```

Lúc này, chúng ta đang so sánh hai supplier number, là hợp lệ.

Tương tự, hệ thống cũng thay thế so sánh `P.PNO = 'X4'` bằng:

```
P.PNO = PNO('X4')
```

Nhưng với `S.SNO = P.PNO`, hệ thống không thể tự định nghĩa một toán tử chuyển đổi, nên so sánh này gây ra *type error:* Hai toán hạng có kiểu dữ liệu khác nhau, và không có cách khiến chúng có cùng kiểu.

*Chú ý:* Ngầm chuyển đổi kiểu dữ liệu thường được gọi là *ép kiểu* (*coercion*). Trong ví dụ thứ nhất, ta có thể nói `'X4'` được ép kiểu thành `SNO`; trong ví dụ thứ hai được ép kiểu thành `PNO`.

Tiếp tục với ví dụ, một toán tử khác bạn phải định nghĩa khi bạn tự định nghĩa kiểu dữ liệu là toán tử *THE_*, trong trường hợp này, chuyển đổi một giá trị `SNO` hay `PNO` thành chuỗi ký tự biểu diễn nó. Để cho đơn giản, giả sử hai toán tử THE_ cho `SNO` và `PNO` đều có tên là `THE_CHAR`. Khi đó, nếu chúng ta thực sự muốn so sánh `S.SNO` và `P.PNO`, thì điều hợp lý duy nhất tôi có thể nghĩ tới là chúng ta muốn kiểm tra kiểu diễn CHAR của chúng có bằng nhau hay không, cái mà có thể thực hiện như sau:

```
THE_CHAR ( S.SNO ) = THE_CHAR ( P.PNO )
```

Tức là: Chuyển supplier number về một chuỗi ký tự, chuyển part number về một chuỗi ký tự, và so sánh hai chuỗi này.

Chắc bạn đã thấy, cơ chế tôi phác họa ở đây, bao gồm toán tử selector và toán tử THE_, đưa ra cả (a) cách kiểm tra miền giá trị (domain check) và (b) domain check override, khi cần. Hơn nữa, cách này hoàn toàn rõ ràng, trực giao và không bột phát. Ngược lại, "domain check override" không thực sự hoàn thành tốt nhiệm vụ; thực tế, nó hoàn toàn vô lý, vì làm xáo trộn kiểu dữ liệu và biểu diễn (kiểu dữ liệu là khái niệm của mô hình, biểu diễn là khái niệm của thực thi).

Bây giờ, có lẽ bạn đã nhận ra những gì tôi đang nói ở đây thực ra là *strong typing* trong các ngôn ngữ lập trình, về cơ bản, có nghĩa là (a) tất cả mọi thứ&mdash;cụ thể là tất cả giá trị và biến&mdash;đều có một kiểu dữ liệu, và (b) bất kể khi nào chúng ta thực hiện một tính toán, hệ thống đều kiểm tra các toán hạng có đúng kiểu dữ liệu cho tính toán đó hay không (hoặc, có khả năng, ép toán hạng về đúng kiểu). Để ý là cơ chế này hoạt động với bất kỳ tính toán nào, không chỉ so sánh như đang thảo luận. Ví dụ, xét hai biểu thức sau:

```
P.WEIGHT * SP.QTY
P.WEIGHT + SP.QTY
```

Biểu thức thứ nhất có thể hợp lệ (nó tạo ra một giá trị trọng lượng weight khác: là trọng lượng tổng của mỗi shipment). Ngược lại, biểu thức thứ hai không hợp lệ (cộng trọng lượng và số lượng sẽ tạo ra cái gì?).

## Tính nguyên tử của giá trị (Data value atomicity)

Tôi hi vọng phần trước đã thuyết phục được bạn miền giá trị thực ra là kiểu dữ liệu, không hơn không kém. Bây giờ tôi muốn chuyển tới vấn đề *tính nguyên tử của giá trị* và khái niệm liên quan *first normal form* (viết tắt là 1NF). Trong chương 1, tôi đã nói 1NF nghĩa là tất cả tuple trong quan hệ chỉ chứa một giá trị đơn (có kiểu dữ liệu thích hợp) cho mỗi vị trí thuộc tính&mdash;và người ta thường nói kèm theo "giá trị đơn" có tính "nguyên tử".

Codd định nghĩa dữ liệu có tính nguyên tử nghĩa là dữ liệu "không thể phân chia được thành các phần nhỏ hơn bởi DBMS (ngoại trừ một số hàm đặc biệt)". Nhưng kể cả khi bỏ qua phần trong đóng mở ngoặc thì định nghĩa này cũng rất mơ hồ. Ví dụ chuỗi ký tự, nó có tính nguyên tử hay không? Tất cả sản phẩm mà tôi biết đều đưa ra vài toán tử&mdash;LIKE, SUBSTR (substring), vân vân&mdash;cùng dựa vào cơ sở rằng chuỗi ký tự có thể "phân chia được thành các phần nhỏ hơn". Vậy thì chuỗi ký tự có tính nguyên tử hay không? Bạn nghĩ sao?

Dưới đây là một số ví dụ khác về các giá trị mà tính nguyên tử của chúng vẫn là một câu hỏi mở, song ta chắc chắn muốn sử dụng chúng làm giá trị cho các thuộc tính trong quan hệ:

 * Chuỗi bit.
 * Số hữu tỉ (có thể tách thành phần nguyên và phần thập phân).
 * Ngày tháng và thời gian (có thể tách thành năm / tháng / ngày và giờ / phút / giây).

Vân vân.

Trước khi đưa ra kết luận, cùng xét một ví dụ. Xét hình 2.1 bên dưới. Quan hệ `R1` là bản rút gọn của quan hệ shipments; nó vẽ ra supplier nào cung cấp part nào, và chứa một tuple cho mỗi kết hợp `{SNO,PNO}`. Tiếp theo, để cho đơn giản, hãy đồng ý rằng, supplier number và part number là nguyên tử; tức là, ít nhất là quan hệ `R1` có dạng 1NF.

<figure>
  <img src="{{ site.baseurl }}/images/2017_01_07_r1_r2_r3.png" alt="r1 r2 r3 relations" />
  <figcaption>
    FIGURE 2.1: Relations R1, R2, and R3
  </figcaption>
</figure>

Bây giờ giả sử chúng ta thay thế `R1` bởi `R2`, vẽ ra nhà cung cấp mà cung cấp *nhóm* bộ phận (*group of parts*) (thuộc tính `PNO` trong `R2` được một số gọi là *multivalued*). Thì đa số chắc chắn sẽ nói rằng `R2` không có dạng 1NF; thực tế, nó trông giống ví dụ "nhóm lặp" ("repeating group"), và nhóm lặp là một thứ mà đa số đều đồng ý cần phải triệt tiêu khỏi 1NF (các nhóm hiển nhiên là không nguyên tử, đúng không?).

Vậy thì để cho đơn giản, hãy cùng thống nhất `R2` không có dạng 1NF. Nhưng giả sử chúng ta thay `R2` bởi `R3`. Thì tôi tuyên bố *R3 có dạng 1NF!* Do:

 * Thứ nhất, để ý tôi đã đổi tên `PNO` thành `PNO_SET`, và tôi đóng group of parts, là giá trị của `PNO_SET` vào hai ngoặc nhọn, để nhấn mạnh rằng một nhóm như thế thực sự là một giá trị đơn: một giá trị tập hợp&mdash;một tập hợp ở một mức trừu tượng nhất định, vẫn là một giá trị đơn.
 * Thứ hai, một tập hợp như `{P2,P4,P5}` *có tính nguyên tử giống như một chuỗi ký tự, không hơn không kém*.

Điều thực sự mà tôi muốn làm rõ ở đây đó là khái niệm nguyên tử *tuyệt đối không có ý nghĩa gì cả*; nó chỉ phụ thuộc vào cách chúng ta muốn thao tác với dữ liệu. Đôi khi chúng ta muốn xử lý cả một tập hợp của part number như một giá trị đơn lẻ, và đôi khi chúng ta muốn xử lý từng part number trong tập hợp đó&mdash;nhưng cũng có lúc chúng ta muốn dữ liệu có mức độ chi tiết thấp hơn, hoặc mức độ trừu tượng thấp hơn.

Quay lại một chút với quan hệ `R3`. Tôi đã vẽ ra giá trị `PNO_SET` là tập hợp tổng quát. Nhưng thực tế sẽ hữu ích hơn nếu chúng cụ thể hơn, là quan hệ (xem hình 2.2, tôi đã đổi tên thuộc tính thành `PNO_REL`). Tại sao lại hữu ích hơn? Bởi vì quan hệ, không phải tập hợp tổng quát, mới là cốt lõi của mô hình quan hệ[^relation]. Từ đó, toàn bộ sức mạnh của đại số quan hệ ngay lập tức có thể áp dụng được cho các quan hệ này&mdash;chúng có thể được restrict, project, join, vân vân. Ngược lại, nếu sử dụng các tập hợp tổng quát, thì chúng ta phải định nghĩa các toán tử mới, để thao tác với các tập hợp này.

<figure>
  <img src="{{ site.baseurl }}/images/2017_01_07_r4.png" alt="r4 relation" />
  <figcaption>
    FIGURE 2.2: Relation R4 (a revised version of R3)
  </figcaption>
</figure>

Thuộc tính `PNO_REL` là một ví dụ của *thuộc tính có giá trị quan hệ* (*relation-valued attribute*) (RVA). Tôi sẽ nói nhiều hơn về RVA trong chương 5 và chương 7; ở đây tôi chỉ muốn chú ý rằng SQL không hỗ trợ RVA. (Chính xác hơn, nó không hỗ trợ cái tương đương với RVA, *table-valued column*. Nhưng kỳ lạ, nó lại hỗ trợ cả (a) cột có giá trị là mảng (array) và (b) cột có giá trị là "multiset of rows". Một *multiset*, còn gọi là *bag*, giống như một tập hợp ngoại trừ nó cho phép giá trị trùng nhau. Như thế, cột có giá trị là multiset of rows cũng có nét giống với "table-valued column"; nhưng nó không phải là table-valued column, vì giá trị của nó không thể thao tác được với các toán tử bảng của SQL.)

Tôi chọn ví dụ trên một cách có chủ đích, vì giá trị gây sốc của nó. Sau cùng, quan hệ có RVA trông khá giống với quan hệ có nhóm lặp, và bạn có lẽ luôn nghe thấy rằng nhóm lặp không có chỗ trong thế giới cơ sở dữ liệu. Nhưng tôi đã sử dụng nhiều ví dụ khác nhau để chứng minh luận điểm của tôi: tôi đã chỉ ra thuộc tính (và do đó cả miền) có thể có giá trị mảng (array); hay bag (multiset); hay danh sách (list); hay hình ảnh; hay bản ghi âm thanh, video; hay tia X; hay dấu vân tay; hay XML document; hay bất kỳ những loại giá trị nào khác, "nguyên tử" hoặc "không nguyên tử". Thuộc tính, và miền giá trị, có thể chứa *bất kỳ thứ gì* (tức là, *bất kỳ giá trị gì*).

Vài năm gần đây, chúng ta hay nghe thấy về các "object-relational" system. Thật tình cờ, những gì đã thảo luận đã giải thích tại sao "object-relational" system chính là relational system, không hơn không kém. Sau cùng, toàn bộ về một object-relational" system chính là chúng ta có thể có giá trị thuộc tính trong quan hệ có độ phức tạp tùy ý.

## Lưu ý

[^dba]: DBA = database administrator.
[^relation]: Sự khác biệt giữa quan hệ và tập hợp tổng quát là tập hợp có thể chứa bất cứ thứ gì, còn quan hệ chỉ chứa tuple. Nhưng nó vẫn tương đồng với một tập hợp là có thể coi như một giá trị đơn.