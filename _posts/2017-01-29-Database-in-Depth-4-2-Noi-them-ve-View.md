---
layout: post
title: 'Database in Depth 4.2: Nói thêm về View'
---

Một view là một relvar ảo; nói cách khác, nó là một relvar mà "nhìn và cảm giác" giống như một relvar cơ sở nhưng (khác với relvar cơ sở) không tồn tại song song với những relvar khác&mdash;mà được định nghĩa dựa trên relvar. Đây là một định nghĩa:

<div class="definition">
  <strong>Định nghĩa</strong>: Một <em>view</em> hay <em>relvar ảo V</em> là một relvar mà giá trị của nó tại thời điểm <em>t</em> là kết quả tính toán của một biểu thức quan hệ tại thời điểm <em>t</em> đó. Biều thức quan hệ này được chỉ ra khi định nghĩa <em>V</em> và nó phải kể ra ít nhất một relvar.
</div>

Dưới đây là hai view, "London suppliers" và "non-London suppliers" (**Tutorial D** bên trái, SQL bên phải):

```
VAR LS VIRTUAL                      │    CREATE VIEW LS AS
( S WHERE CITY = 'London' ) ;       │    ( SELECT *
                                    │      FROM   S
                                    │      WHERE  CITY = 'London' )
                                    │
                                    │
VAR NLS VIRTUAL                     │    CREATE VIEW NLS AS
( S WHERE CITY ≠'London' ) ;        │    ( SELECT *
                                    │      FROM   S
                                    │      WHERE  CITY <> 'London' )
```

## View Retrievals (Lấy dữ liệu từ view)

Xin nhắc lại, view được trông đợi "nhìn và cảm nhận" giống như các relvar cơ sở. Cụ thể, người dùng có thể thao tác trên view (được định nghĩa sau cùng) như thể nó là relvar cơ sở, nghĩa là DBMS phải ánh xạ những thao tác đó thành các thao tác thích hợp trên relvar cơ sở. Tôi nói "sau cùng" ở đây vì một điều chúng ta có thể làm, nếu view thực sự có hành vi giống relvar cơ sở, là định nghĩa view sau dựa trên view trước, như trong ví dụ SQL này:

```
CREATE VIEW LS_STATUS
  AS ( SELECT LS.SNO, LS.STATUS
       FROM LS ) ;        /* LS is the "London suppliers" view */
```

Ánh xạ các hành động chỉ đọc rất đơn giản. Giả sử chúng ta có truy vấn SQL sau trên view `LS`:

```
SELECT LS.SNO
FROM   LS
WHERE  LS.STATUS > 10
```

Đầu tiên, DBMS thay thế tham chiếu tới view trong mệnh đề FROM thành biểu thức định nghĩa view, tạo ra:

```
SELECT LS.SNO
FROM ( SELECT S.*
       FROM   S
       WHERE  S.CITY = 'London' ) AS LS
WHERE  LS.STATUS > 10
```

Biểu thức này có thể được tối giản thành:

```
SELECT S.SNO
FROM   S
WHERE  S.CITY = 'London'
AND    S.STATUS > 10
```

Quá trình trên hoạt động được chính là nhờ tính chất *closure* của đại số quan hệ. Closure nghĩa là tại bất kỳ nơi nào chúng ta được phép sử dụng tên biến để biểu diễn giá trị của biến&mdash;như trong câu truy vấn&mdash;chúng ta đều có thể thay thế tên biến đó bởi một biểu thức tổng quát hơn (miễn là giá trị biểu diễn bởi biểu thức có đúng kiểu dữ liệu, đương nhiên). Ví dụ trong mệnh đề FROM, ta có một tên bảng SQL; nên tại đây, ta cũng có thể có một biểu thức bảng SQL tổng quát hơn, và đó là lý do tại sao chúng ta được phép thay thế biểu thức định nghĩa view `LS` cho tên `LS`.

## View Updates (Cập nhật view)

Bây giờ tôi chuyển sang các thao tác cập nhật. Trước khi đi vào chi tiết, tôi muốn nhìn lại hai view London suppliers và non-London suppliers (và tôi sẽ chuyển sang **Tutorial D**):

```
VAR LS VIRTUAL ( S WHERE CITY = 'London' ) ;
VAR NLS VIRTUAL ( S WHERE CITY ≠'London' ) ;
```

Điểm quan trọng ở đây là: thay vì `S` là relvar cơ sở và `LS` và `NLS` là view, *`LS` và `NLS` có thể là relvar cơ sở và `S` có thể là view*, như:

