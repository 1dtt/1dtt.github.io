---
layout: post
title: 'SQL and Relational Theory: Các tính chất của một quan hệ'
---

Bây giờ, ta quay lại phân tích những khái niệm cơ bản về tính quan hệ. Phần này sẽ tập trung vào một số tính chất của quan hệ. Đầu tiên, tất cả quan hệ đều bao gồm một *heading* và một *body*. Heading là một tập hợp các thuộc tính (thuật ngữ *thuộc tính* ở đây nói chính xác ra là một cặp tên-thuộc-tính : tên-kiểu-dữ-liệu, và trong một heading không có hai thuộc tính trùng tên), và body là tập hợp các tuple tuân theo heading đó. Ví dụ quan hệ nhà cung cấp `S` trong hình 1.3, heading có bốn thuộc tính và body có năm tuple. Chú ý, như thế nghĩa là một quan hệ thực ra không chứa các tuple &mdash; nó chứa một body, và đến lượt body này mới chứa các tuple &mdash; nhưng để đơn giản, chúng ta thường nói quan hệ trực tiếp chứa các tuple.

Mặc dù nói một cách chặt chẽ rằng heading chứa các cặp tên-thuộc-tính : tên-kiểu-dữ-liệu, nhưng ta thường bỏ qua phần tên kiểu dữ liệu. Ví dụ, thuộc tính `STATUS` có kiểu dữ liệu &mdash; giả sử, gọi là INTEGER và nó không được vẽ ra trong hình 1.3. Tuy không được vẽ ra, nhưng hãy luôn nhớ đến sự có mặt của tên kiểu dữ liệu trong heading.

Tiếp theo, số lượng thuộc tính trong heading là *degree* - *độ* (hay *arity*), của heading đó cũng cả quan hệ chứa heading đó. Số lượng tuple trong body gọi là *cardinality*, của body, cũng như cả quan hệ chứa body đó. Ví dụ, quan hệ `S` trong hình 1.3 có degree 4 và cardinality 5; trong khi đó, quan hệ `P` có degree 5 và cardinality 6, và quan hệ `SP` có degree 3 và cardinality 12. *Chú ý:* Thuật ngữ *degree* cũng được sử dụng trong liên kết với các tuple[^degree]. Ví dụ, tất cả tuple trong quan hệ `S` (giống như chính `S`) đều có degree 4.

Tiếp theo,  một quan hệ *không bao giờ* chứa các tuple trùng nhau. Tính chất này tuân theo định nghĩa body là một tập hợp các tuple, mà tập hợp trong toán học thì không chứa các phần tử trùng nhau. SQL thất bại tại đây: bảng SQL cho phép tồn tại các hàng trùng nhau, nghĩa là, bảng SQL không phải quan hệ.  Xuyên suốt cuốn sách này, tác giả *luôn luôn* sử dụng thuật ngữ quan hệ "relation" để chỉ tới một quan hệ &mdash; không có các tuple trùng nhau, theo định nghĩa &mdash; và không phải là một bảng SQL. Cũng như thế, các toán tử quan hệ luôn luôn tạo ra một kết quả không có các tuple trùng nhau, cũng theo như định nghĩa. Ví dụ, project quan hệ `S` trong hình 1.3 trên thuộc tính `CITY` sẽ tạo ra kết quả như bên trái chứ không phải bên phải:

![rows_ordering]({{ site.baseurl }}/images/post4_rows_ordering.png)

(Kết quả ở bên trái thu được thông qua câu truy vấn SQL `SELECT DISTINCT CITY FROM S`. Bỏ qua từ DISTINCT sẽ dẫn đến kết quả không mang tính quan hệ như bên phải. Chú ý là bảng bên phải không có dấu kẻ đôi dưới chân; bởi nó không có khóa, nên cũng không có khóa chính.)

Tiếp theo, các tuple trong một quan hệ *không có thứ tự*, nằm chồng lên nhau. Tính chất này một lần nữa tuân theo định nghĩa, một body là một tập hợp, mà tập hợp trong toán học không tính thứ tự giữa các phần tử (vì thế, ví dụ, `{a,b,c}` và `{c,a,b}` là cùng một tập hợp, tương tự cũng áp dụng cho mô hình quan hệ).  Tất nhiên là khi vẽ một quan hệ thành một bảng trên sách vở, ta cũng phải vẽ các hàng theo một thứ tự từ trên xuống dưới nào đó, nhưng thứ tự này thực sự không thuộc mô hình quan hệ. Trường hợp quan hệ `S` trong hình 1.3, ta có thể vẽ các hàng theo bất kể thứ tự nào &mdash; giả sử `S3`, đến `S1`, đến `S5`, đến `S4`, đến `S2` &mdash; và bức ảnh đó vẫn biểu diễn cùng một quan hệ. *Chú ý:* Tuy quan hệ không có thứ tự giữa các tuple, nhưng không có nghĩa là ta không thể thêm đặc tả ORDER BY vào câu các truy vấn. ORDER BY rất có ích cho việc hiển thị kết quả, nhưng nó không phải là một toán tử quan hệ.

