---
layout: post
title: 'Database in Depth 1.3: Mô hình so với Thực thi'
---

Trước khi đi xa hơn, có một điểm rất quan trọng tôi cần phải giải thích, bởi vì nó là cơ sở cho tất cả mọi thứ trong cuốn sách này. Mô hình quan hệ, đương nhiên, là một *mô hình dữ liệu* (*data model*). Nhưng không may mô hình dữ liệu lại có hai nghĩa khá khác nhau trong thế giới cơ sở dữ liệu. Nghĩa thứ nhất và cũng là nghĩa căn bản hơn là:

<div class="definition">
  <strong>Định nghĩa</strong>: Một <em>mô hình dữ liệu</em> (nghĩa thứ nhất) là một định nghĩa trừu tượng, đầy đủ và có tính logic về các cấu trúc dữ liệu, toán tử, vân vân, kết hợp chúng với nhau tạo nên một <em>bộ máy trừu tượng</em> cho phép người dùng có thể tương tác.
</div>

Từ định nghĩa này, chúng ta có thể phân biệt một mô hình dữ liệu theo nghĩa thứ nhất với một *thực thi* (*implementation*) của nó. Thực thi có thể được định nghĩa như sau:

<div class="definition">
  <strong>Định nghĩa</strong>: Một <em>thực thi</em> của một mô hình dữ liệu (nghĩa thứ nhất) là một thực hiện vật lý các thành phần của bộ máy trừu tượng, mà cấu thành nên mô hình dữ liệu đó, trên một bộ máy thật.
</div>

Tôi sẽ phân tích hai định nghĩa này theo các khía cạnh của mô hình quan hệ. Đầu tiên, và hiển nhiên, khái niệm *quan hệ* là thành phần của mô hình:  người dùng cần phải biết quan hệ là gì, họ phải biết rằng quan hệ được tạo nên từ các tuple và thuộc tính, họ phải biết cách diễn giải một quan hệ, vân vân. Tất cả điều này là thành phần của mô hình. Nhưng họ không cần biết các quan hệ được lưu vật lý trên máy tính như thế nào, từng đơn vị dữ liệu được mã hóa vật lý như thế nào, hay có những *index* hay *access path* vật lý nào đang tồn tại; tất cả điều này thuộc thực thi, không phải mô hình.

Hay với khái niệm *join*: Người dùng cần phải biết join là gì, họ phải biết cách gọi một join, phải biết kết quả của một join trông như thế nào, vân vân. Cũng như thế, tất cả điều này đều thuộc mô hình. Nhưng họ không cần biết cách join được thực hiện vật lý như thế nào, có những biến đổi biểu thức nào xảy ra bên dưới, hay có những *index* hay *access path* vật lý nào được sử dụng, hay những truy xuất I/O nào đã xảy ra; tất cả thuộc về thực thi, không phải mô hình.

Nói tóm lại:

 * *Mô hình* (nghĩa thứ nhất) là cái người dùng phải hiểu rõ.
 * *Thực thi* là cái người dùng không cần biết.

(Tất nhiên, tôi không nói rằng người dùng không *được phép* biết về thực thi; tôi chỉ nói là không cần biết. Nói cách khác, thực thi nên *bị ẩn khỏi người dùng*.)

Sau đây là một số hệ quả từ hai định nghĩa trên. Thứ nhất, chú ý hiệu năng về cơ bản là vấn đề của thực thi, không phải của mô hình&mdash;điểm này rất hay bị hiểu sai. Chúng ta thường nói rằng, ví dụ, "join chậm". Điều này là vô lý! Join là thành phần của mô hình, mà mô hình không thể nói là nhanh hay chậm; chỉ có thực thi mới so sánh được như thế. Nên, là hợp lý khi chúng ta nói một sản phẩm X này có thực thi nhanh hơn hay chậm hơn một sản phẩm Y khác. (Mô hình là chung cho nhiều sản phẩm.)

Đúng thật hiệu năng là vấn đề của thực thi; nhưng không có nghĩa một thực thi tốt sẽ chạy tốt nếu bạn sử dụng mô hình một cách không đúng đắn. Thật vậy, đây chính là lý do mà bạn phải hiểu rõ về mô hình: để sử dụng mô hình một cách đúng đắn. Nếu bạn viết một biểu thức quan hệ kiểu như là `S JOIN SP`, thì bạn có quyền kỳ vọng hệ thống thực hiện nó một cách hiệu quả. Nhưng nếu bạn cứ khăng khăng viết một join như thế này (pseudocode):

