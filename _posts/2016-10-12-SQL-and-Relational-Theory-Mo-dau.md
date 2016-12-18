---
layout: post
title: 'SQL and Relational Theory: Mở đầu'
---

SQL ở khắp mọi nơi. Nhưng lại rất khó sử dụng: Nó phức tạp, khó hiểu, và dễ lỗi. Để có một cơ may nào đó viết ra được code SQL đúng, tức là code làm chính xác những gì ta mong muốn, không hơn, không kém, ta phải tuân theo một số nguyên tắc nhất định. Và luận điểm của quyển sách này là nguyên tắc ***using SQL relationally*** (sử dụng SQL một cách quan hệ). Nhưng như thế nghĩa là gì? SQL không có tính quan hệ?

Thực tế là SQL là ngôn ngữ chuẩn được sử dụng cho các cơ sở dữ liệu quan hệ, nhưng chính nó lại không có tính quan hệ. Sự thật là, SQL tách rời khỏi lý thuyết quan hệ ([relational theory](https://en.wikipedia.org/wiki/Relational_theory)) ở tất cả khía cạnh, ví dụ như hàng trùng nhau (*duplicate rows*), *null*... và nhiều ví dụ khác nữa. Kết quả là, SQL dễ khiến ta mắc lỗi. Nếu không muốn thế, ta cần hiểu rõ lý thuyết quan hệ, những sự khác biệt của SQL; và những cách phòng tránh các vấn đề SQL có thể gây ra. Nói cách khác, ta cần sử dụng SQL một cách quan hệ. Sau đó, vờ như SQL thực sự có tính quan hệ, ta có thể tận hưởng lợi ích khi làm việc với một hệ thống quan hệ thực sự.

Mô hình quan hệ ([relational model](https://en.wikipedia.org/wiki/Relational_model)) được giới thiệu lần đầu tiên vào năm 1969, và bây giờ, hơn 45 năm sau, dường như nó vẫn chưa được hiểu rõ bởi cộng đồng sử dụng cơ sở dữ liệu.

## Mô hình quan hệ đã bị hiểu sai đi rất nhiều

Một chuyên gia trong bất kỳ lĩnh vực nào cũng đều phải có hiểu biết về nền tảng của lĩnh vực đó. Vì thế, nếu muốn trở thành chuyên gia về cơ sở dữ liệu, ta phải hiểu mô hình quan hệ, nó là nền tảng của cơ sở dữ liệu. Ngày nay, tất cả khóa học quản trị cơ sở dữ liệu, cho dù là học thuật hay thương mại, cũng đều có ý thức dạy về mô hình quan hệ &mdash; nhưng dường như lại dạy rất tệ, và kết quả thì không đi đến đâu. Điều này có thể do một vài nguyên nhân sau:

 * Chỉ được dạy thoáng qua. Với những người mới học, rất khó để thấy được sự liên quan của những thứ được dạy tới sách vở, hay rất khó để hiểu các vấn đề mà mô hình quan hệ muốn giải quyết.
 * Chính người dạy cũng không có sự hiểu biết đầy đủ về tài liệu, sách vở.
 * Cũng có thể mô hình quan hệ không hề được dạy một tý nào cả. Thay vào đó, họ dạy SQL, hay một biến thể nào đó của SQL, được Oracle, Microsoft... tạo ra.

Vì thế, quyển sách này hướng tới những người học cơ sở dữ liệu nói chung, và những người học SQL nói riêng.  ***SQL và mô hình quan hệ không phải là một***. Dưới đây là một số vấn đề về tính quan hệ mà SQL không rõ ràng:

 * Cơ sở dữ liệu (*database*), quan hệ (*relation*), và *tuple* là gì.
 * Sự khác nhau giữa giá trị (*relation value*) và biến (*relation variable*).
 * Sự liên quan giữa *predicate* và *proposition*.
 * Sự quan trọng của tên thuộc tính (*attribute name*).
 * Vai trò quyết định của ràng buộc tính toàn vẹn (*integrity constraint*).
 * Lý thuyết thông tin (*The Information Principle*) và ý nghĩa của nó.

và rất nhiều vấn đề khác (đây không phải là danh sách đầy đủ).

Nếu sự hiểu biết của ta về mô hình quan hệ được suy ra từ sự hiểu biết về SQL, thì có lẽ ta đã hiểu sai về "thứ mà nó không phải như thế". Kết quả là, ta sẽ phải gạt bỏ đi những thứ mà ta đã biết sau khi đọc quyển sách này &mdash; và đó là một việc không dễ dàng.

## Một số lưu ý khi nhắc đến thuật ngữ

Ở phần trước, tác giả đã sử dụng các thuật ngữ chính thống như là *relation*, *tuple*, và *attribute*. SQL không sử dụng những thuật ngữ này, mà dùng những từ "thân thiện" hơn, như *table*, *row*, và *column*.  Thông thường, rất dễ hiểu cho việc sử dụng từ ngữ thân thiện để mô tả một khái niệm. Tuy nhiên trong trường hợp này, có vẻ như nó lại làm méo mó đi, và thực tế đã giết chết ý nghĩa chính thống của khái niệm. Sự thật là, một quan hệ (relation) *không phải* là một bảng (table), một tuple *không phải* là một hàng (row), và một thuộc tính (attribute) *không phải* là một cột (column). Tuy nhiên, trong ngữ cảnh thông thường, không chính thống, việc sử dụng các từ thân thiện là có thể chấp nhận được, nhưng chỉ khi tất cả các bên đều hiểu rằng: từ ngữ thân thiện chỉ mang tính gần đúng và không nắm giữ bản chất thật sự. Nói ngắn gọn: Nếu ta đã hiểu được bản chất thực sự của vấn đề, thì việc sử dụng những từ ngữ thân thiện là một ý tưởng tốt; nhưng ban đầu để học hay nhận thức bản chất của vấn đề, ta cần nắm vững các thuật ngữ chính thống. Vì thế, trong sách này, tác giả thường sử dụng các thuật ngữ chính thống, và chỉ sử dụng thuật ngữ của SQL trong ngữ cảnh SQL.

Một điểm nữa: Nói rằng SQL đơn giản hóa thuật ngữ, nhưng đôi khi nó lại làm phức tạp lên. Ví dụ nó sử dụng các từ *operator*, *function*, *procedure*, *routine* và *method*, tất cả thực ra đều biểu diễn cho cùng một thứ (nếu có khác nhau thì cũng rất ít). Trong sách này, tác giả sẽ chỉ dùng từ *operator* (toán tử); vì thế, "=" (phép so sánh bằng), ":=" (phép gán), "+" (phép cộng), DISTINCT, JOIN, SUM, GROUP BY... tất cả đều là operator.

Thêm nữa, về SQL, khi sử dụng một thuật ngữ, nó có nghĩa là tác giả đã sử dụng một thuật ngữ được quy chuẩn, ngoại trừ một số rất ít trường hợp mà ngữ cảnh bắt buộc:

 * Việc sử dụng thuật ngữ theo quy chuẩn không thích hợp. Ví dụ, tác giả sử dụng *table expression* thay cho thuật ngữ chuẩn *query expression*, do hai nguyên nhân: Đầu tiên, giá trị mà những biểu thức biểu diễn thực sự là một bảng chứ không phải một query; thứ hai, query không phải ngữ cảnh duy nhất mà một biểu thức như thế được sử dụng. (Thực ra quy chuẩn có sử dụng thuật ngữ *table expression*, nhưng nó cũng không được sử dụng một cách đúng đắn; cụ thể, nó chỉ tới thứ đi theo sau mệnh đề SELECT trong biểu thức SELECT - FROM - WHERE - GROUP BY  - HAVING.)
 * Tiếp theo, không phải lúc nào sử dụng thuật ngữ table expression cũng là đúng, cho dù đáng lý ra là như thế. Ví dụ, trong lời gọi explicit JOIN, mặc dù nó đúng là biểu diễn một bảng, nhưng nó không thể là một table expression "độc lập" (mức ngoài cùng khi lồng nhau), cũng không thể là một table expression nằm trong hai dấu ngoặc đơn, cấu thành một subquery.
 * Tác giả bỏ qua các khía cạnh của quy chuẩn mà được xem như là vụn vặt &mdash; đặc biệt là nếu nó không thuộc vào cái gọi là Core SQL, hay không liên quan lắm tới mô hình quan hệ. Ví dụ gồm có OLAP function, dynamic SQL, temporary table.

Một điểm cần chú ý, tất cả các sản phẩm SQL đều thêm vào một số tính năng không hề có trong quy chuẩn. Thông thường đối với những tính năng như thế: Có thể sử dụng nếu cần &mdash; nhưng bỏ nếu chúng vi phạm các quy tắc quan hệ. Tuy nhiên, ở đây và tất cả mọi nơi khác, quy tắc quan trọng hơn tất cả, đó là: ***Ta có thể làm bất cứ thứ gì mà ta muốn, miễn là ta hiểu rõ đang làm gì.***

## Lý thuyết chứ không phải sản phẩm

Tại sao, muốn trở thành một chuyên gia cơ sở dữ liệu, ta cần có hiểu biết về mô hình quan hệ? Lý do là, mô hình quan hệ không chỉ dùng riêng cho một sản phẩm bất kỳ, nó là nguyên lý (principle). Định nghĩa về nguyên lý (nguồn *Chambers Twentieth Century Dictionary*):

 > **principle**: nguồn, gốc, khởi nguồn: là những thứ nền tảng: bản chất tự nhiên: cơ sở lý thuyết: một chân lý nền tảng, mà từ đó, những thứ khác được sinh sôi, nảy nở.

Đặc điểm của nguyên lý là: Nó tồn tại lâu dài. Ngược lại, sản phẩm và công nghệ (bao gồm ngôn ngữ SQL) thay đổi liên tục &mdash; nguyên lý thì không. Ví dụ, giả sử ta biết Oracle; giả sử ta là chuyên gia về Oracle. Nhưng nếu chỉ có thế, thì tri thức này sẽ không thể sử dụng được trong một môi trường khác, như DB2 hay SQL Server. Nhưng nếu ta biết những nguyên lý cơ bản &mdash; tức là mô hình quan hệ &mdash; nghĩa là ta có tri thức, kỹ năng có thể dùng được ở tất cả mọi nơi.

Vì thế, quyển sách này sẽ quan tâm đến nguyên lý, không phải sản phẩm, và nền tảng, không phải xu hướng hay mốt. Nhưng trong thực tế luôn có những sự đánh đổi. Đôi khi, ta thực sự có lý do để không thiết kế cơ sở dữ liệu theo đúng lý thuyết. Và với SQL, mặc dù có thể sử dụng nó một cách quan hệ, đôi khi, ta thấy rằng - do dự án đã đi chệch quá xa - hay tốn quá nhiều hiệu năng để làm thế... trong những trường hợp đó, ta buộc phải làm những thứ "không thực sự quan hệ". Tuy nhiên, khi phải thực hiện những sự đánh đổi đó, ta nên thực hiện *dựa trên khái niệm*. Tức là:

 * Hiểu rõ mình đang làm gì.
 * Nếu làm theo lý thuyết thì sẽ thế nào, và ta phải có lý do thuyết phục để làm khác đi.
 * Cần ghi lại lý do, để cho nếu chúng trở nên không hợp lý trong tương lai (ví dụ, sản phẩm có một bản release tốt hơn), thì ta có thể quay lại làm theo lý thuyết.

Câu nói sau &mdash; của Leonardo da Vinci &mdash; vì thế đã hơn 500 tuổi:

 > Người nào thích hành động không dựa vào lý thuyết thì cũng giống như là thủy thủ lái một con thuyền mà không có bánh lái hay la bàn, và họ không bao giờ chắc chắn nơi con thuyền sẽ đến. **Hành động luôn luôn phải dựa trên một tri thức lý thuyết vững vàng.**

(Phần in đậm là của tác giả thêm vào.)
