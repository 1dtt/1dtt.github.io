---
layout: post
title: 'Database in Depth 1.5: Relation so với Relvar'
---

Sự thật là, từ xa xưa đến giờ, rất nhiều người nhầm lẫn giữa quan hệ (relation, cái được nói tới từ đầu đến giờ) và *biến* quan hệ.

Hãy tạm quên đi cơ sở dữ liệu và nhớ đến ngôn ngữ lập trình. Giả sử tôi có câu lệnh sau:

```
DECLARE N INTEGER ... ;
```

Thì `N` ở đây *không phải một số nguyên integer*. Mà nó là một *biến*, các *giá trị* của nó là số nguyên (mỗi số nguyên khác nhau tại mỗi thời điểm khác nhau). Giống hệt như thế, nếu trong SQL chúng ta có:

```
CREATE TABLE T ... ;
```

thì `T` *không phải một bảng*; nó là một biến, một *biến bảng* (hay tôi thích gọi bằng&mdash;bỏ qua những tật xấu của SQL như null và hàng trùng nhau&mdash;một *biến quan hệ*) có các giá trị là quan hệ (mỗi quan hệ tại mỗi thời điểm khác nhau).

Xét hình 1.3 thêm một lần nữa. Hình này vẽ ra ba giá trị quan hệ: là các giá trị quan hệ xuất hiện trong cơ sở dữ liệu tại một thời điểm nhất định.  Nếu chúng ta nhìn vào cơ sở dữ liệu này vào một thời điểm khác, có thể ba giá trị quan hệ này đã thay đổi. Nói cách khác, `S`, `P`, và `SP` mới thực sự là biến: chính xác là biến quan hệ. Ví dụ, giả sử biến quan hệ `S` hiện tại có giá trị&mdash;giá trị quan hệ&mdash;đang như hình 1.3, và giả sử ta xóa các tuple (thực ra chỉ có một) cho suppliers trú tại `Athens`:

```
DELETE S WHERE CITY = 'Athens' ;
```

Chúng ta sẽ thu được kết quả:

![relation value]({{ site.baseurl }}/images/2017_01_06_relation_value.png)

Theo lý thuyết, những gì đang diễn ra là giá trị cũ của `S` được thay thế bằng một giá trị hoàn toàn mới. Tất nhiên, giá trị cũ (có năm tuple) và giá trị mới (có bốn tuple) nhìn rất giống nhau, nhưng chúng thực sự là hai giá trị khác nhau.  Thực tế, một lệnh DELETE như trên tương đương về logic, và là cách viết nhanh cho phép gán quan hệ sau[^relational-assignment]:

```
S := S MINUS ( S WHERE CITY = 'Athens' ) ;
```

Theo cách giống như vậy, tất nhiên, lệnh INSERT và UPDATE cũng là cách viết nhanh cho hai phép gán quan hệ nào đó. Vì thế, như tôi đã đề cập trong phần "Nhìn lại mô hình gốc", phép gán quan hệ được thực hiện bởi toán tử gán quan hệ (toán tử update), và thực sự nó là toán tử update duy nhất mà chúng ta cần.

Như vậy là có sự khác biệt về logic giữa giá trị quan hệ và biến quan hệ. Vấn đề là, giới cơ sở dữ liệu từ xa xưa chỉ sử dụng chung một thuật ngữ, *quan hệ*, để chỉ cả hai khái niệm, và thói quen này đã dẫn đến nhầm lẫn. Do đó, từ thời điểm này tôi sẽ phân biệt chúng một cách cẩn thận&mdash;tôi sẽ nói giá trị quan hệ khi muốn nói tới giá trị quan hệ, và biến quan hệ khi muốn nói tới biến quan hệ. Tuy nhiên, tôi cũng sẽ nói tắt *giá trị quan hệ* thành *quan hệ* (giống như cách chúng ta nói tắt *giá trị số nguyên* thành *số nguyên*). Và tôi sẽ nói tắt *biến quan hệ* thành *relvar*; ví dụ, tôi sẽ nói cơ sở dữ liệu suppliers-and-parts chứa ba *relvar* (chính xác là ba relvar cơ sở).

## Lưu ý

[^relational-assignment]: Tôi không thể diễn giải điều này bằng SQL, vì SQL không trực tiếp hỗ trợ phép gán quan hệ. Trong toàn bộ cuốn sách này, tôi sẽ cố gắng diễn giải bằng SQL nhiều nhất có thể&mdash;nhưng khi không thể, vì một lý do nào đó, như ở đây, tôi sẽ sử dụng một ngôn ngữ đơn giản gọi là **Tutorial  D** (in đậm).
