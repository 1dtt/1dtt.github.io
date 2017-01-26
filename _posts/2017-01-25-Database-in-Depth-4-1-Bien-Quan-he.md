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

Điểm thứ nhất tôi muốn nhấn mạnh là, bất kể ta dùng cú pháp gì để diễn đạt, một phép gán quan hệ vẫn là một hành động *set-level (có cấp độ tập hợp)*. (Thực tế, *tất cả* hành động trong mô hình quan hệ đều là set-level, chúng ta sẽ thấy trong Chương 5.) Do đó, INSERT chèn một tập hợp tuple tới một relvar; DELETE xóa một tập hợp tuple từ một relvar; và UPDATE cập nhật một tập tuple trong một relvar. Đúng là chúng ta hay nói kiểu như chỉ cập nhật một tuple đơn lẻ nào đó, nhưng bạn nên hiểu rằng:

 * Nói như thế ý là tập hợp tuple đang cập nhật có cardinality 1.

 * Hơn nữa cập nhật một tập hợp tuple có cardinality 1 đôi khi không thể thực hiện được.

Ví dụ, giả sử relvar `S` chịu một ràng buộc rằng `S1` và `S4` luôn luôn chung một thành phố. Thì bất kỳ "single-tuple UPDATE" (UPDATE cho một tuple đơn lẻ) nào chỉ thay đổi `CITY` cho một trong hai supplier chắc chắn sẽ thất bại. Chúng ta phải cập nhật cả hai cùng một lúc, có thể như sau (SQL):

```
UPDATE S
SET    CITY = 'New York'
WHERE  S.SNO = SNO('S1') OR S.SNO = SNO('S4') ;
```

Cái được cập nhật ở đây hiển nhiên là một tập hợp hai tuple.

Một hệ quả là không có thứ gì trong mô hình quan hệ tương đương với "cập nhật theo vị trí" của SQL (tức là, UPDATE hay DELETE "WHERE CURRENT OF *cursor*"&mdash;sửa, xóa tại vị trí hàng hiện tại), vì theo định nghĩa, những hành động này là tuple-level (có cấp độ tuple) (hay đúng hơn là row-level), không phải set-level. Các sản phẩm SQL ngày này đều hành động như vậy, vì chúng thiếu đi sự hỗ trợ đúng đắn cho các ràng buộc toàn vẹn. Trong tương lai, nếu các sản phẩm hỗ trợ tốt hơn, thì những "cập nhật theo vị trí" có thể sẽ *không* hoạt động nữa; nghĩa là, ứng dụng hôm nay đang vận hành tốt có thể bị đổ vỡ trong ngày mai&mdash;với tôi đây là một điều không mong muốn.

Bây giờ tôi phải thú nhận. Sự thật là, nói rằng tôi đang "cập nhật một tuple"&mdash;hay rõ hơn là tập hợp tuple&mdash;là rất không chính xác (nếu không muốn nói cẩu thả). Nếu `V` là đối tượng được cập nhật, thì `V` theo định nghĩa phải là một *biến*, không phải một giá trị; và tuple, giống như quan hệ, là *giá trị*, chúng không thể sửa được, cũng từ định nghĩa. Ý nghĩa thực sự khi chúng ta nói (ví dụ) cập nhật tuple `t1` thành `t2`, trong một relvar `R`, là chúng ta đang *thay thế* tuple `t1` trong `R` bởi một tuple khác `t2`. Và cách nói này vẫn là cẩu thả. Cái chúng ta *thực sự* muốn nói là chúng ta đang thay thế quan hệ `r1`&mdash;là giá trị ban đầu của `R`&mdash;bằng một quan hệ khác `r2`. Vậy quan hệ `r2` ở đây chính xác là gì? Giả sử `s1` và `s2` là hai quan hệ lần lượt chứa chỉ tuple `t1` và `t2`; thì `r2` là `(r1 MINUS s1) UNION s2`. Nói cách khác, "cập nhật tuple `t1` thành `t2` trong relvar `R`" có thể được hiểu là xóa `t1` rồi chèn `t2`&mdash;nếu tôi được phép nói một cách không chặt chẽ rằng xóa và chèn một tuple đơn lẻ.

Tương tự, nó không đúng khi nói "cập nhật thuộc tính `A` trong tuple `t`"&mdash;hay trong quan hệ `r`, hay thậm chí trong relvar `R`. Tất nhiên, chúng ta luôn làm thế, vì nó thuận tiện (tiết kiệm rất nhiều lời giải thích); nhưng cũng giống như các thuật ngữ thân thiện tôi thảo luận trong Chương 1&mdash;điều này chấp nhận được khi và chỉ khi tất cả đều hiểu rằng lối nói như vậy chỉ gần đúng, và sự thật là nó che đi bản chất thực sự.

## Nói thêm về Khóa ứng cử

Tôi đã giải thích ý tưởng chung của khóa ứng cử trong Chương 1, bây giờ tôi muốn làm rõ hơn khái niệm này. Dưới đây là một định nghĩa:

