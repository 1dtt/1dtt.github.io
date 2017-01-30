---
layout: post
title: 'Database in Depth 4.3: Relvar và Predicate'
---

Đây là phần quan trọng nhất trong chương này. Bản chất của nó là: có một cách khác để hiểu relvar. Ý tôi là, đa số mọi người đều nghĩ về relvar chỉ là tập tin (tập chứa thông tin, file)&mdash;một tập tin khá trừu tượng (abstract, không có chi tiết về mối quan hệ), (có thể từ *disciplined (có tổ chức, có nguyên tắc)* đúng hơn so với từ trừu tượng), dẫu sao vẫn là tập tin. Nhưng có một cách khác nhìn nhận relvar, một cách mà tôi tin rằng sẽ đưa tới một hiểu biết sâu hơn nhiều về những gì thực sự diễn ra. Như sau.

Xét relvar suppliers `S`. Giống tất cả relvar khác, relvar này biểu diễn một phần gì đó trong thế giới thực. Cụ thể hơn: heading của relvar này biểu diễn một *predicate*, là một phát biểu tổng quát về một phần nào đó trong thế giới thực (nó tổng quát vì nó là *parameterized (có tham số truyền vào)*). Predicate trông như sau:

*Supplier `SNO` trong hợp đồng, có tên là `SNAME`, có trạng thái `STATUS`, và có vị trí tại thành phố `CITY`.*

Predicate này là *sự diễn giải (interpretation)*, hay *sự diễn giải có dụng ý (intended interpretation)*&mdash;nói cách khác, là *ý nghĩa*, còn gọi là *intension*[^intension] (chú ý chính tả)&mdash;cho relvar `S`.

Bạn có thể hiểu một predicate là một *hàm chân trị*. Giống tất cả hàm khác, nó có một tập tham số (parameter), nó trả về một kết quả khi được gọi, và (vì giá trị trả về là giá trị chân lý) kết quả là TRUE hoặc FALSE. Với predicate ví dụ, các tham số là `SNO`, `SNAME`, `STATUS`, và `CITY` (tương ứng với các thuộc tính trong relvar, đương nhiên). Khi chúng ta gọi hàm này&mdash;*tạo predicate (instantiate the predicate)*&mdash;chúng ta thay thế đối số (argument) cho tham số. Giả sử chúng ta thay `S1`, `Smith`, `20`, và `London`. Thì chúng ta thu được *phát biểu (proposition)* sau:

*Supplier `S1` trong hợp đồng, có tên là `Smith`, có trạng thái `20`, và có vị trí tại thành phố `London`.*
 
Nói chung, một proposition là một phát biểu (không phải tổng quát) phải là đúng hoặc sai. Ví dụ:

 1. Edward Abbey đã viết *The Monkey Wrench Gang*.

 2. William Shakespeare đã viết *The Monkey Wrench Gang*.

Phát biểu thứ nhất là đúng và thứ hai là sai. Tuy nhiên, những phát biểu mà có liên kết với relvar chỉ là những phát biểu đúng, tôi sẽ giải thích:

 * Thứ nhất, tất cả relvar đều liên kết với một predicate, gọi là *relvar predicate*.

 * Giả sử relvar `R` có predicate `P`. Thì mỗi tuple `t` tồn tại trong `R` tại một thời điểm nhất định có thể được hiểu là đang biểu diễn một proposition `p`, thu được bằng cách gọi hay tạo (invoking hay instantiating) `P` tại thời điểm đó với các giá trị thuộc tính của `t` là các đối số.

 * Và (rất quan trọng!) theo quy ước tất cả proposition `p` phải có kết quả là TRUE.

Do đó, với relvar `S`, chúng ta có các proposition sau:

*Supplier `S1` trong hợp đồng, có tên là `Smith`, có trạng thái `20`, và có vị trí tại thành phố `London`.*
 
*Supplier `S2` trong hợp đồng, có tên là `Jones`, có trạng thái `10`, và có vị trí tại thành phố `Paris`.*
 
*Supplier `S3` trong hợp đồng, có tên là `Blake`, có trạng thái `30`, và có vị trí tại thành phố `Paris`.*

Vân vân.

