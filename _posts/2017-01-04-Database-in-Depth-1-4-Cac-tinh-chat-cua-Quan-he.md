---
layout: post
title: 'Database in Depth 1.4: Các tính chất của quan hệ'
---

Bây giờ hãy cùng trở lại phân tích những khái niệm cơ bản về tính quan hệ. Trong phần này, tôi muốn tập trung vào một số tính chất của quan hệ. Đầu tiên, tất cả quan hệ đều có một *heading* và một *body*: heading là một tập hợp các thuộc tính (thuật ngữ *thuộc tính* ở đây chính xác là một cặp tên-thuộc-tính : tên-kiểu-dữ-liệu), và body là tập hợp các tuple tuân theo heading đó. Ví dụ quan hệ suppliers `S` trong hình 1.3, heading có bốn thuộc tính và body có năm tuple. Chú ý, như thế nghĩa là một quan hệ thực ra không chứa các tuple&mdash;nó chứa một body, và đến lượt body này mới chứa các tuple&mdash;nhưng để đơn giản, chúng ta thường nói trực tiếp quan hệ chứa các tuple.

Mặc dù nói một cách chặt chẽ rằng heading chứa các cặp tên-thuộc-tính : tên-kiểu-dữ-liệu, nhưng chúng ta thường bỏ qua phần tên kiểu dữ liệu trên hình vẽ. Ví dụ, thuộc tính `STATUS` có một kiểu dữ liệu&mdash;giả sử là INTEGER, nhưng nó không được vẽ ra trong hình 1.3. Tuy vậy, bạn đừng bao giờ quên sự mặt của kiểu dữ liệu trong heading.

Tiếp theo, số lượng thuộc tính trong heading là *degree* (*độ*) (hay *arity*), của heading đó cũng như cả quan hệ chứa heading đó. Số lượng tuple trong body gọi là *cardinality*, của body, cũng như cả quan hệ chứa body đó. Ví dụ, quan hệ `S` trong hình 1.3 có degree 4 và cardinality 5; trong khi, quan hệ `P` có degree 5 và cardinality 6, và quan hệ `SP` có degree 3 và cardinality 12. *Chú ý:* Thuật ngữ *degree* cũng được sử dụng trong liên kết với các tuple.[^degree] Ví dụ, tất cả tuple trong quan hệ `S` (giống như chính `S`) đều có degree 4.

Tiếp theo, quan hệ *không bao giờ* chứa các tuple trùng nhau. Tính chất này tuân theo định nghĩa body là một tập hợp các tuple, mà tập hợp trong toán học thì không chứa các phần tử trùng nhau. SQL thất bại tại đây: bảng SQL cho phép tồn tại các hàng trùng nhau, nghĩa là, bảng SQL không phải quan hệ (relation). Do đó, làm ơn hãy hiểu rằng, tôi *luôn luôn* sử dụng thuật ngữ "relation" để chỉ tới một quan hệ&mdash;không có các tuple trùng nhau, theo định nghĩa&mdash;và không phải là một bảng SQL. Làm ơn cũng hiểu rằng các toán tử quan hệ *luôn luôn* tạo ra một kết quả không có các tuple trùng nhau, một lần nữa cũng theo định nghĩa.  Ví dụ, project quan hệ `S` trong hình 1.3 trên thuộc tính `CITY` sẽ tạo ra kết quả như bên trái chứ không phải bên phải:

![rows_ordering]({{ site.baseurl }}/images/2017_01_04_rows_ordering.png)

(Kết quả ở bên trái thu được thông qua câu truy vấn SQL `SELECT DISTINCT CITY FROM S`. Bỏ qua DISTINCT sẽ dẫn đến kết quả không có tính quan hệ như bên phải. Chú ý bảng bên phải không có dấu kẻ đôi dưới chân; và vì nó không có khóa, nên nó cũng không có khóa chính.)

Tiếp theo, các tuple trong một quan hệ *không có thứ tự*, nằm chồng lên nhau. Tính chất này một lần nữa tuân theo định nghĩa, một body là một tập hợp, mà tập hợp trong toán học không tính thứ tự giữa các phần tử (ví dụ, `{a,b,c}` và `{c,a,b}` là cùng một tập hợp, tương tự cũng áp dụng cho mô hình quan hệ). Tất nhiên là khi vẽ một quan hệ thành một bảng trên sách vở, chúng ta cũng phải vẽ các hàng theo một thứ tự từ trên xuống dưới nào đó, nhưng thứ tự này thực sự không tương ứng với bất cứ thứ gì trong mô hình quan hệ. Ví dụ quan hệ `S` trong hình 1.3, ta có thể vẽ các hàng theo bất kể thứ tự nào&mdash;giả sử `S3`, đến `S1`, đến `S5`, đến `S4`, đến `S2`&mdash;và hình ảnh đó vẫn biểu diễn cùng một quan hệ. *Chú ý:* Tuy quan hệ không có thứ tự giữa các tuple, nhưng không có nghĩa là ta không thể thêm đặc tả ORDER BY vào câu truy vấn. ORDER BY rất hữu ích trong việc hiển thị kết quả, nhưng nó không phải là một toán tử quan hệ.