<div class="definition">
  <strong>Định nghĩa</strong>: Giả sử <em>K</em> là một tập con của heading của relvar <em>R</em>. Thì <em>K</em> là một <em>khóa ứng cử</em> (gọi tắt là <em>khóa</em>) cho <em>R</em> khi và chỉ khi nó sở hữu cả hai tính chất sau:<br/>
    <ol>
      <li><em>Tính duy nhất (Uniqueness):</em> Không có giá trị nào của <em>R</em> chứa hai tuple khác nhau có cùng giá trị <em>K</em></li><br/>
      <li><em>Tính không thể rút gọn (Irreducibility):</em> Không có tập con nào của <em>K</em> có tính duy nhất.</li>
    </ol>
</div>

Tính duy nhất rất dễ hiểu, nhưng tôi phải nói một chút về tính chất không thể rút gọn. Ví dụ trong relvar `S`, có tập hợp thuộc tính `{SNO,CITY}`&mdash;giả sử gọi là `SK`&mdash;là một tập con của heading `S` có tính duy nhất. Nhưng nó không có tính không thể rút gọn, vì chúng ta có thể loại bỏ thuộc tính `CITY` và cái còn lại, tập hợp `{SNO}`, vẫn có tính duy nhất. Nên không thể công nhận `SK` là một khóa, do nó "quá lớn". Ngược lại, `{SNO}` không thể rút gọn, và nó là một khóa.

Tại sao chúng ta lại muốn giữ cho khóa không thể rút gọn? Một lý do là nếu chúng ta chỉ ra một "khóa" *không* có tính không thể rút gọn, DBMS sẽ không thể thực hiện ràng buộc tính duy nhất một cách đúng đắn. Ví dụ, giả sử chúng ta nói dối DBMS rằng `{SNO,CITY}` là một khóa. Thì DBMS không thể thực hiện ràng buộc rằng các supplier number `{SNO}` là duy nhất một cách "toàn bộ"; thay vào đó, nó chỉ có thể thực hiện một ràng buộc yếu hơn rằng các supplier number `{SNO}` là duy nhất trên "cục bộ", nghĩa là chúng chỉ duy nhất trong một thành phố nhất định. Đây là một lý do (không phải duy nhất) tại sao chúng ta cần khóa không kèm theo bất kỳ thuộc tính nào không cần thiết, vì mục đích xác định tính duy nhất.

Tất cả relvar ta gặp từ đầu tới giờ chỉ có một khóa. Ngược lại, dưới đây là một số ví dụ relvar có nhiều khóa. Chú ý sự chồng lặp của khóa trong ví dụ thứ hai và thứ ba:

```
VAR TAX_BRACKET BASE RELATION
  { LOW MONEY , HIGH MONEY , PERCENTAGE INTEGER }
    KEY { LOW }
    KEY { HIGH }
    KEY { PERCENTAGE } ;

VAR ROSTER BASE RELATION
  { DAY DAY_OF_WEEK , TIME TIME_OF_DAY , GATE GATE , PILOT NAME }
    KEY { DAY , TIME , GATE }
    KEY { DAY , TIME , PILOT } ;

VAR MARRIAGE BASE RELATION
  { SPOUSE_A NAME , SPOUSE_B NAME , DATE_OF_MARRIAGE DATE }
    /* assume no polygamy and no persons marrying */
    /* each other more than once ... */
    KEY { SPOUSE_A , DATE_OF_MARRIAGE }
    KEY { DATE_OF_MARRIAGE , SPOUSE_B }
    KEY { SPOUSE_B , SPOUSE_A } ;
```

Tôi sẽ kết thúc phần này với một số điểm khác. Thứ nhất, lưu ý rằng khái niệm khóa áp dụng cho relvar, không phải quan hệ. Tại sao? Vì nói một cái gì đó là khóa nghĩa là đang chỉ ra một ràng buộc toàn vẹn&mdash;cụ thể là ràng buộc tính duy nhất&mdash;và ràng buộc toàn vẹn áp dụng cho biến, không phải giá trị. (Ràng buộc toàn vẹn ép buộc các cập nhật, và cập nhật là áp dụng cho biến, không phải giá trị. Xem thêm trong chương 6.)

Thứ hai, nếu `R` là một relvar, thì `R` phải có ít nhất một khóa. Do giá trị của `R` là các quan hệ, mà quan hệ theo định nghĩa không chứa tuple trùng lặp; vì thế, ít nhất thì kết hợp tất cả thuộc tính của `R` phải có tính duy nhất.

Thứ ba, chú ý rằng các giá trị của khóa là tuple. Ví dụ với relvar `S`, nó có khóa `{SNO}` (chỉ một phần tử), giá trị của khóa này cho một tuple nào đó&mdash;giả sử, cho tuple `S1`&mdash;là:

```
TUPLE { SNO SNO('S1') }
```

(Nhớ lại từ Chương 3 rằng tập hợp con của một tuple cũng là một tuple.) Đương nhiên trong thực tế chúng ta hay nói, một cách không chính thức, rằng giá trị của khóa chỉ là `S1`&mdash;hay `SNO('S1')`&mdash;nhưng thực sự không phải như thế.