<div class="definition">
  <strong>Thêm định nghĩa</strong>: Giả sử <em>P</em> là relvar predicate hay intension cho relvar <em>R</em>, và giá trị của <em>R</em> tại thời điểm đang xét là <em>r</em>. Thì <em>r</em>&mdash;hay đúng hơn là body của <em>r</em>&mdash;tạo nên <em>extension</em> của <em>P</em> tại thời điểm đó. Chú ý, vì thế, extension thay đổi theo thời gian, nhưng intension thì không.
</div>

## Biểu thức quan hệ

Những khái niệm tôi đang thảo luận trong phần này đều có thể áp dụng tới bất kỳ biểu thức quan hệ nào. Ví dụ, xét biểu thức sau, biểu diễn một projection của suppliers `S` trên tất cả thuộc tính ngoại trừ `CITY`:

```
S { SNO , SNAME , STATUS }
```

Kết quả của projection chứa tất cả tuple có dạng:

```
TUPLE { SNO s , SNAME n , STATUS t}
```

là một tuple có dạng:

```
TUPLE { SNO s , SNAME n , STATUS t , CITY c}
```

đang tồn tại trong `S` với một giá trị `CITY` nào đó, `c`. Nói cách khác, kết quả của biểu thức quan hệ trên biểu diễn một extension (tập hợp các proposition trả về TRUE tại thời điểm hiện tại) của một predicate có dạng:

*Tồn tại một thành phố `CITY` nào đó sao cho tại đó có supplier `SNO` có trong hợp đồng, có tên là `SNAME`, có trạng thái `STATUS`.*

Predicate này biểu diễn ý nghĩa của biểu thức quan hệ `S{SNO,SNAME,STATUS}`. Nó chỉ có ba tham số và quan hệ tương ứng (projection của suppliers trên tất cả thuộc tính ngoại trừ `CITY`) chỉ có ba thuộc tính&mdash;`CITY` *không* phải là tham số, mà gọi là một *bound variable*[^bound-variable], theo thực tế rằng nó "được định lượng (quantified)", bởi cụm *Tồn tại một thành phố nào đó*. [^existential-quantifier] Có một cách để thấy rõ hơn&mdash;rằng predicate chỉ có ba tham số, không phải bốn&mdash;là quan sát predicate sau tương đương về logic với predicate đang xét:

*Supplier `SNO` trong hợp đồng, có tên là `SNAME`, có trạng thái `STATUS`, và có vị trí tại một thành phố nào đó (ở một nơi nào đó, nhưng chúng ta không biết là nơi nào).*

Từ tất cả những điều trên, một view biểu diễn một predicate. Ví dụ, nếu view `SST` được định nghĩa như sau:

```
VAR SST VIRTUAL ( S { SNO , SNAME , STATUS } ) ;
```

thì relvar predicate cho `SST` chính là:

*Supplier `SNO` trong hợp đồng, có tên là `SNAME`, có trạng thái `STATUS`, và có vị trí tại một thành phố nào đó.*

Còn một điểm cuối cùng tôi muốn nói về predicate và proposition. Tôi đã nói predicate có một tập tham số. Tuy nhiên, như thường lệ, tập hợp *có thể* rỗng&mdash;và nếu đúng thế, predicate trở thành proposition! (Chắc chắn nó là một phát biểu phải đúng hoặc sai.) Nói cách khác, một proposition là một predicate *suy biến (degenerate)*; tất cả proposition đều là predicate, nhưng đa số predicate *không phải* proposition.

## Lưu ý

[^intension]: *Intension* và *extension*, trong *logic*, là hai từ tương quan, để nói về một khái niệm. "Intension" biểu diễn nội dung bên trong của khái niệm, mà cấu thành định nghĩa chính thức; và "extension" biểu diễn phạm vi ứng dụng của khái niệm bằng cách đặt tên một vật cụ thể mà khái niệm biểu diễn. Ví dụ, intension của "tàu biển" là một danh từ "phương tiện chuyên chở trên biển", trong khi extension là tập các đối tượng như tàu chở hàng, tàu chở khách, tàu chiến, vân vân.

[^bound-variable]: Một biến trước đó được tự do truyền giá trị, nhưng hiện tại đang được gắn với một giá trị hoặc tập giá trị cụ thể.

[^existential-quantifier]: Lượng từ chỉ số lượng (như "some" và "all"), cho biết bao nhiêu phần tử của một predicate là đúng. Ví dụ "Tồn tại số thực x nào đó sao cho x^2 + 1 = 0" là mệnh đề sai.