Theo cách giống như thế, các thuộc tính trong quan hệ cũng *không có thứ tự*, nằm cạnh nhau, bởi vì một heading cũng là một tập hợp toán học. Một lần nữa, khi vẽ quan hệ thành một bảng trên sách vở, chúng ta cũng cũng phải vẽ các cột theo một thứ tự từ trái sang phải nào đó, nhưng thứ tự này thực sự không tương ứng với bất cứ thứ gì trong mô hình quan hệ. Ví dụ quan hệ `S` trong hình 1.3, các cột có thể có bất kỳ thứ tự nào&mdash;chẳng hạn `STATUS`, `SNAME`, `CITY`, `SNO`&mdash;và hình ảnh đó vẫn biểu diễn cùng một quan hệ trong mô hình quan hệ. Bất ngờ, SQL cũng thất bại tại đây: bảng SQL có thứ tự từ trái sang phải đối với cột (một lý do nữa tại sao bảng SQL không phải là quan hệ). Ví dụ, hai hình dưới đây biểu diễn cùng một quan hệ nhưng lại là hai bảng SQL khác nhau:

![coloumns_ordering]({{ site.baseurl }}/images/2017_01_04_columns_ordering.png)

(Hai truy vấn SQL tương ứng để tạo ra hai kết quả trên là `SELECT SNO, CITY FROM S` và `SELECT CITY, SNO FROM S`. Ngay lúc này, có thể bạn sẽ nghĩ sự khác biệt giữa hai bảng là không đáng kể; nhưng thực tế, chúng dẫn tới một số hệ quả nghiêm trọng, tôi sẽ đề cập trong các chương sau.)

Tiếp theo, quan hệ luôn luôn *được chuẩn hóa* (*normalized*) (tương đương chúng có dạng chuẩn thứ nhất *first normal form*, 1NF). [^first-normal-form] Nói một cách đơn giản nghĩa là khi vẽ một quan hệ thành bảng, với tất cả giao nhau giữa hàng và cột chúng ta chỉ nhận được một giá trị đơn lẻ. Nói đơn giản hơn nữa, nghĩa là tất cả tuple chỉ chứa một giá trị đơn lẻ, có kiểu dữ liệu thích hợp, cho mỗi vị trí thuộc tính.

Tiếp theo, chúng ta hãy phân biệt quan hệ *cơ sở* (*base*) và quan hệ *dẫn xuất* (*derived*). Như tôi đã giải thích từ trước, các toán tử của đại số quan hệ cho phép ta bắt đầu với một số quan hệ&mdash;có thể như những quan hệ trong hình 1.3&mdash;và sau đó thu được các quan hệ mới từ những quan hệ ban đầu này. Quan hệ cho trước gọi là quan hệ cơ sở; quan hệ mới gọi là quan hệ dẫn xuất. Như vậy, hiển nhiên rằng một hệ thống quan hệ trước hết cần phải đưa ra một cách định nghĩa các quan hệ cơ sở. Trong SQL, nhiệm vụ này được thực hiện bởi lệnh CREATE TABLE (đồng cấp với một quan hệ cơ sở trong SQL, đương nhiên, là một *bảng* cơ sở). Hiển nhiên là quan hệ cơ sở cần phải có tên. Ví dụ:

```
CREATE TABLE S ... ;
```

Nhưng một số quan hệ dẫn xuất&mdash;đặc biệt bao gồm cái gọi là *view*&mdash; cũng được đặt tên. Một view (còn gọi là *quan hệ ảo* (*virtual relation*)) là một quan hệ có tên mà giá trị của nó tại một thời điểm *t* nào đó là kết quả của một biểu thức quan hệ nào đó tại thời điểm *t*. Ví dụ SQL:

```
CREATE VIEW SST_PARIS
  AS ( SELECT SNO , STATUS
       FROM S
       WHERE CITY = 'Paris' ) ;
```