Giống như thế, các thuộc tính trong quan hệ cũng không có thứ tự, nằm cạnh nhau, bởi vì một heading cũng là một tập hợp. Một lần nữa, khi vẽ một quan hệ thành một bảng trên sách vở, cũng cũng phải vẽ các cột theo một thứ tự từ trái sang phải nào đó, nhưng thứ tự này thực sự không thuộc mô hình quan hệ. Với quan hệ `S` trong hình 1.3, các cột có thể có bất kỳ thứ tự nào &mdash; chẳng hạn `STATUS`, `SNAME`, `CITY`, `SNO` &mdash; và ảnh đó vẫn biểu diễn cùng một quan hệ trong mô hình quan hệ. Và SQL cũng thất bại tại đây: bảng SQL có thứ tự từ trái sang phải đối với cột (một lý do nữa tại sao bảng SQL không phải là quan hệ). Ví dụ, hai ảnh sau biểu diễn cùng một quan hệ nhưng lại là hai bảng SQL khác nhau:

![coloumns_ordering]({{ site.baseurl }}/images/post4_columns_ordering.png)

(Hai câu truy vấn SQL tương ứng để tạo ra hai kết quả trên là `SELECT SNO, CITY FROM S` và `SELECT CITY, SNO FROM S`. Ngay lúc này, ta có thể nghĩ rằng sự khác biệt giữa hai câu truy vấn và sự khác biệt giữa hai bảng là không đáng kể; nhưng thực tế, chúng gây ra một số hệ quả nghiêm trọng, sẽ được đề cập trong các chương sau &mdash; ví dụ trong phần toán tử explicit JOIN của SQL trong chương 6.)

Cuối cùng, một quan hệ luôn luôn được *chuẩn hóa - normalize* (tương đương nó có dạng chuẩn thứ nhất *first normal form*, 1NF). [^first-normal-form] Nói một cách đơn giản nghĩa là khi vẽ một quan hệ thành bảng, với tất cả giao nhau giữa hàng và cột ta chỉ nhận được một giá trị đơn lẻ. Nghĩa là, tất cả tuple chỉ chứa một giá trị đơn lẻ, có kiểu dữ liệu thích hợp, cho mỗi thuộc tính:

![1nf]({{ site.baseurl }}/images/post4_1nf.png)

Như từng nói: thực tế là có sự khác biệt về logic giữa một quan hệ, và một bức hình vẽ quan hệ, ví dụ hình 1.1 và hình 1.3. Các cấu trúc như trong hình hoàn toàn không phải là quan hệ, mà đúng hơn chúng là ảnh vẽ của quan hệ &mdash; hay thường gọi là *bảng*, mặc dù *bảng* là một từ nặng nề của ngữ cảnh SQL. Tất nhiên, quan hệ và bảng cũng có một số điểm tương đồng nhất định, và trong ngữ cảnh thông thường, có thể chấp nhận được khi nói rằng chúng là một. Nhưng khi cần chính xác &mdash; như lúc này &mdash; thì ta phải hiểu rằng đây là hai khái niệm khác nhau.

Nói đi thì cũng nói lại, sau tất cả, việc quan hệ, một đối tượng trừu tượng của mô hình quan hệ, được biểu diễn đơn giản như vậy trên sách vở cũng mang đến thuận lợi rất lớn. Thực tế, sự biểu diễn đơn giản này khiến cho các hệ thống quan hệ dễ sử dụng, dễ hiểu, và dễ hình dung cách hệ thống vận hành. Nhưng không may sự biểu diễn đơn giản cũng dễ dẫn đến những suy luận không đúng (ví dụ, thứ tự từ trên xuống dưới của các tuple).

Một điểm nữa: Như đã nói, có *sự khác biệt về logic* giữa một quan hệ và một bức hình vẽ quan hệ. Khái niệm sau của sự khác biệt về logic được dẫn ra từ châm ngôn của Wittgenstein:

 > Tất cả khác biệt về logic đều là những khác biệt lớn.

