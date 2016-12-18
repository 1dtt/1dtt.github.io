---
layout: post
title: 'SQL and Relational Theory: Mô hình và Thực thi'
---

Trước khi tiếp tục thảo luận, chúng ta có một điểm vô cùng quan trọng cần giải thích, nó là nền móng cho tất cả những thứ khác. Mô hình quan hệ là, đương nhiên, một mô hình dữ liệu (*data model*). Không may, thuật ngữ này lại có hai ý nghĩa khá là khác nhau trong thế giới cơ sở dữ liệu. Nghĩa đầu tiên và cũng là nghĩa nền tảng hơn:

<div class="definition">
  <strong>Định nghĩa</strong>: Một <em>mô hình dữ liệu - data model</em> (nghĩa đầu tiên) là một định nghĩa trừu tượng, đầy đủ và logic về các cấu trúc dữ liệu, toán tử dữ liệu, vân vân, và kết hợp chúng với nhau để tạo nên một bộ máy trừu tượng cho phép người dùng có thể tương tác.
</div>

Từ định nghĩa trên, ta có thể phân biệt một mô hình dữ liệu theo nghĩa đầu tiên với một thực thi của nó. Thực thi có thể được định nghĩa như sau:

<div class="definition">
  <strong>Định nghĩa</strong>: Một <em>thực thi - implementation</em> của một mô hình dữ liệu (nghĩa đầu tiên) là một thực hiện vật lý các thành phần của bộ máy trừu tượng cấu thành mô hình đó trên một bộ máy thật.
</div>

Bây giờ, ta sẽ phân tích hai định nghĩa này theo các khía cạnh của mô hình dữ liệu. Đầu tiên, xét khái niệm *quan hệ*. Khái niệm này là thành phần của mô hình: Người dùng phải biết quan hệ là gì, họ phải biết rằng quan hệ được tạo nên từ các tuple và thuộc tính, họ phải biết cách diễn giải một quan hệ, vân vân. Tất cả điều này là thành phần của mô hình. Nhưng họ không cần phải biết các quan hệ được lưu vật lý trên máy tính như thế nào, hay cách từng đơn vị dữ liệu được mã hóa vật lý, hay có những *index* hay *access path* vật lý nào đang tồn tại; tất cả những điều này thuộc vào thực thi, không phải mô hình.

Hay với khái niệm *join*: Người dùng phải biết join là gì, họ phải biết cách gọi một join, phải biết kết quả của một join trông như thế nào, vân vân. Cũng như thế, tất cả điều này đều thuộc mô hình. Nhưng họ không cần phải biết cách join được thực hiện vật lý như thế nào, có những biến đổi nào xảy ra trong quá trình thực thi, hay có những *index* hay *access path* vật lý nào được sử dụng, hay những truy xuất I/O nào đã xảy ra; tất cả thuộc về thực thi, không phải mô hình.

Một ví dụ nữa: *Khóa ứng cử* (gọi tắt là *khóa*), cũng như thế, là thành phần của mô hình. Người dùng chắc chắn cần phải biết khóa là gì; cụ thể, họ phải biết một khóa có tính duy nhất. Ngày nay, tính duy nhất của khóa thường được thực thi trong các hệ thống bởi một thứ gọi là "unique index"; nhưng *index* nói chung, hay *unique index* nói riêng, không thuộc vào mô hình, chúng là thành phần của thực thi. Vì thế, không được phép nhầm lẫn một *unique index* với một khóa trong ngữ cảnh quan hệ, mặc dù *unique index* được sử dụng để thực thi khóa (chính xác hơn là thực thi một số ràng buộc của khóa).

Nói tóm lại:

 * *Mô hình* (nghĩa thứ nhất) là cái người dùng phải hiểu rõ.
 * *Thực thi* là cái người dùng không cần phải biết.

Nhưng xin hãy hiểu, ở đây không nói người dùng không được phép biết về thực thi; chỉ là không cần biết. Nói cách khác, thực thi nên *bị ẩn khỏi người dùng*.

Một số hệ quả từ những định nghĩa trên. Đầu tiên, chú ý rằng tất cả mọi thứ liên quan đến hiệu năng về cơ bản đều là do thực thi, không phải vấn đề của mô hình. Điểm này rất hay bị hiểu sai. Ví dụ, ta thường nghe phàn nàn "join chậm". Điều này là vô lý. Join là thành phần của mô hình, mà mô hình không thể được nói là nhanh hay chậm; chỉ có thực thi mới nhanh hay chậm. Vì thế, ta chỉ nghe thấy những câu đại loại như sản phẩm này chạy nhanh hơn sản phẩm khác... Mô hình là chung giữa các sản phẩm.

Đúng thật hiệu năng là vấn đề của thực thi; nhưng không có nghĩa là một thực thi tốt sẽ chạy tốt nếu ta sử dụng mô hình không đúng đắn. Thực sự, đó chính xác là lý do mà ta cần hiểu rõ về mô hình: để sử dụng mô hình một cách đúng đắn. Khi ta viết một biểu thức quan hệ kiểu như `S JOIN SP`, thì ta có quyền cho rằng hệ thống thực hiện nó một cách hiệu quả. Nhưng nếu cứ nhất định viết ra một lệnh join như thế này (pseudocode) &mdash;

```
do for all tuples in S ;
    fetch S tuple into TS , TN , TT , TC ;
    do for all tuples in SP with SNO = TS ;
        fetch SP tuple into TS , TP , TQ ;
        emit TS , TN , TT , TC , TP , TQ ;
    end do ;
end do ;
```

&mdash; thì ta không thể nào nhận được hiệu năng tốt.