Về nguyên tắc, bạn có thể thao tác trên view như thể chúng là các quan hệ cơ sở, nhưng chúng *không* phải là quan hệ cơ sở; thay vào đó, bạn có thể coi một view là "có thực"&mdash;thật vậy, bạn có thể coi một view là một quan hệ cơ sở được tạo ra bởi một biểu thức quan hệ&mdash;tại thời điểm chúng được tham chiếu. (Dù vậy tôi cũng phải nhấn mạnh rằng, coi view là có thực khi chúng được tham chiếu chỉ thuần túy là khái niệm; đây là cách nghĩ, không phải những gì thực sự diễn ra; thực tế các toán tử cập nhật không hoạt động với view. Cách view thực sự hoạt động như thế nào sẽ được giải thích trong chương 4.)

Có một điểm quan trọng tôi cần làm rõ ở đây. Có lẽ bạn thường nghe thấy mọi người miêu tả sự khác nhau giữa quan hệ cơ sở và view như sau:

 * Quan hệ cơ sở thực sự tồn tại&mdash;tức là, chúng được lưu vật lý trong cơ sở dữ liệu.
 * Ngược lại, view không *thực sự tồn tại*&mdash;chúng chỉ đơn thuần đưa ra cách nhìn khác vào quan hệ cơ sở.

*Nhưng mô hình quan hệ không hề nói tới cái gì sẽ được lưu vật lý!* Đặc biệt, nó *không* nói rằng quan hệ cơ sở được lưu vật lý. Điều kiện duy nhất đó là tất cả những thứ được lưu vật lý phải có một ánh xạ nào đó tới các quan hệ cơ sở, để những quan hệ cơ sở này có thể được tạo ra khi cần (trong bất kỳ trường hợp nào). Nếu quan hệ cơ sở có thể được tạo ra từ những thứ được lưu vật lý, thì tất cả những thứ khác cũng có thể như thế. Ví dụ, chúng ta có thể lưu vật lý join của suppliers và shipments, thay vì lưu chúng riêng biệt; thì quan hệ cơ sở `S` và `SP` có thể được tạo ra, bằng hai phép project tương ứng trên join này. Nói cách khác: đối với mô hình quan hệ, quan hệ cơ sở không "vật lý" hơn, hay kém "vật lý" hơn view.

Thực ra mô hình quan hệ chủ đích không nhắc đến việc lưu trữ vật lý. Mục đích để tạo điều kiện cho các hãng được tự do thực thi mô hình theo cách họ muốn&mdash;cụ thể, theo cách mà họ cảm thấy cho hiệu năng tốt nhất&mdash;mà không vướng bận tới tính độc lập dữ liệu vật lý. Thực tế đáng buồn là hầu hết các hãng sản phẩm SQL dường như không hiểu điều này (hoặc không để ý tới); họ ánh xạ trực tiếp các bảng cơ sở tới bộ nhớ vật lý, và (như tôi đã nói trong phần trước) sản phẩm của họ có tính độc lập dữ liệu vật lý kém hơn nhiều so với các hệ thống quan hệ theo lý thuyết. Thực tế này cũng được thể hiện chính trong chuẩn SQL (cũng như nhiều tài liệu SQL khác), điển hình là&mdash;khá phổ biến&mdash; cụm từ "tables and views". Rõ ràng bất cứ ai gặp phải cụm từ này cũng đều có ấn tượng rằng bảng và view là hai thứ khác nhau, cũng có thể "bảng" luôn luôn nghĩa là bảng cơ sở, và cũng có thể bảng cơ sở được lưu vật lý, còn view thì không. Tóm lại tất cả những gì tôi muốn nói ở đây là view *đúng là* bảng (hay, quan hệ); tức là, chúng ta có thể thực hiện cùng các toán tử trên view giống như trên các quan hệ (ít nhất là trong mô hình quan hệ). Vì thế, xuyên suốt cuốn sách, tôi sẽ dùng thuật ngữ *quan hệ* để chỉ tới một quan hệ (có thể là quan hệ cơ sở, view, hay kết quả của truy vấn, vân vân); và nếu muốn nói riêng tới quan hệ cơ sở, tối sẽ chỉ rõ "quan hệ cơ sở". Đừng vấp phải cái bẫy sơ đẳng rằng thuật ngữ *quan hệ* chỉ nghĩa là quan hệ cơ sở.

## Lưu ý

[^degree]: Nó cũng được sử dụng trong liên kết với khóa.
[^first-normal-form]:  "Thứ nhất" nghĩa là ta có thể định nghĩa các dạng chuẩn có mức cao hơn&mdash;second normal form, third normal form, vân vân&mdash;chúng liên quan đến nguyên tắc thiết kế cơ sở dữ liệu.