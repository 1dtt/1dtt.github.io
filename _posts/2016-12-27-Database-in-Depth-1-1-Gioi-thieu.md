---
layout: post
title: 'Database in Depth 1.1: Giới thiệu'
---

**Bất kỳ một chuyên gia trong lĩnh vực nào cũng phải có hiểu biết về nền móng của lĩnh vực đó.** Do đó, nếu muốn trở thành một chuyên gia về cơ sở dữ liệu, bạn cần hiểu biết mô hình quan hệ (relational model), bởi vì mô hình này là nền móng của lĩnh vực cơ sở dữ liệu. Ngày nay, tất cả khóa học quản trị cơ sở dữ liệu, dù là học thuật hay thương mại, cũng đều có ý thức dạy về mô hình quan hệ&mdash;nhưng dường như lại dạy rất tệ, và kết quả thì không đi đến đâu. Tôi cho rằng: Cộng đồng cơ sở dữ liệu chắc chắn không hiểu rõ mô hình quan hệ. Dưới đây là một số lý do:

 * Mô hình này chỉ được dạy thoáng qua. Ít nhất là đối với những người mới, rất khó để hiểu đúng các kiến thức trong sách vở, hay rất khó để hiểu được các vấn đề mà mô hình muốn giải quyết, hoặc cả hai.
 * Chính những người dạy cũng không có hiểu biết đầy đủ.
 * (Có thể.) Mô hình này hoàn toàn không được dạy&mdash;thay vào đó, người ta dạy ngôn ngữ SQL hay một biến thể (dialect) nào đó của SQL, như T-SQL của Microsoft.

Nếu sự hiểu biết của bạn về mô hình quan hệ được suy ra từ kiến thức SQL, thì tôi e rằng bạn đã hiểu sai về "thứ mà nó không phải như thế".

<div class="definition">
  <strong>Chú ý</strong>: <strong>SQL</strong> &ne; mô hình quan hệ!
</div>

Dưới đây biểu diễn một số vấn đề về tính quan hệ mà SQL không làm rõ:

 * Cơ sở dữ liệu (database), quan hệ (relation), và tuple là gì
 * Khác biệt giữa relation và kiểu dữ liệu (type)
 * Khác biệt giữa *giá trị relation* và *biến relation*
 * Liên quan giữa predicate và proposition
 * Tính hợp lệ của thuộc tính có giá trị là quan hệ (*relation-valued attribute*)
 * Vai trò quan trọng của ràng buộc toàn vẹn (*integrity constraint*)

và rất nhiều vấn đề khác (đây không phải là danh sách đầy đủ). Tất cả vấn đề trên, và tất nhiên rất nhiều vấn đề khác, đều được làm rõ trong cuốn sách này.

## Lưu ý về thuật ngữ

Trong danh sách các vấn đề về tính quan hệ kể trên, chắc bạn sẽ nhận ra ngay tôi sử dụng các thuật ngữ chính thống *relation*, *tuple*, và *attribute*. SQL không sử dụng những thuật ngữ này&mdash;thay vào đó, nó sử dụng các từ "thân thiện" hơn *table*, *row*, và *column*. Thông thường, rất dễ hiểu cho việc sử dụng từ ngữ thân thiện để mô tả một khái niệm. Tuy nhiên trong trường hợp này, có vẻ như nó lại làm méo mó đi, và thực tế đã giết chết ý nghĩa chính thống của khái niệm. Sự thật là, một relation *không phải* là một table, một tuple *không phải* là một row, và một attribute *không phải* là một column. Tất nhiên, trong đời thường, sử dụng các từ thân thiện có thể chấp nhận được, nhưng chỉ khi các bên đều hiểu rằng: từ ngữ thân thiện chỉ mang tính gần đúng và không nắm giữ bản chất thật sự. Nói ngắn gọn: Nếu bạn đã hiểu được bản chất thực sự của vấn đề, thì việc sử dụng những từ ngữ thân thiện là một ý tưởng tốt; nhưng để học hay để nhận thức bản chất của vấn đề, bạn thực sự cần hiểu thấu các thuật ngữ chính thống. Do đó trong sách này, phần lớn thời gian tôi sẽ sử dụng các thuật ngữ chính thống&mdash;và tất nhiên, tôi sẽ đưa ra định nghĩa chính xác cho chúng khi cần.

