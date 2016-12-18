---
layout: post
title: 'SQL and Relational Theory: Quan hệ cơ sở và quan hệ dẫn xuất'
---

Như đã biết, các toán tử của đại số quan hệ cho phép ta khởi đầu với một số quan cho trước, như trong hình 1.3, sau đó thu được các quan hệ mới từ những quan hệ đã cho này. Những quan hệ cho trước gọi là quan hệ *cơ sở - based*, và những quan hệ mới gọi là *dẫn xuất - derived*. Như vậy vậy, ban đầu ta phải có một cách nào đó tạo ra, hay định nghĩa những quan hệ cơ sở. Trong SQL, nhiệm vụ này được thực hiện bởi lệnh CREATE TABLE (đồng cấp với một quan hệ cơ sở trong SQL, đương nhiên, là một bảng cơ sở, cái CREATE TABLE tạo ra). Hiển nhiên là quan hệ cơ sở cần được đặt tên &mdash; ví dụ:

```
CREATE TABLE S ... ;
```

Nhưng một số quan hệ dẫn xuất, đặc biệt bao gồm cả cái gọi là *view*, cũng được đặt tên. Một view (còn được biết đến là *quan hệ ảo - virtual relation*) là một quan hệ có tên mà giá trị của nó tại một thời điểm *t* nào đó là kết quả của một biểu thức quan hệ nào đó tại thời điểm *t* (view chỉ tồn tại nhất thời). Ví dụ SQL:

```
CREATE VIEW SST_PARIS
  AS ( SELECT SNO , STATUS
       FROM S
       WHERE CITY = 'Paris' ) ;
```

Về nguyên tắc, ta có thể thao tác trên view như thể chúng là các quan hệ cơ sở,[^view] nhưng chúng không phải là các quan hệ cơ sở. Thay vào đó, ta có thể coi một view là "có thực" &mdash; thật vậy, ta có thể coi view là một quan hệ cơ sở được tạo ra bởi một biểu thức quan hệ nào đó &mdash; tại thời điểm view được tham chiếu. Nhưng cũng phải nhấn mạnh rằng, coi view có thực khi chúng được tham chiếu chỉ thuần túy là khái niệm; đây chỉ là cách nghĩ, không phải những gì thực sự diễn ra; thực tế các toán tử cập nhật không hoạt động với view. Cách view thực sự hoạt động sẽ được giải thích trong chương 9.

Có một điểm quan trọng cần làm rõ. Ta thường nghe thấy sự khác nhau giữa quan hệ cơ sở và view như sau (*cảnh báo! không đúng sự thật!*):

 * Quan hệ cơ sở thực sự tồn tại &mdash; tức là, chúng được lưu vật lý trong cơ sở dữ liệu.
 * Ngược lại, view không *thực sự tồn tại* &mdash; chúng chỉ đưa ra cách nhìn khác tới quan hệ cơ sở.

Mô hình quan hệ chủ đích không nhắc tới lưu vật lý cái gì! &mdash; thực tế, nó hoàn toàn không đề cập đến lưu trữ vật lý. Đặc biệt, nó càng không nói rằng các quan hệ cơ sở được lưu vật lý còn view thì không. Điều kiện duy nhất của mô hình quan hệ đó là tất cả những thứ được lưu vật lý phải có một ánh xạ nào đó tới quan hệ cơ sở, để cho lúc cần, ta có thể thu được các quan hệ cơ sở bằng cách này hay cách khác. Và nếu như quan hệ cơ sở có thể thu được từ những thứ được lưu vật lý, thì tất cả những thứ khác cũng như vậy. Ví dụ, có thể lưu vật lý join của các nhà cung cấp supplier với các mua bán shipment, thay vì tách riêng chúng; sau đó hai quan hệ `S` và `SP` có thể thu được qua hai phép project tương ứng trên join này. [^physically-store] Nói cách khác: Với mô hình quan hệ, các quan hệ cơ sở không "vật lý" hơn, hay kém "vật lý" hơn view.

Thực tế mô hình quan hệ chủ đích không nhắc đến lưu trữ vật lý. Mục đích tạo điều kiện cho các hãng được tự do thực thi mô hình quan hệ theo cách họ muốn &mdash; cụ thể, theo cách cảm thấy cho hiệu năng tốt nhất &mdash; mà không vướng tới tính độc lập dữ liệu vật lý. Thực tế đáng buồn là hầu hết các hãng sản phẩm SQL dường như không hiểu điều này (hoặc là không cân nhắc tới); họ ánh xạ các bảng cơ sở trực tiếp tới bộ nhớ vật lý, vì thế sản phẩm của họ có độ độc lập dữ liệu vật lý kém hơn nhiều so với các hệ thống quan hệ theo lý thuyết. Thực tế này được thể hiện chính trong chuẩn SQL (cũng như nhiều tài liệu SQL khác), điển hình là &mdash; khá phổ biến &mdash; cụm từ "tables and views." Rõ ràng khi nói như thế, ai cũng ấn tượng rằng bảng và view là hai thứ khác nhau, cũng có thể "bảng" luôn luôn nghĩa là bảng cơ sở, và cũng có thể bảng cơ sở được lưu vật lý, còn view thì không. Nói tóm lại, tất cả muốn nhấn mạnh ở đây rằng view *đúng là* một quan hệ (hay bảng); tức là, ta có thể  thực hiện cùng các toán tử trên view giống như trên các quan hệ (ít nhất là trong mô hình quan hệ). Vì thế, xuyên suốt cuốn sách, sẽ sử dụng thuật ngữ *quan hệ* để chỉ tới một quan hệ (có thể là quan hệ cơ sở, view, hay kết quả của truy vấn, vân vân); và nếu muốn chỉ rõ ràng một quan hệ cơ sở, sẽ nói "quan hệ cơ sở". **Đề nghị:** Đừng vấp phải cái bẫy sơ đẳng rằng thuật ngữ *quan hệ* chỉ nghĩa là quan hệ cơ sở &mdash; hay *bảng* trong SQL chỉ có nghĩa là bảng cơ sở. Cũng như thế, đừng nghĩ rằng quan hệ cơ sở (hay bảng cơ sở trong SQL) phải được lưu vật lý.

## Lưu ý

[^view]: Có thể bạn nghĩ rằng điều này không đúng 100% với các toán tử cập nhật, giống cách nghĩ của các sản phẩm SQL hiện nay. Dẫu sao, tác giả vẫn cho rằng điều này là đúng về nguyên tắc.
[^physically-store]: Cách thực hiện này không thỏa đáng nếu tồn tại nhà cung cấp (ví dụ `S5` trong hình 1.3) hiện tại không cung cấp bộ phận nào.