Từ điểm thứ ba này: cần nói rõ rằng khái niệm khóa, giống rất nhiều những thứ khác trong mô hình quan hệ, đều dựa vào một khái niệm cơ sở là *so sánh bằng của tuple*. Tức là, để thực hiện ràng buộc tính duy nhất, chúng ta cần chỉ ra được khi nào hai giá trị khóa bằng nhau, và đây chính là vấn đề bằng của tuple&mdash;kể cả, như trường hợp relvar `S`, các tuple chỉ có degree 1 và "trông giống" như một giá trị vô hướng.

Điểm cuối cùng liên quan tới khái niệm *phụ thuộc hàm (functional dependency)*. Giả sử `K` là khóa cho relvar `R`, và `A` là một thuộc tính của `R`. Thì `R` phải thỏa mãn phụ thuộc hàm:

&nbsp;&nbsp;&nbsp;&nbsp;<code>K &rarr; A</code>

Đọc là `K` xác định `A`.

Nói chung, phụ thuộc hàm <code>K &rarr; A</code> nghĩa là nếu hai tuple của `R` có cùng giá trị `K`, thì chúng cũng phải có cùng giá trị `A`. Nhưng nếu hai tuple có cùng giá trị `K`, trong khi `K` là khóa, thì theo định nghĩa chúng cùng là một tuple!&mdash;do đó chúng *phải* có cùng giá trị `A`. Nói cách khác, nói chung: chúng ta luôn luôn có "mũi tên phụ thuộc hàm" đi từ khóa trỏ tới tất cả mọi thứ khác trong relvar.

## Nói thêm về Khóa ngoại

Tôi đã giải thích ý tưởng chung về khóa ngoại trong Chương 1, nhưng dưới đây là một định nghĩa chính xác (chú ý một lần nữa lại dựa vào so sánh bằng tuple):

<div class="definition">
  <strong>Định nghĩa</strong>: Giả sử <em>R1</em> và <em>R2</em> là hai relvar, có thể trùng nhau, và <em>K</em> là khóa của <em>R1</em>. Giả sử <em>FK</em> là một tập con của heading của <em>R2</em> mà, có thể sau khi đổi tên các thuộc tính, bao gồm đúng các thuộc tính của <em>K</em>. Thì <em>FK</em> là một <em>khóa ngoại</em> khi và chỉ khi, tất cả tuple trong <em>R2</em> phải có một giá trị <em>FK</em> bằng với một giá trị <em>K</em> (là duy nhất) trong một tuple nào đó trong <em>R1</em>.
</div>

Như ta đã biết, trong cơ sở dữ liệu suppliers-and-parts, `{SNO}` và `{PNO}` là hai khóa ngoại trong relvar `SP`, lần lượt tham chiếu tới khóa ứng cử&mdash;thực tế, là khóa chính&mdash;trong relvar `S` và `P`. Dưới đây là một ví dụ khác:

```
VAR EMP BASE RELATION
  { ENO ENO, ..., MNO ENO, ... }
    KEY { ENO }
    FOREIGN KEY { RENAME ( MNO AS ENO ) } REFERENCES EMP ;
```

Thuộc tính `MNO` ở đây biểu diễn cho employee number cho manager (của employee); nên, "relvar tham chiếu" (`R2` trong định nghĩa) và "relvar được tham chiếu" (`R1` trong định nghĩa) trong ví dụ này là một. Ví dụ, một tuple cho employee `E3` có thể có giá trị `MNO` là `E2`, tạo nên một tham chiếu tới tuple cho employee `E2`.

Ngoài ra, tôi nên nói ra rằng mô hình quan hệ ban đầu thiết kế yêu cầu khóa ngoại phải khớp không đơn giản chỉ với khóa ứng cử, mà phải là *khóa chính* trong relvar được tham chiếu. Tuy nhiên, tôi đã đưa ra những lý do trong Chương 1 rằng không có lý luận chắc chắn để chọn một khóa ứng cử làm khóa chính; theo đó, tôi không muốn khẳng định rằng khóa ngoại nhất định phải khớp với khóa chính. (Tôi đồng ý với SQL về điểm này.)

SQL có hỗ trợ không chỉ khóa ngoại, mà còn hỗ trợ các *hành động tham chiếu (referential action)* liên kết với khóa ngoại, như CASCADE (có thể là một phần trong mệnh đề ON DELETE hay ON UPDATE). Ví dụ, lệnh CREATE TABLE cho shipments có thể bao gồm:

```
FOREIGN KEY ( SNO ) REFFERENCES S ( SNO ) ON DELETE CASCADE
```

Với một định nghĩa như thế, khi xóa một supplier sẽ kéo theo xóa tất cả shipments cho supplier đó.

Điểm cuối cùng để kết thúc phần này: Tôi thảo luận khóa ngoại vì nó cực kỳ hữu dụng, và cũng vì nó thuộc vào mô hình quan hệ. Nhưng tôi nên nhấn mạnh rằng nó không thực sự là khái niệm cơ sở&mdash;nó chỉ là cách viết nhanh cho một ràng buộc toàn vẹn thường phải có trong thực tế. (Cũng có thể nói tương tự cho khóa ứng cử, ngoại trừ khóa ứng cử không phổ biến.)