Một điểm khác về thuật ngữ: đã nói SQL cố gắng đơn giản hóa một nhóm thuật ngữ, tôi cũng phải nói thêm rằng, nó ra sức phức tạp hóa một nhóm thuật ngữ khác. Tôi muốn nói tới việc sử dụng các thuật ngữ *operator*, *function*, *procedure*, *routine*, và *method*, tất cả thực chất đều chỉ cùng một thứ (nếu có khác nhau thì cũng rất ít). Tôi sẽ chỉ dùng một thuật ngữ *operator*; vì thế, "=" (phép so sánh bằng), ":=" (phép gán), "+" (phép cộng), DISTINCT, JOIN, SUM, GROUP BY... tất cả đều là operator.

Tiện thể nhắc đến SQL, *hãy chú ý tôi sử dụng từ SQL chỉ có ý nói đến phiên bản chuẩn của ngôn ngữ đó*, không phải một biến thể (dành riêng cho từng sản phẩm, như T-SQL) nào đó&mdash;đương nhiên trừ những lúc tôi nói rõ không phải thế. Vì thế, những đánh giá của tôi đối với SQL chỉ áp dụng cho phiên bản SQL chuẩn; nếu có một đánh giá nào đó không đúng sản phẩm SQL ưu thích của bạn thì, tốt, tôi rất vui được nghe thấy điều đó.

## Lý thuyết, không phải sản phẩm

Tôi muốn dành chút thời gian để phân tích câu hỏi, như tối tuyên bố, tại sao, nếu muốn trở thành một chuyên gia về cơ sở dữ liệu, bạn cần biết về mô hình quan hệ. Lý do là mô hình quan hệ không thuộc về một sản phẩm nào cả, nó là nguyên lý (*principle*). Chúng ta có định nghĩa sau (nguồn *Chambers Twentieth Century Dictionary*):

 > **principle**: nguồn, gốc, khởi nguồn: là những thứ nền tảng: bản chất tự nhiên: cơ sở lý thuyết: một chân lý nền tảng, mà từ đó, những thứ khác được sinh sôi, nảy nở.

Đặc điểm của nguyên lý là: nó bền vững. Ngược lại, sản phẩm và công nghệ (cùng ngôn ngữ SQL) thay đổi liên tục&mdash;nguyên lý thì không. Giả sử bạn biết Oracle; có thể bạn là một chuyên gia về Oracle. Nhưng nếu Oracle là tất cả những gì bạn biết, thì tri thức này sẽ không thể áp dụng được cho một môi trường khác, giả sử DB2 hay SQL Server. Nhưng nếu bạn hiểu những nguyên lý cơ sở&mdash;tức là mô hình quan hệ&mdash;nghĩa là bạn có tri thức và kỹ năng có thể áp dụng được ở tất cả mọi nơi, và chúng không bao giờ lỗi thời.

Do đó trong cuốn sách này, chúng ta sẽ quan tâm đến nguyên lý, không phải sản phẩm, và nền móng, không phải mốt. Tất nhiên, tôi cũng nhận ra rằng đôi khi bạn phải thực hiện một số đánh đổi trong thực tế. Ví dụ, đôi khi bạn có lý do thực tiễn rất tốt để không thiết kế cơ sở dữ liệu một cách tối ưu theo lý thuyết vấn đề này tôi sẽ thảo luận trong chương 7). Ví dụ khác, xét SQL một lần nữa. Mặc dù có thể sử dụng SQL một cách quan hệ, đôi khi bạn sẽ thấy rằng&mdash;do implementation hiện hành đã đi sai hướng rất nhiều&mdash;sử dụng SQL một cách quan hệ khiến cho hệ thống phải chịu tổn thất hiệu năng lớn... trong trường hợp như thế này, bạn bị ép phải làm một thứ gì đó không "có tính quan hệ thực sự" (như là viết query theo một cách lạ lùng và quái dị nào đó để khiến cho implementation hiện hành sử dụng một index).