```
VAR LS BASE RELATION
  { SNO SNO, SNAME NAME, STATUS INTEGER, CITY CHAR }
    KEY { SNO } ;

VAR NLS BASE RELATION
  { SNO SNO, SNAME NAME, STATUS INTEGER, CITY CHAR }
    KEY { SNO } ;

VAR S VIRTUAL ( LS UNION NLS ) ;
```

*Chú ý:* Để hoàn toàn tương đương, chúng ta cũng phải chỉ ra một số ràng buộc&mdash;cụ thể, ràng buộc rằng tất cả giá trị `CITY` trong `LS` là `London` và tất cả giá trị `CITY` trong `NLS` *không* phải `London`.

Thông điệp của ví dụ này, khác to lớn, rằng *relvar nào là cơ sở, relvar nào là ảo là tùy hứng*, theo đó nghĩa là không có sự phân biệt giữa relvar cơ sở và relvar ảo. Điều này gọi là *Nguyên lý hoán đổi (The Principle of Interchangeability)* (giữa relvar cơ sở và relvar ảo). Dưới đây là một số hệ quả:

 * Giống relvar cơ sở, view cũng chịu các ràng buộc toàn vẹn. (Chúng ta hay nghĩ rằng các ràng buộc toàn vẹn chỉ áp dụng tới relvar cơ sở, nhưng *Nguyên lý hoán đổi* chỉ ra luận điểm này không thực sự đúng.)

 * Cụ thể, view có khóa ứng cử. Nó cũng có thể có khóa ngoại, và cũng có thể có khóa ngoại tham chiếu tới.

 * Tôi không đề cập điểm này trong Chương 1, quy tắc "toàn vẹn thực thể" chỉ áp dụng cho relvar cơ sở, không áp dụng cho view. Nó vì thế vi phạm *Nguyên lý hoán đổi*. (Dẫu sao tôi cũng bác bỏ quy tắc này, vì nó liên quan đến null.)

 * Rất nhiều sản phẩm SQL, và cả chuẩn SQL, cung cấp tính năng "row ID". Nếu tính năng này chỉ áp dụng cho bảng cơ sở và không áp dụng cho view&mdash;có vẻ thực tế là như vậy&mdash;thì nó vi phạm *Nguyên lý hoán đổi*. Row ID không phải thành phần của mô hình quan hệ, nhưng cũng không có nghĩa là chúng không được phép hỗ trợ. Nhưng có một lưu ý quan trọng nếu row ID giống như một loại *object* ID trong ngữ cảnh hướng đối tượng (không may, đúng là như thế, trong cả chuẩn và hầu hết sản phẩm SQL), thì nó chắc chắn bị loại bỏ! Object ID là *con trỏ (pointer)*, và mô hình quan hệ tường minh loại bỏ con trỏ.

Quay lại vấn đề chính, ta *phải* có khả năng cập nhật view&mdash;nếu không, rõ ràng sẽ vi phạm *Nguyên lý hoán đổi*.

Có lẽ bạn cũng biết, hỗ trợ của SQL cho vấn đề này rất yếu, cả trong chuẩn lẫn các sản phẩm thương mại lớn. Nó thường chỉ hỗ trợ cập nhật cho các view được định nghĩa là restriction và/hoặc projection trên một relvar cơ sở đơn lẻ (và cũng có một số vấn đề). Ví dụ, xét view sau (giống hệt view trong Chương 1):

```
CREATE VIEW SST_PARIS
  AS ( SELECT S.SNO , S.STATUS
       FROM S
       WHERE S.CITY = 'Paris' ) ;
```

View này là một projection của một restriction của bảng cơ sở `S`, và ta có thể, ví dụ, thực hiện DELETE trên nó:

```
DELETE
FROM   SST_PARIS
WHERE  SST_PARIS.STATUS > 15;
```

DELETE này ánh xạ với:

```
DELETE
FROM   S
WHERE  S.CITY = 'Paris'
AND    S.STATUS > 15;
```

Rất ít sản phẩm hỗ trợ cập nhật view phức tạp hơn thế này.

Không may, tôi đang đi lạc tại một nơi còn nhiều tranh cãi. Ý kiến riêng của tôi là vấn đề cập nhật view gần như đã được giải quyết hoàn toàn (tức là, đã tồn tại lý thuyết); nhưng không phải ai cũng đồng ý với điều đó, và để thảo luận chi tiết hơn về vấn đề này cần nhiều kiến thức nền tảng vượt quá với giới hạn cuốn sách. Do đó tôi e rằng tôi chỉ có thể dẫn bạn tới một cuốn sách khác&mdash;*Databases, Types, and the Relational Model: The Third Manifesto*.

