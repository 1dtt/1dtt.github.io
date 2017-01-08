---
layout: post
title: 'Database in Depth 3.1: Một Tuple là gì?'
---

Đây có phải một tuple?

![types]({{ site.baseurl }}/images/2017_01_09_tuple0.png)

Không&mdash;nó là *hình ảnh* của một tuple, không phải một tuple (chú ý lần đầu tiên tôi thêm tên kiểu vào trong hình). Như chúng ta đã thấy trong chương 1, có sự khác biệt giữa một vật và một hình ảnh vẽ vật đó, và sự khác biệt này có thể rất quan trọng. Ví dụ, tuple không có thứ tự từ trái sang phải đối với các thuộc tính của nó, nên hình ảnh sau đây là một ảnh khác của cùng tuple đó:

![types]({{ site.baseurl }}/images/2017_01_09_tuple1.png)

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
`SNO(S1)`, `NAME('Smith')`, `20`, và `London`.<br/><br/>

*Chú ý:* Trong hai hình trên, tôi đều vẽ giá trị thuộc tính ở dạng đơn giản hóa. Nhưng nó hoàn toàn không chính xác khi nói rằng, giá trị `SNO` chỉ là `'S1'` hay (cẩu thả hơn) chỉ là `S1`. Một giá trị có kiểu `SNO` là một giá trị kiểu `SNO`, không phải là giá trị kiểu CHAR!&mdash;và biểu thức `SNO('S1')` mới là một *hằng số (literal)* có kiểu `SNO`.

 * *Heading*<br/>
`{SNO:CHAR, SNAME:CHAR, STATUS:INTEGER, CITY:CHAR}`

Bây giờ, một tuple là một giá trị