Những lưu ý về hiệu năng này cũng áp dụng cho SQL. Giống như các toán tử quan hệ (join và số còn lại), SQL không thể được nói là nhanh hay chậm &mdash; ta chỉ được quyền nói thế với thực thi của nó &mdash; nhưng vẫn có khả năng ta sử dụng SQL làm cho hiệu năng chậm đi. Mặc dù vậy thì vấn đề hiệu năng sẽ được nói tương đối ít trong quyển sách này, và khi nào được đề cập tới, tác giả sẽ có những lời khuyên dành cho nó.

<div class="definition">
  <strong>Ngoài lề</strong>: Nói chung, lời khuyên của tác giả là không bao giờ lấy hiệu năng làm nhân tố quyết định; sau cùng, mục đích của mô hình quan hệ là tách vấn đề hiệu năng ra khỏi mối lo của người dùng và chuyển nó cho hệ thống đảm nhiệm.<br/><br/>

  Tuy nhiên, mục đích này vẫn chưa đạt được một cách toàn vẹn, vì thế (như đã từng nói) mục đích sử dụng SQL một cách quan hệ (<em>using SQL relationally</em>) đôi khi phải đánh đổi bằng lợi ích về hiệu năng. Đó cũng là lý do cho một điều đã được đề cập, có một quy tắc lớn hơn tất cả: <em>Ta có thể làm bất kỳ điều gì ta muốn, miễn là ta biết mình đang làm gì.</em>
</div>

Trở lại với sự phân biệt giữa mô hình và thực thi. Điểm thứ hai, chính xác là do sự tách biệt mô hình và thực thi đã cho phép chúng ta có được *độc lập dữ liệu vật lý* (*physical data independence*). Độc lập dữ liệu vật lý &mdash; nghe không xuôi tai lắm &mdash; có nghĩa là ta được phép tự do thay đổi cách dữ liệu được lưu trữ vật lý và truy cập mà không làm ảnh hưởng tới cách dữ liệu được thao tác bởi người dùng. Một trong số lý do thường thấy cho sự thay đổi đó, là hiệu năng. Và sự thật là ta có thể làm điều này mà không cần thay đổi đến cách người dùng thao tác với dữ liệu, chương trình hiện tại, các câu truy vấn, tất cả vẫn hoạt động tốt sau thay đổi. Như thế, nghĩa là *không phải đào tạo lại con người, xây dựng lại ứng dụng* (cũng như không phải thiết kế lại cơ sở dữ liệu).

Index, đặc biệt các loại access path vật lý, đúng là thành phần của thực thi, không phải mô hình; chúng nằm trong tối và nên bị che đi khỏi người dùng. (Chú ý là access path không bao giờ được nhắc đến trong mô hình quan hệ.) Cũng như thế, chúng nên bị loại bỏ hoàn toàn ra khỏi SQL. **Đề nghị:** Không nên sử dụng bất kỳ một cấu trúc SQL nào vi phạm quy tắc này. (Thực tế cũng không có cấu trúc SQL nào như thế trong quy chuẩn, nhưng đối với các sản phẩm SQL thì khác.)

Từ những định nghĩa nói trên, có thể thấy sự phân biệt giữa mô hình và thực thi chỉ là một trường hợp đặc biệt &mdash; một trường hợp đặc biệt quan trọng &mdash; của sự phân biệt giữa logic và vật chất nói chung. Tiếc thay, hầu hết các hệ thống SQL ngày nay đều không làm rõ những sự phân biệt này. Và một hậu quả trực tiếp là chúng rất kém độc lập dữ liệu vật lý, và có năng lực kém hơn nhiều so với các hệ thống quan hệ theo lý thuyết.

Bây giờ, chuyển tới ý nghĩa thứ hai của thuật ngữ *mô hình dữ liệu*. Nó có thể được định nghĩa như sau:

<div class="definition">
  <strong>Định nghĩa</strong>: Một <em>mô hình dữ liệu - data model</em> (nghĩa thứ hai) là mô hình của dữ liệu &mdash;nhất là dữ liệu bền &mdash; của một công ty nào đó.
</div>

Nói cách khác, một mô hình dữ liệu theo nghĩa thứ hai chỉ là một thiết kế logic, và đôi khi có chút trừu tượng của cơ sở dữ liệu. Ví dụ, ta có thể nói mô hình dữ liệu của một ngân hàng, bệnh viện hay tổ chức chính phủ nào đó.

Sau khi đã giải thích hai nghĩa khác nhau của mô hình dữ liệu, tác giả có một phép hoán dụ để giải thích rõ thêm quan hệ giữa chúng:

* Một mô hình dữ liệu theo nghĩa đầu tiên giống như là một ngôn ngữ lập trình, mà có thể được sử dụng để giải quyết rất nhiều vấn đề cùng một loại, nhưng chính nó không có mối liên kết trực tiếp với bất kỳ vấn đề cụ thể nào.

* Một mô hình dữ liệu theo nghĩa thứ hai giống như là một chương trình cụ thể được viết bằng ngôn ngữ lập trình đó &mdash; nó sử dụng các công cụ của mô hình theo nghĩa thứ nhất, để giải quyết một vấn đề cụ thể.

Kết luận, khi nói về mô hình dữ liệu theo nghĩa thứ hai, ta sử dụng từ "relational models" ở dạng số nhiều. Nhưng khi ta nói về mô hình dữ liệu theo nghĩa thứ nhất, thì *chỉ có một mô hình quan hệ*.

Trong phần còn lại của sách, tác giả chỉ sử dụng từ *mô hình dữ liệu*, thường nói gọn là *mô hình* theo nghĩa thứ nhất.