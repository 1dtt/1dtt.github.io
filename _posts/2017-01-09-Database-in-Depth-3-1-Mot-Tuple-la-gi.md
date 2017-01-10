---
layout: post
title: 'Database in Depth 3.1: Một Tuple là gì?'
---

## Một Tuple là gì?

Đây có phải một tuple?

![tuple]({{ site.baseurl }}/images/2017_01_09_tuple0.png)

Không&mdash;nó là *hình ảnh* của một tuple, không phải một tuple (chú ý lần đầu tiên tôi thêm tên kiểu vào trong hình). Như chúng ta đã thấy trong chương 1, có sự khác biệt giữa một vật và một hình ảnh vẽ vật đó, và sự khác biệt này có thể rất quan trọng. Ví dụ, tuple không có thứ tự từ trái sang phải đối với các thuộc tính của nó, nên hình ảnh sau đây là một ảnh khác của cùng tuple đó:

![tuple]({{ site.baseurl }}/images/2017_01_09_tuple1.png)

Tôi chắc chắn cần phải sử dụng đến các hình ảnh như thế này, nhưng làm ơn hãy nhớ rằng chúng *chỉ là* hình ảnh, và chúng đôi khi gợi nên một số điều không đúng sự thật.

Sau khi đã làm rõ, giờ tôi có thể nói tuple chính xác là gì:

<div class="definition">
  <strong>Định nghĩa</strong>: Cho <em>T1, T2, ..., Tn (n &#8805; 0)</em> là các tên kiểu dữ liệu, có thể giống nhau. Liên kết với mỗi <em>Ti</em> là một tên thuộc tính khác nhau <em>Ai</em>; mỗi kết hợp tên-thuộc-tính : tên-kiểu tạo nên một <em>thuộc tính</em>. Liên kết với mỗi thuộc tính là một giá trị <em>vi</em> có kiểu dữ liệu <em>Ti</em>; mỗi kết hợp thuộc-tính : giá-trị tạo nên một <em>thành phần (component)</em>. Tập hợp của tất cả <em>n</em> thành phần là <em>t</em>, là một <em>giá trị tuple</em> (gọi tắt là <em>tuple</em>) trên các thuộc tính <em>A1, A2, ..., An</em>. Giá trị <em>n</em> là <em>độ (degree)</em> của <em>t</em>; một tuple có degree 1 là <em>unary</em>, một tuple có degree 2 là <em>binary</em>, một tuple có degree 3 là <em>ternary</em>, ..., và tổng quát một tuple có degree <em>n</em> là <em>n</em>-ary. Tập hợp tất cả <em>n</em> thuộc tính là <em>heading</em> của <em>t</em>.
</div>

Ví dụ, cho một trong hai hình ảnh trên (tuple cho supplier `S1`), chúng ta có:

 * *Độ*<br/>
Bốn. Heading của tuple cũng có degree 4.

 * *Các tên kiểu dữ liệu*<br/>
`SNO`, `NAME`, INTEGER, và CHAR.

 * *Các tên thuộc tính tương ứng*<br/>
`SNO`, `SNAME`, `STATUS`, và `CITY`.

 * *Các giá trị thuộc tính tương ứng*<br/>
`SNO(S1)`, `NAME('Smith')`, `20`, và `London`.<br/>

   *Chú ý:* Trong hai hình trên, tôi đều vẽ giá trị thuộc tính ở dạng đơn giản hóa. Nhưng nó hoàn toàn không chính xác khi nói rằng, giá trị `SNO` chỉ là `'S1'` hay (cẩu thả hơn) chỉ là `S1`. Một giá trị có kiểu `SNO` là một giá trị kiểu `SNO`, không phải là giá trị kiểu CHAR!&mdash;và biểu thức `SNO('S1')` mới là một *hằng số (literal)* có kiểu `SNO`.

 * *Heading*<br/>
`{SNO:CHAR, SNAME:CHAR, STATUS:INTEGER, CITY:CHAR}`

Giờ thì, một tuple là một giá trị; vì thế giống như tất cả giá trị khác, nó có một kiểu dữ liệu, và kiểu này, giống tất cả kiểu khác, đều có tên. Trong **Tutorial D**, tên kiểu có dạng `TUPLE{H}`, và `{H}` là heading. Với ví dụ của chúng ta, tên tuple là:

```
TUPLE { SNO SNO, SNAME NAME, STATUS INTEGER, CITY CHAR }
```

(nhưng thứ tự các thuộc tính là tùy ý).

Xin nhắc lại, một tuple là một giá trị. Vì thế, giống như tất cả giá trị khác, nó phải được trả về bởi một lời gọi toán tử selector (một lời gọi tuple selector, đương nhiên, khi giá trị là tuple). Dưới đây là một lời gọi tuple selector cho ví dụ của chúng ta (vẫn bằng **Tutorial D**):

```
TUPLE { SNO SNO('S1'), SNAME NAME('Smith'),
                       STATUS 20, CITY 'London' }
```

(thứ tự của các thành phần là tùy ý).

*Chú ý:* Về cú pháp. Như bạn thấy, từ khóa `TUPLE` có hai nhiệm vụ trong **Tutorial D**&mdash;một là sử dụng trong lời gọi tuple selector và hai là trong khai báo kiểu tuple. Từ khóa `RELATION` cũng tương tự như vậy.

Cuối cùng, một điều về SQL. Đương nhiên, SQL hỗ trợ hàng, không phải tuple; cụ thể, nó hỗ trợ *kiểu hàng*, *hàm tạo kiểu hàng* (*row type constructor*), và *hàm tạo giá trị hàng* (*row value constructor*), mà lần lượt có chút tương đồng với *kiểu tuple*, *TUPLE type generator*, và *tuple selector*. Nhưng những tương tự này cực kỳ lỏng lẻo. Ví dụ, biểu thức SQL này:

```
ROW ( 1, 2 )
```

là một ví dụ về hàm tạo giá trị hàng, (a) có các thành phần vô danh và (b) không biểu diễn cùng một hàng với hàm tạo `ROW (2, 1)`.

*Chú ý:* Từ khóa `ROW` trong hàm tạo giá trị là tùy chọn.

## Một số hệ quả quan trọng

Bây giờ, tôi muốn nêu bật một số hệ quả quan trọng từ các định từ trong phần trước. Thứ nhất là: *tuple không chứa null*. Do theo định nghĩa, tất cả tuple chứa một giá trị (có kiểu thích hợp) cho mỗi vị trí thuộc tính, và ta thấy từ Chương 1 rằng null không phải giá trị.[^null] Tất nhiên, tuple không chứa null thì quan hệ cũng vậy; nên ngay lập tức chúng ta đã có ít nhất một lý do để loại bỏ khái niệm null&mdash;nhưng tôi cũng sẽ đưa thêm một vài lý do thực tế khác, trong phần "Tại sao null bị loại bỏ".

Hệ quả tiếp theo: *tất cả tập con của một tuple là một tuple và tất cả tập con của một heading là một heading*. Ví dụ cho tuple supplier `S1` của chúng ta, cái mà chúng ta có thể gọi là "giá trị `{SNO, CITY}`" trong tuple `S1` chính nó cũng là một tuple khác:

![tuple]({{ site.baseurl }}/images/2017_01_09_tuple2.png)

Và kiểu dữ liệu của nó là:

```
TUPLE { SNO SNO, CITY CHAR }
```

Giống như vậy, dưới đây cũng là một tuple (có kiểu `TUPLE {SNO SNO}`):

![tuple]({{ site.baseurl }}/images/2017_01_09_tuple3.png)

Nếu chúng ta muốn truy cập tới giá trị của một thuộc tính&mdash;ví dụ `SNO('S1')`&mdash;thì chúng ta phải trích xuất từ tuple có chứa thuộc tính đó. **Tutorial D** sử dụng cú pháp `SNO FROM t` (trong đó `t` là một biểu thức biểu diễn một tuple có chứa thuộc tính `SNO`). SQL sử dụng ký hiệu chấm: `t.SNO`.

Tiếp theo, tôi chắc chắn bạn có biết tập hợp rỗng là một tập con của mọi tập hợp. Nên một tuple có heading rỗng, từ đó có tập các thành phần là rỗng, vẫn là một tuple hợp lệ!&mdash;cho dù có chút khó khăn khi vẽ một tuple như thế trên sách vở, và tôi cũng không cố làm việc đó. Một tuple với heading rỗng có kiểu dữ liệu `TUPLE{}` (không có thành phần nào); chúng ta đôi khi gọi tường minh nó là *0-tuple*, để nhấn mạnh rằng nó là một tuple có degree 0. Lúc này, có thể bạn sẽ nghĩ một tuple như thế dường như không có nhiều tác dụng trong thực tiễn; nhưng hóa ra, có thể khá bất ngờ, nó lại cực kỳ quan trọng. Tôi sẽ nói thêm về điều này trong phần "TABLE_DUM và TABLE_DEE".

Hệ quả quan trọng cuối cùng có liên quan đến khái niệm *so sánh bằng hai tuple*. (Nhớ lại trong chương 2 rằng toán tử so sánh "=" được định nghĩa cho mọi kiểu dữ liệu, và tuple cũng không ngoại lệ.) Về cơ bản, hai tuple bằng nhau khi và chỉ khi chúng là cùng một tuple. Nhưng nó cực kỳ đáng giá để phát biểu một cách tường minh định nghĩa về sự bằng nhau giữa hai tuple; vì rất nhiều thứ trong mô hình quan hệ đều phụ thuộc vào định nghĩa này&mdash;như khóa ứng cử, khóa ngoại, và đa số toán tử của đại số quan hệ. Sau đây là một định nghĩa chính xác:

<div class="definition">
  <strong>Định nghĩa</strong>: Hai tuple <em>t1</em> và <em>t2</em> bằng nhau khi và chỉ khi chúng có cùng các thuộc tính <em>A1, A2, ..., An</em>&mdash;tức là, chúng có cùng kiểu dữ liệu&mdash;và với tất cả <em>i</em> (<em>i</em> = 1, 2, ..., <em>n</em>), chúng ta có giá trị <em>v1</em> của <em>Ai</em> trong <em>t1</em> bằng với giá trị <em>v2</em> của <em>Ai</em> trong <em>t2</em>.
</div>

Cũng như thế (có vẻ hiển nhiên, nhưng tôi vẫn phải nói ra), hai tuple *trùng nhau (duplicate)* khi và chỉ khi chúng bằng nhau.

Hệ quả tức thì của định nghĩa trên là tất cả 0-tuples trùng lẫn nhau, nên chúng ta có quyền sử dụng từ "the" 0-tuple thay cho "a" 0-tuple, và thực sự chúng ta hay làm như vậy.

Chú ý cuối cùng rằng hai toán tử so sánh "<" và ">" không áp dụng cho tuple.

## Lưu ý

[^null]: SQL thường (không phải luôn luôn) chỉ tường minh null là giá trị, *giá trị null*.