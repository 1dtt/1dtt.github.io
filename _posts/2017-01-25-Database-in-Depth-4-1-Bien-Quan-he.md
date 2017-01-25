---
layout: post
title: 'Database in Depth 4.1: Biến Quan hệ'
---

**Chúng ta đã thấy từ Chương 1 rằng một biến quan hệ (gọi tắt là relvar) là một biến** mà giá trị của nó là các quan hệ, và chính relvar, chứ không phải quan hệ, mới là đối tượng cho các toán tử INSERT, DELETE, và UPDATE. Chúng ta cũng thấy rằng INSERT, DELETE, và UPDATE chỉ là cách viết nhanh cho các phép gán quan hệ. Tôi cũng xin nhắc lại rằng (a) nếu `R` là một relvar và `r` là một quan hệ gán cho `R`, thì `R` và `r` phải có cùng kiểu dữ liệu, và (b) các thuật ngữ *heading, body, thuộc tính (attribute), tuple, cardinality, và degree*, được định nghĩa trong Chương 3 dành cho quan hệ, tất cả đều có thể áp dụng cho relvar. Bây giờ là lúc chúng ta xét kỹ hơn những vấn đề này. Trở lại với ví dụ quen thuộc, cơ sở dữ liệu suppliers-and-parts, tôi định nghĩa các relvar cơ sở bằng **Tutorial D**:

```
VAR S BASE RELATION
  { SNO SNO, SNAME NAME, STATUS INTEGER, CITY CHAR }
    KEY { SNO } ;

VAR P BASE RELATION
  { PNO PNO, PNAME NAME, COLOR COLOR, WEIGHT WEIGHT, CITY CHAR }
    KEY { PNO } ;

VAR SP BASE RELATION
  { SNO SNO, PNO PNO, QTY QTY }
    KEY { SNO, PNO }
    FOREIGN KEY { SNO } REFERENCES S
    FOREIGN KEY { PNO } REFERENCES P ;
```

## Updating Is Set-at-a-Time (Một cập nhật là một tập hợp tại một thời điểm nhất định)

Điểm thứ nhất tôi muốn nhấn mạnh là, bất kể chúng ta dùng cú pháp gì để diễn đạt, một phép gán quan hệ vẫn là một hành động *set-level (có cấp độ tập hợp)*. (Thực tế, *tất cả* hành động trong mô hình quan hệ đều là set-level, chúng ta sẽ thấy trong Chương 5.) Do đó, INSERT chèn một tập hợp tuple tới relvar; DELETE xóa một tập hợp tuple từ relvar; và UPDATE cập nhật một tập tuple trong relvar. Giờ thì, đúng là chúng ta hay nói kiểu như chỉ cập nhật một tuple đơn lẻ nào đó, nhưng bạn nên hiểu rằng:

 * Nói như thế ý là tập hợp tuple chúng ta đang cập nhật có cardinality 1.

 * Và cập nhật một tập hợp tuple có cardinality 1 đôi khi không thể thực hiện được.

Ví dụ, giả sử relvar `S` chịu một ràng buộc rằng `S1` và `S4` luôn luôn cùng thành phố. Thì bất kỳ "single-tuple UPDATE" nào chỉ thay đổi thành phố cho một trong hai supplier chắc chắn sẽ thất bại. Chúng ta phải cập nhật cả hai cùng một lúc, có thể như sau (SQL):

```
UPDATE S
SET    CITY = 'New York'
WHERE  S.SNO = SNO('S1') OR S.SNO = SNO('S4') ;
```

Cái được cập nhật ở đây hiển nhiên là một tập hợp hai tuple.

Một hệ quả từ điều trên là không có thứ gì trong mô hình quan hệ tương đương với "cập nhật theo vị trí" của SQL (tức là, UPDATE hay DELETE "WHERE CURRENT OF *cursor*"&mdash;sửa, xóa tại vị trí hàng hiện tại), vì theo định nghĩa, những hành động này là tuple-level (có cấp độ tuple) (hay đúng hơn là row-level), không phải set-level. Các sản phẩm SQL ngày này đều hành động như vậy, vì chúng thiếu đi sự hỗ trợ đúng đắn cho ràng buộc toàn vẹn. Trong tương lai, nếu các sản phẩm hỗ trợ tốt hơn, thì những "cập nhật theo vị trí" có thể sẽ *không* hoạt động nữa; nghĩa là, ứng dụng hôm nay đang vận hành tốt có thể bị đổ vỡ trong ngày mai&mdash;với tôi đây là một điều không mong muốn.

Bây giờ tôi phải thú nhận. Sự thật là, nói rằng tôi đang "cập nhật một tuple"&mdash;hay tập hợp tuple&mdash;là rất không chính xác (nếu không muốn nói cẩu thả). Nếu `V` là đối tượng được cập nhật, thì `V` theo định nghĩa phải là một *biến*, không phải một giá trị; và tuple, giống như quan hệ, là *giá trị*, chúng không thể cập nhật được, một lần nữa cũng theo định nghĩa. Ý nghĩa thực sự khi chúng ta nói (ví dụ) cập nhật tuple `t1` thành `t2`, trong một relvar `R`, là chúng ta đang *thay thế* tuple `t1` trong `R` bởi một tuple khác `t2`. Và cách nói này vẫn là cẩu thả. Cái chúng ta *thực sự* muốn nói là chúng ta đang thay thế quan hệ `r1`&mdash;là giá trị ban đầu của `R`&mdash;bằng một quan hệ khác `r2`. Quan hệ `r2` ở đây chính xác là cái gì? Giả sử `s1` và `s2` là hai quan hệ lần lượt chứa chỉ tuple `t1` và `t2`; thì `r2` là `(r1 MINUS s1) UNION s2`. Nói cách khác, "cập nhật tuple `t1` thành `t2` trong relvar `R`" có thể được hiểu là xóa `t1` rồi chèn `t2`&mdash;nếu tôi được phép nói một cách không chặt chẽ rằng xóa và chèn một tuple đơn lẻ.

Tương tự, nó không đúng khi nói "cập nhật thuộc tính `A` trong tuple `t`"&mdash;hay trong quan hệ `r`, hay thậm chí trong relvar `R`. Tất nhiên, chúng ta luôn làm như thế, vì nó thuật tiện (tiết kiệm rất nhiều lời giải thích); nhưng cũng giống như các thuật ngữ thân thiện tôi đã thảo luận trong Chương 1&mdash;nó chấp nhận được khi và chỉ khi tất cả chúng ta đều hiểu rằng lối nói như vậy chỉ gần đúng, và sự thật là nó che đi bản chất thực sự.

## Nói thêm về Khóa ứng cử

Tôi đã giải thích ý tưởng chung của khóa ứng cử trong Chương 1, bây giờ tôi muốn làm rõ hơn khái niệm này. Dưới đây là một định nghĩa:

<div class="definition">
  <strong>Định nghĩa</strong>: Giả sử <em>K</em> là một tập con của heading của relvar <em>R</em>. Thì <em>K</em>là một <em>khóa ứng cử</em> (gọi tắt là <em>khóa</em>) cho <em>R</em> khi và chỉ khi nó sở hữu cả hai tính chất sau:<br/><br/>
    <ol>
      <li><em>Tính duy nhất (Uniqueness):</em> Không có giá trị nào của <em>R</em> chứa hai tuple khác nhau có cùng giá trị cho <em>K</em></li><br/>
      <li><em>Tính không rút gọn được (Irreducibility):</em> Không có tập con của <em>K</em> có tính duy nhất.</li>
    </ol>
</div>

Tính duy nhất rất dễ hiểu.