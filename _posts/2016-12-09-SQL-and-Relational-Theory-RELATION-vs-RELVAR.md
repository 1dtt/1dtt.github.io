---
layout: post
title: 'SQL and Relational Theory: RELATION vs. RELVAR'
---

Trong lịch sử, cũng có rất nhiều nhầm lẫn về một sự khác biệt logic nữa: đó là, một bên là các quan hệ, và một bên là các *biến* quan hệ - relation *variable*.

Tạm quên đi cơ sở dữ liệu và nghĩ đến ngôn ngữ lập trình. Giả sử ta có câu lệnh sau:

```
DECLARE N INTEGER ... ;
```

Chú ý `N` ở đây *không phải một số nguyên*. Nó một *biến*, có *giá trị* là các số nguyên &mdash; mỗi số nguyên khác nhau tại một thời điểm khác nhau. Giống như thế, nếu ta có lệnh sau trong SQL &mdash;

```
CREATE TABLE T ... ;
```

&mdash; thì `T` *không phải một bảng*. Nó là một biến, một biến bảng hay (thích gọi hơn, bỏ qua những mông lung của SQL như hàng trùng nhau và thứ tự từ trái sang phải của cột) một biến quan hệ, mà giá trị của nó là những quan hệ (mỗi quan hệ khác nhau tại một thời điểm khác nhau).

Tiếp tục quan sát hình 1.3, cơ sở dữ liệu nhà-cung-cấp-và-bộ-phận. Hình này vẽ ra ba *giá trị* quan hệ: là các giá trị quan hệ tồn tại trong cơ sở dữ liệu tại một thời điểm nhất định. Nếu ta nhìn vào cơ sở dữ liệu này vào một thời điểm khác, có thể ba giá trị quan hệ đã thay đổi. Nói cách khác, `S`, `P`, và `SP` mới thực sự là biến: chính xác là biến quan hệ. Ví dụ, giả sử biến quan hệ `S` hiện tại có giá trị &mdash; giá trị quan hệ &mdash; như hình 1.3, và giả sử ta xóa các tuple (thực ra chỉ có một) cho nhà cung cấp `Athens`:

```
DELETE S WHERE CITY = 'Athens' ;
```

Sẽ thu được kết quả:

![relation value]({{ site.baseurl }}/images/post6_relation_value.png)

Theo lý thuyết, cái xảy ra là giá trị cũ của `S` được thay thế bằng một giá trị hoàn toàn mới. Tất nhiên, giá trị cũ (có năm tuple) và giá trị mới (có bốn tuple) nhìn rất giống nhau, nhưng chúng thực sự là hai giá trị khác nhau. Thực tế, một lệnh DELETE như trên tương đương về logic, và là cách viết nhanh cho toán tử gán quan hệ sau:

```
S := S MINUS ( S WHERE CITY = 'Athens' ) ;
```

Với tất cả phép gán, những gì xảy ra là (a) *biểu thức nguồn - source expression* bên tay phải được tính toán và sau đó (b) kết quả của phép tính &mdash; trong trường hợp này là một quan hệ &mdash; được gán cho *biến đích - target variable* bên tay trái.

<div class="definition">
  <strong>Ngoài lề</strong>: Tác giả không thể chỉ ra phép gán trên bằng SQL, vì SQL không trực tiếp hỗ trợ toán tử gán quan hệ. Thay vào đó, tác giả thể hiện (kèm theo DELETE) bằng một ngôn ngữ dễ hiểu gọi là <strong>Tutorial  D</strong> (in đậm). <strong>Tutorial D</strong> là ngôn ngữ Hugh Darwen và tác giả sử dụng để mô tả các ý tưởng quan hệ trong cuốn sách <em>Databases, Types, and the Relational Model: The Third Manifesto</em> (xem phụ lục G).
</div>

Nhắc lại, DELETE là cách viết nhanh cho một toán tử gán quan hệ &mdash; và tất nhiên, cũng tương tự với INSERT và UPDATE: Về cơ bản, chúng cũng là cách viết nhanh cho các toán tử gán quan hệ nào đó. Vì thế, như đã nói trong phần "Nhìn lại mô hình gốc", trong mô hình quan hệ, toán tử gán quan hệ là một toán tử cập nhật; thực sự về logic, chúng ta chỉ cần một toán tử cập nhật.

Như vậy là có sự khác biệt logic giữa giá trị quan hệ và biến quan hệ. Vấn đề là, giới cơ sở dữ liệu từ xa xưa chỉ sử dụng cùng một thuật ngữ, *quan hệ*, để gọi cả hai khái niệm này, thực tiễn này đã gây ra nhiều lẫn lộn. [^confusion] Vì thế, từ lúc này, tác giả sẽ phân biệt rất cẩn thận hai khái niệm &mdash; sẽ nói rõ ràng giá trị quan hệ để chỉ tới giá trị quan hệ và biến quan hệ để chỉ biến quan hệ. Tuy nhiên, *giá trị quan hệ* sẽ nói tắt thành *quan hệ* (giống như cách nói tắt *giá trị số nguyên* thành *số nguyên*). Và nói tắt *biến quan hệ* thành **relvar**; ví dụ, cơ sở dữ liệu nhà-cung-cấp-và-bộ-phận gồm ba *relvar* (chính xác là ba relvar cơ sở).

## Lưu ý

[^confusion]: Vậy là cộng đồng quan hệ cũng không hoàn hảo! Nhưng SQL cũng phạm cùng lỗi đó, tất nhiên, vì nó cũng chỉ có một từ *bảng*, lúc phải hiểu là giá trị bảng, lúc phải hiểu là biến bảng.