Khái niệm này cực kỳ có ích: Nó là một "công cụ tư duy", giúp rất nhiều cho việc tư duy rõ ràng, chính xác, cũng như cho việc xác định, phân tích những hỗn độn, không may lại rất phổ biến trong thế giới cơ sở dữ liệu. Ta hãy cùng điểm lại một số khác biệt quan trọng:

 * SQL với mô hình quan hệ
 * Mô hình với thực thi
 * Mô hình dữ liệu (nghĩa thứ nhất) với mô hình dữ liệu (nghĩa thứ hai)

Và càng về sau ta sẽ càng gặp nhiều.

#### Một số điểm quan trọng

Phần tiếp nối này đề cập tới một số điểm quan trọng sẽ làm tiền đề cho các chương sau:

 * *Tất cả tập hợp con của một tuple là một tuple.* Ví dụ, xét tuple cho nhà cung cấp `S1` trong hình 1.3. Tuple này có bốn phần tử, tương ứng với bốn thuộc tính `SNO`, `SNAME`, `STATUS`, và `CITY`. Và nếu ta bỏ đi (giả sử) thành phần `SNAME`, thì những gì còn lại thực sự vẫn là một tuple, một tuple có ba phần từ (degree 3).
 * *Tất cả tập hợp con của một heading là một heading.* Ví dụ, xét heading của quan hệ `S` trong hình 1.3. Heading này chứa bốn thuộc tính `SNO`, `SNAME`, `STATUS`, và `CITY`. Và nếu ta bỏ đi (giả sử) hai thuộc tính `SNAME` và `STATUS`, thì những gì còn lại vẫn là một heading, một heading có degree 2.
 * *Tất cả tập hợp con của một body là một body.* Ví dụ, xét body của quan hệ `S` trong hình 1.3. Body này có năm tuple, tương ứng với năm nhà cung cấp `S1`, `S2`, `S3`, `S4`, và `S5`. Và nếu ta bỏ đi (giả sử) hai tuple `S1` và `S3`, thì những gì còn lại vẫn là một body, một body có cardinality 3.

<div class="definition">
  <strong>Ngoài lề</strong>: Để phù hợp với quy tắc khoa học chuẩn &mdash; ở đây ta hiểu một biểu thức có dạng "B là một tập hợp con của A" bao hàm cả trường hợp A và B bằng nhau. Vì thế, tất cả tuple đều là tập hợp con của chính nó (tương tự với heading và body). Khi muốn ngoại trừ trường hợp này, ta nói tường minh bằng thuật ngữ tập hợp <em>con thực</em> - <em>proper</em>. Ví dụ, tuple cho nhà cung cấp <code>S1</code> đúng là một tập hợp con của chính nó, nhưng không phải là một tập hợp con thực. Những chú ý này cũng áp dụng tương tự cho tập hợp cha; ví dụ, tuple cho <code>S1</code> là một tập hợp cha của chính nó, nhưng không phải một tập hợp cha thực.
</div>

Đôi điều về khái niệm *bằng - equality* &mdash; đặc biệt là khi áp dụng với tuple và quan hệ. Nói chung, hai giá trị bằng nhau khi và chỉ khi chúng là một giá trị. Ví dụ, số nguyên 3 bằng với số nguyên 3, chứ không bằng bất kỳ thứ gì khác &mdash; cụ thể là bất kỳ số nguyên nào khác. Hệt như thế, hai tuple bằng nhau khi và chỉ khi chúng là một tuple. Ví dụ hình 1.3, tuple `S1` chỉ bằng tuple `S1`, không bằng những tuple khác. Nói cách khác, hai tuple bằng nhau khi và chỉ khi (a) chúng chứa các thuộc tính giống hệt nhau và (b) hai giá trị tương ứng với mỗi thuộc tính cũng bằng nhau.

Hai tuple *trùng* nhau - *duplicate* khi và chỉ khi chúng bằng nhau: tức là chúng là một giá trị.

Áp dụng với quan hệ: Hai quan hệ bằng nhau khi và chỉ khi chúng là một quan hệ. Ví dụ hình 1.3, quan hệ `S` chỉ bằng chính nó, không bằng các quan hệ khác. Nói cách khác, hai quan hệ bằng nhau khi và chỉ khi heading của chúng bằng nhau và body của chúng bằng nhau.

## Lưu ý

[^degree]: Nó cũng được sử dụng trong liên kết với khóa.
[^first-normal-form]:  "Thứ nhất" nghĩa là ta có thể định nghĩa các dạng chuẩn có mức cao hơn &mdash; second normal form, third normal form, vân vân &mdash; chúng liên quan đến nguyên tắc thiết kế cơ sở dữ liệu.