```
do for all tuples in S ;
    fetch S tuple into TS , TN , TT , TC ;
    do for all tuples in SP with SNO = TS ;
        fetch SP tuple into TS , TP , TQ ;
        emit TS , TN , TT , TC , TP , TQ ;
    end do ;
end do ;
```

thì chắc chắn bạn không thể nào nhận được hiệu năng tốt. (Các hệ thống quan hệ không nên được sử dụng giống như các phương thức truy cập đơn giản.)

Thứ hai, chính do sự tách biệt về logic giữa mô hình và thực thi mà chúng ta mới có được *độc lập dữ liệu vật lý* (*physical data independence*). Độc lập dữ liệu vật lý nghĩa là chúng ta có thể tự do thay đổi cách dữ liệu được lưu vật lý và truy cập vật lý mà không cần thực hiện các thay đổi tương ứng tới cách dữ liệu được nhận thức bởi người dùng. Một lý do phổ biến mà chúng ta muốn thay đổi nơi lưu trữ và cách truy cập là hiệu năng; và sự thật chúng ta có thể thực hiện những thay đổi này mà không cần thay đổi đến cách người dùng nhìn nhận dữ liệu; nghĩa là tầng ứng dụng, các câu truy vấn hiện tại, vân vân, vẫn hoạt động tốt. Do đó, rất quan trọng, độc lập dữ liệu vật lý tức là *bảo vệ sự đầu tư của bạn trong việc đào tạo người dùng (lập trình viên) và tầng ứng dụng*.

Bạn có thể thấy, từ những định nghĩa trên, sự phân biệt giữa mô hình và thực thi thật ra là một trường hợp đặc biệt&mdash;trường hợp đặc biệt quan trọng&mdash;của sự phân biệt quen thuộc giữa logic và vật chất. Tiếc thay, đa số các hệ thống SQL ngày nay đều không làm rõ những sự phân biệt này. Hậu quả trực tiếp là chúng kém độc lập dữ liệu vật lý hơn nhiều so với các hệ thống quan hệ theo lý thuyết.

Bây giờ, tôi muốn chuyển tới ý nghĩa thứ hai của thuật ngữ *mô hình dữ liệu*. Nó có thể được định nghĩa như sau:

<div class="definition">
  <strong>Định nghĩa</strong>: Một <em>mô hình dữ liệu</em> (nghĩa thứ hai) là mô hình của dữ liệu&mdash;đặc biệt là dữ liệu bền&mdash;của một tổ chức nào đó.
</div>

Nói cách khác, một mô hình dữ liệu theo nghĩa thứ hai chỉ là một *thiết kế logic của cơ sở dữ liệu* (có thể có chút trừu tượng). Ví dụ, chúng ta có thể nói mô hình dữ liệu của một ngân hàng, bệnh viện hay tổ chức chính phủ.

Vậy là tôi đã giải thích xong hai ý nghĩa khác nhau của mô hình dữ liệu, bây giờ tôi muốn trình bày một phép hoán dụ rất thích hợp cho mối liên hệ giữa chúng:

 * Một mô hình dữ liệu theo nghĩa thứ nhất giống như một ngôn ngữ lập trình, mà cấu trúc của nó có thể được sử dụng để giải quyết rất nhiều vấn đề cùng một loại, nhưng chính nó không có mối liên kết trực tiếp tới vấn đề nào.
 * Một mô hình dữ liệu theo nghĩa thứ hai giống như là một chương trình được viết bằng ngôn ngữ lập trình đó&mdash;nó sử dụng các công cụ của mô hình, theo nghĩa thứ nhất, để giải quyết một vấn đề cụ thể.

Kết luận, từ tất cả điều trên, nếu chúng ta đang nói về mô hình dữ theo nghĩa thứ hai, thì có lẽ nên sử dụng từ "relational models" ở dạng số nhiều hoặc "a" relational model (với một chủ đề nào đó). Nhưng nếu chúng ta đang nói về mô hình dữ liệu theo nghĩa thứ nhất, thì *chỉ có một mô hình quan hệ*, và nó là "the" relational model (với chủ đề xác định, là chủ đề trong quyển sách này).

Trong phần còn lại của sách, tôi sẽ sử dụng từ *mô hình dữ liệu*&mdash;thường nói gọn là *mô hình*&mdash;chỉ theo nghĩa thứ nhất.