## Một số điểm khác

Còn vài điều tôi muốn đề cập để kết thúc phần này. Thứ nhất, rất nhiều người biết, nhưng vẫn đáng nhắc lại, rằng view có hai mục đích khá khác khau:

 * Người dùng định nghĩa nên view `V`, hiển nhiên biết biểu thức `X` định nghĩa `V`. Người dùng này có thể sử dụng tên `V` tại những nơi `X` xuất hiện, nhưng cách sử dụng như vậy về cơ bản chỉ là cách viết tắt.

 * Ngược lại, người dùng nhận thông tin về sự tồn tại của `V` và được phép sử dụng nó được cho là (lý tưởng) *không* biết đến biểu thức `X`; với cách dùng này, `V` sẽ nhìn và cảm nhận giống một relvar cơ sở, như ta đã thấy. Đây mới thực sự là cách sử dụng quan trọng, và là cách tôi đang tập trung tới.

Thứ hai, khi tôi giải thích view là gì ở phần đầu, tôi đã nói rằng biểu thức quan hệ định nghĩa view phải kể ra ít nhất một relvar. Tại sao? Vì nếu không, "relvar ảo" hoàn toàn không phải là một relvar!&mdash;ý tôi nó không phải một *biến*, chắc chắn không thể cập nhật được. Mà nó sẽ là một *hằng số quan hệ*, chúng ta có thể gọi là "relcon". Ví dụ:

```
CONST PERIODIC_TABLE ( RELATION {
      TUPLE { ELEMENT 'Hydrogen' , SYMBOL 'H'  , ATOMICNO 1 },
      TUPLE { ELEMENT 'Helium'   , SYMBOL 'He' , ATOMICNO 2 },
      ...
      TUPLE { ELEMENT 'Uranium'  , SYMBOL 'U'  , ATOMICNO 92 },
                                } ) ;
```

Thứ ba, còn một loại relvar dẫn xuất nữa, gọi là *snapshot (hình chụp)*. Như tên nó gợi ý, một snapshot, tuy được suy ra, nhưng là thực, không phải ảo&mdash;nghĩa là nó được biểu diễn không chỉ bởi định nghĩa (xét theo các relvar khác), mà còn vì, ít nhất là theo lý thuyết, có một bản sao dữ liệu riêng biệt. Ví dụ: 

```
VAR LSS SNAPSHOT ( S WHERE CITY = 'London' )
    REFRESH EVERY DAY ;
```

Định nghĩa một snapshot như trên giống như thực thi một truy vấn, ngoại trừ:

 * Kết quả của truy vấn được lưu trong cơ sở dữ liệu dưới một tên cụ thể (`LSS` trong ví dụ) là một relvar chỉ đọc.

 * Theo chu kỳ (`EVERY DAY` trong  ví dụ), snapshot được làm mới&mdash;giá trị hiện tại bị bỏ đi, truy vấn được thực thi lại, kết quả của thực thi mới trở thành giá trị mới cho snapshot.

Trong ví dụ, vì thế, snapshot `LSS` biểu diễn dữ liệu trong vòng 24 giờ trước.

Snapshot quan trọng trong kho dữ liệu, hệ thống phân tán, và rất nhiều nơi khác. Lý do là các chương trình ứng dụng có thể cần dữ liệu tại trước một thời điểm nào đó, như ứng dụng báo cáo, kế toán.

Một vấn đề đó là ngày nay snapshot được nhớ đến không phải là snapshot mà là *materialized view (view được hiện thực hóa)*. Nhưng snapshot *không phải* là view! View hoàn toàn không được hiện thực hóa; chúng ta đã thấy, các hành động trên view được ánh xạ thành các hành động thích hợp tới relvar cơ sở. Do đó, "materialized view" dễ thấy là một thuật ngữ mâu thuẫn. Tệ hơn nữa, ngày nay thuật ngữ *view* không có định tính thường được dùng để chỉ "materialized view", nên chúng ta đang có nguy cơ không còn một thuật ngữ đúng để chỉ view theo nghĩa gốc. Trong cuốn sách này, đương nhiên, tôi sử dụng thuật ngữ *view* theo nghĩa gốc, nhưng tôi phải cảnh báo rằng không phải lúc nào *view* cũng nghĩa như thế ở những nơi khác.
