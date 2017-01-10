---
layout: post
title: 'Database in Depth 1.2: Nhìn lại mô hình gốc'
---

Phần này là khởi điểm cho các cuộc thảo luận tiếp theo; chúng ta sẽ nhìn lại những khía cạnh cơ bản nhất của mô hình quan hệ khi nó lần đầu tiên được định nghĩa. Chú ý định tính "lần đầu tiên được định nghĩa"! Một nhầm lẫn phổ biến về mô hình quan hệ là nó hoàn toàn đứng yên. Điều này là sai. Mô hình quan hệ giống toán học ở chỗ: toán học không đứng yên mà thay đổi theo thời gian. Thực tế, mô hình quan hệ có thể được xem là một nhánh nhỏ của toán học; tức là, nó phát triển theo thời gian, khi có định lý mới được tìm ra hay được chứng minh. Ngoài ra, những đóng góp mới cho toán học có thể được thực hiện bởi bất kỳ ai, miễn là có đủ khả năng. Giống như thế, mô hình quan hệ ban đầu chỉ được tạo ra bởi một người, nhưng nó đã trở thành tài sản của cả thể giới và đang được phát triển bởi cộng đồng.

Người đàn ông đã nghĩ ra mô hình quan hệ có tên là E. F. Codd, lúc đó đang là một nhà nghiên cứu tại IBM. Cuối năm 1968, Codd, từng học toán tại Exeter College, Oxford, đã nhận ra rằng có thể sử dụng các quy tắc toán học để bơm một số nguyên lý chắc chắn, chặt chẽ vào lĩnh vực quản trị cơ sở dữ liệu, mà trước đó còn rất đơn sơ. Năm 1969, ông ấy lần đầu tiên công bố định nghĩa về mô hình quan hệ trong một Báo cáo Nghiên Cứu của IBM.

## Cấu trúc (structure)

Ban đầu mô hình quan hệ có ba thành phần chính&mdash;cấu trúc (structure), tính toàn vẹn (integrity) và thao tác (manipulation)&mdash;tôi sẽ lần lượt trình bày qua một cách ngắn gọn. Nhưng hãy chú ý, tất cả "định nghĩa" tôi đưa ra lúc này chỉ là chung chung; và tôi sẽ làm rõ chúng trong các chương sau.

Đầu tiên là cấu trúc. Cấu trúc, tất nhiên, chính là quan hệ, và như tất cả mọi người đều biết, nó thường được vẽ thành bảng trên sách vở (xem hình 1.1). Quan hệ được định nghĩa trên các *kiểu dữ liệu* (*type*) (còn được biết đến là *domain*); một kiểu dữ liệu về cơ bản là một tập hợp các giá trị mà từ đó một thuộc tính của một quan hệ nào đó lấy ra được giá trị của nó. Tham khảo cơ sở dữ liệu departments-and-employees trong hình 1.1, ví dụ có thể có một kiểu dữ liệu tên là `DNO` ("department numbers"), là một tập hợp mã bộ phận hợp lệ. Thuộc tính `DNO` trong quan hệ `DEPT` ("departments") và `DNO` trong quan hệ `EMP` ("employees") đều lấy giá trị từ tập hợp này. (Tên thuộc tính không nhất thiết phải trùng với tên kiểu dữ liệu, và thường là như thế. Sau này chúng ta sẽ gặp rất nhiều ví dụ.)

<figure>
  <img src="{{ site.baseurl }}/images/2017_01_02_The departments_and_employees_ database_sample_values.png" alt="departments-and-employees database" />
  <figcaption>
    FIGURE 1.1: departments-and-employees database&mdash;sample values
  </figcaption>
</figure>

Như tôi đã nói, các bảng như trong hình 1.1 biểu diễn các quan hệ: chính xác là các quan hệ *n* ngôi (*n*-ary relation). Một quan hệ *n* ngôi có thể được vẽ ra là một bảng với *n* cột; cột trong ảnh tương ứng với *thuộc tính* của quan hệ và hàng tương ứng với *tuple*. Giá trị *n* có thể là bất kỳ số nguyên không âm nào. Chúng ta có 1-ary relation gọi là *unary*, 2-ary relation gọi là *binary*, 3-ary relation gọi là *ternary*; vân vân.

Mô hình quan hệ cũng hỗ trợ các loại khóa (*key*). Tất cả quan hệ đều có ít nhất một khóa ứng cử (*candidate key*). Một khóa ứng cử là một định danh, xác định tính duy nhất&mdash;nghĩa là, nó là một kết hợp các thuộc tính, nhưng thông thường "kết hợp" này chỉ bao gồm một thuộc tính&mdash;mà tất cả tuple trong quan hệ có một giá trị duy nhất cho kết hợp này. Ví dụ, tất cả department chỉ có một department number duy nhất và tất cả employee chỉ có một employee number duy nhất, nên chúng ta có thể nói rằng `{DNO}` là khóa ứng cử cho `DEPT` và `{ENO}` là khóa ứng cử cho `EMP`. Chú ý dấu ngoặc nhọn: khóa ứng cử luôn luôn là kết hợp (*combination*), hay tập (*set*) các thuộc tính&mdash;cho dù tập hợp chỉ chứa một thuộc tính&mdash;và dấu ngoặc nhọn để gói một tập hợp.

Tiếp theo, một *khóa chính* (*primary key*) là một khóa ứng cử được lựa chọn từ các khóa ứng cử của quan hệ. Nếu một quan hệ chỉ có một khóa ứng cử thì hiển nhiên không có gì khác nhau nếu chúng ta nói nó là khóa chính. Nhưng nếu một quan hệ có hai hay nhiều khóa ứng cử, thì chúng ta thường phải chọn ra một trong số chúng làm khóa chính, nghĩa là khóa chính có "đặc quyền hơn các khóa khác". Giả sử tất cả employee có một `ENO` duy nhất và một `ENAME` duy nhất, thì cả `ENO` và `ENAME` đều cùng là khóa ứng cử cho `EMP`. Vậy thì chúng ta có thể chọn `ENO` làm khóa chính.

Để ý tôi đã nói rằng *thường* chọn ra một khóa chính. Nếu chỉ có một khóa ứng cử, thì chúng ta không phải chọn và cũng không có vấn đề gì. Nhưng nếu có hai hay nhiều, thì chọn ra một khóa ứng cử làm khóa chính có vẻ hơi tùy tiện (ít nhất là với tôi); chắc chắn sẽ có những trường hợp mà chúng ta dường như không có bất cứ lý do tốt nào để quyết định chọn lựa này. Trong cuốn sách này, tôi *sẽ* tuân theo khái niệm khóa chính&mdash;trong các hình như hình 1.1, tôi đánh dấu thuộc tính khóa chính bằng hai gạch dưới&mdash;nhưng tôi phải nhấn mạnh rằng khóa ứng cử, không phải khóa chính, mới thực sự có ý nghĩa từ góc độ tính quan hệ. Vì lý do đó, từ thời điểm này tôi sẽ sử dụng thuật ngữ *khóa*, không có định tính, để chỉ tới khóa ứng cử. (Nếu bạn lăn tăn, thì "ưu đãi đặc biệt" dành cho khóa chính hơn các khóa ứng cử khác chỉ có ý nghĩa về mặt cách nói, cách viết; nó không phải căn bản, và cũng không quá quan trọng).

Cuối cùng, một *khóa ngoại* (*foreign key*) là một tập hợp các thuộc tính trong một quan hệ mà các giá trị của tập hợp này phải khớp với các giá trị của một khóa ứng cử trong một quan hệ khác (hoặc cũng có thể là trong cùng quan hệ đó). Ví dụ trong hình 1.1, `{DNO}` là một khóa ngoại trong `EMP`, các giá trị của nó khớp với các giá trị của khóa ứng cử `{DNO}` trong `DEPT` (thể hiện bằng mũi tên trong hình). *Phải khớp*, nghĩa là, ví dụ,  nếu `EMP` chứa một tuple trong đó `DNO` có giá trị `D2`, thì `DEPT` cũng phải tồn tại một tuple mà trong đó `DNO` có giá trị `D2`; nếu không, tức là `EMP` đang chứa một employee nào đó thuộc về một department không tồn tại, và cơ sở dữ liệu này không phải là "một mô hình đúng trong thực tế".

## Tính toàn vẹn (Integrity)

Một *ràng buộc toàn vẹn* (*integrity constraint*) (gọi tắt là ràng buộc) về cơ bản chỉ là một biểu thức boolean mà kết quả phải là TRUE. Chúng ta có thể có một ràng buộc rằng các giá trị `SALARY` phải lớn hơn 0. Bất kỳ một cơ sở dữ liệu nào cũng sẽ có một số ràng buộc như thế, tuy nhiên những ràng buộc này chỉ áp dụng cho các quan hệ nằm trong cơ sở dữ liệu đó, nghĩa là áp dụng riêng cho cơ sở dữ liệu đó. Ngược lại, mô hình quan hệ (ít nhất là cái đầu tiên được tạo ra) bao gồm hai ràng buộc *tổng quát*&mdash;tổng quát nghĩa là áp dụng cho tất cả cơ sở dữ liệu. Trong đó có một ràng buộc liên quan đến khóa chính và một liên quan đến khóa ngoại:

 1. *Toàn vẹn thực thể* (*The entity integrity rule*): Thuộc tính khóa chính không được phép null.
 2. *Toàn vẹn tham chiếu* (*The referential integrity rule*): Không được phép có giá trị khóa ngoại không khớp.

Hãy để tôi giải thích ràng buộc thứ hai trước. Sử dụng cụm từ *giá trị khóa ngoại không khớp*, tôi muốn nói tới một giá trị khóa ngoại mà không bằng với bất kỳ giá trị đang tồn tại nào của khóa ứng cử tương ứng. Tức là, cơ sở dữ liệu departments-and-employees sẽ vi phạm ràng buộc referential integrity nếu nó chứa một tuple trong `EMP`, mà giả sử, có giá trị `DNO` bằng `D2`, nhưng lại không có tuple nào trong `DEPT` mà có `DNO` bằng `D2`. Do đó, ràng buộc referential integrity đơn giản chỉ là ý nghĩa của khóa ngoại; cái tên *referential integrity* xuất phát từ luận điểm bất kỳ giá trị khóa ngoại nào cũng có thể được coi như là *tham chiếu* tới tuple có cùng giá trị cho khóa ứng cử tương ứng. Thật vậy, quy tắc này có thể được phát biểu thành: "Nếu B tham chiếu tới A, thì A phải đang tồn tại."

Đối với ràng buộc toàn vẹn thực thể (entity integrity), tôi có một vấn đề. Tôi hoàn toàn loại bỏ khái niệm "null"; tức là, đối với tôi *không có chỗ cho null trong mô hình quan hệ*. (Hiển nhiên là Codd đã nghĩ khác, nhưng tôi có nhiều lý do vững chãi.) Vậy thì để giải thích cho quy tắc toàn vẹn thực thể, tạm thời tôi sẽ gác lại quan điểm của mình, nhưng làm ơn hãy hiểu rằng tôi sẽ trở lại toàn bộ vấn đề về null trong chương 3.

Về bản chất, null là một "đánh dấu", có ý nghĩa là *giá trị không xác định* (rất chú ý, null không phải là một giá trị; xin nhắc lại, nó một *đánh dấu* (*marker*), hay *cờ* (*flag*)). Giả sử chúng ta không biết lương của employee `E2`. Vậy thì, thay vì nhập vào một giá trị có thực `SALARY` nào đó trong tuple cho employee `E2` trong quan hệ `EMP`&mdash;thực ra là không thể, bởi vì theo định nghĩa, ta không biết giá trị đó bằng bao nhiêu&mdash;ta *đánh dấu* thành phần `SALARY` trong tuple là null:

![null]({{ site.baseurl }}/images/2017_01_02_null.png)

Bạn có thể thấy, tuple này chứa *không gì cả* cho thành phần `SALARY`. Trong cuốn sách này, tôi sẽ sử dụng bôi đen như trên để chỉ thành phần trống; bạn có thể hiểu ký hiệu này thay cho "đánh dấu" null.

Xét quan hệ `EMP`, quy tắc toàn vẹn thực thể phát biểu rằng, một cách chung chung, một employee có thể có tên (`ENAME`) không xác định, bộ phận (`DNO`) không xác định, hay lương (`SALARY`) không xác định, nhưng *không được phép* có mã (`ENO`) không xác định&mdash;bởi vì nếu mã không xác định, thì chúng ta không biết mình đang nói đến employee nào (tức là, "thực thể" nào).

Đây là tất cả những gì tối muốn nói về null vào lúc này. Hãy quên nó đi cho đến chương 3.

## Thao tác (manipulation)

Thành phần thao tác của mô hình bao gồm:

 * Một tập hợp các toán tử quan hệ, như difference (MINUS), gọi là *đại số quan hệ* (*relational algebra*), cùng với
 * Một toán tử *gán quan hệ*, cho phép kết quả của một biểu thức quan hệ, như `r MINUS s` (trong đó `r` và `s` là hai quan hệ), có thể được gán tới một quan hệ nào đó.

Toán tử gán quan hệ là cơ sở để các cập nhật (update) thực hiện được trong mô hình quan hệ,[^assignment-operator] tôi sẽ nói kỹ hơn trong phần "Relations Versus Relvars". Với đại số quan hệ, nó chứa một tập hợp các toán tử cho phép các quan hệ "mới" được tạo ra từ các quan hệ "cũ" (nói chung chung). Chính xác hơn, mỗi toán tử nhận vào ít nhất một quan hệ làm đầu vào và tạo ra một quan hệ khác là đầu ra; ví dụ, difference (MINUS) nhận vào hai quan hệ làm đầu vào và "trừ" quan hệ này cho quan hệ kia để tạo ra một quan hệ khác là đầu ra. Và điều quan trọng ở đây, đầu ra là một quan hệ khác: đây là tính chất *closure* nổi tiếng của đại số quan hệ. Tính chất closure cho phép chúng ta viết các *biểu thức quan hệ lồng nhau* (*nested relational expression*); bởi vì đầu ra của một toán tử là cùng loại với đầu vào, nên đầu ra của toán tử này có thể trở thành đầu vào của toán tử khác&mdash;nghĩa là, ví dụ ta có thể difference giữa `r` và `s`, rồi nạp kết quả làm đầu vào cho một union với một quan hệ `u` nào đó, rồi lại nạp kết quả mới này làm đầu vào cho một intersection với một quan hệ `v` khác, vân vân.

Lúc này, chúng ta có thể định nghĩa bao nhiêu toán tử tùy ý, miễn là nó thỏa mãn định nghĩa đơn giản "ít nhất một quan hệ đầu vào, đúng một quan hệ đầu ra". Trong danh sách sau tôi sẽ mô tả qua 
về bảy toán tử (thường được hiểu các toán tử gốc, được định nghĩa trong các bài báo đầu tiên của Codd); trong chương 5, tôi giới thiệu thêm một số toán tử khác và miêu tả chúng chi tiết hơn.

#### *Restrict*

Trả về một quan hệ chứa các tuple từ một quan hệ nào đó mà thỏa mãn một điều kiện nhất định. Ví dụ, chúng ta có thể giới hạn quan hệ `EMP` chỉ lấy ra các tuple có giá trị `DNO` bằng `D2`.

#### *Project*

Trả về một quan hệ chứa các (sub)tuple sót lại, sau khi loại bỏ một số thuộc tính trong một quan hệ. Ví dụ, ta có thể project quan hệ `EMP` trên hai thuộc tính `ENO` và `SALARY` (bỏ đi `ENAME` và `DNO`).

#### *Product*

Trả về một quan hệ chứa các tuple, mỗi tuple là kết hợp của hai tuple từ hai quan hệ đầu vào. Toán tử này còn được biết đến là *cartesian product* (tích Đề-các), *cross product*, *cross join*, và *cartesian join*; thực tế, nó chỉ là một trường hợp đặc biệt của join.

<figure>
  <img src="{{ site.baseurl }}/images/2017_01_02_original_relational_algebra.png" alt="original relational algebra" />
  <figcaption>
    FIGURE 1.2: The original relational algebra
  </figcaption>
</figure>

#### *Intersect*

Trả về một quan hệ chứa các tuple xuất hiện trong cả hai quan hệ. (Thực ra, intersect cũng là một trường hợp đặc biệt của join.)

#### *Union*

Trả về một quan hệ chứa các tuple mà xuất hiện trong hoặc quan hệ này hay quan hệ kia, hoặc cả hai.

#### *Difference*

Trả về một quan hệ chứa các tuple chỉ xuất hiện trong quan hệ thứ nhất, nhưng không xuất hiện trong quan hệ thứ hai.

#### *Join*

Trả về một quan hệ chứa các tuple, mỗi tuple là kết hợp của hai tuple từ hai quan hệ đầu và hai tuple này có cùng giá trị của thuộc tính chung (giá trị chung chỉ xuất hiện một, không phải hai lần trong tuple kết quả). *Chú ý:* Loại join này ban đầu được gọi là *natural* join. Vì là loại quan trọng nhất, nên ta có quy ước khi dùng thuật ngữ *join* mà không có định tính thì có nghĩa là natural join.

Còn một điểm cuối cùng để kết thúc phần này: có lẽ bạn cũng biết, có một thứ gọi là *giải tích quan hệ* (*relational calculus*). Giải tích quan hệ có thể được xem như là một lựa chọn khác thay cho đại số quan hệ; tức là, thay vì nói rằng, phần thao tác bao gồm đại số quan hệ (cộng với một phép gán quan hệ), ta có thể nói nó bao gồm giải tích quan hệ (cộng với một phép gán quan hệ). Hai thứ này đương nhau và đổi chỗ được cho nhau, theo ý nghĩa, với mỗi biểu thức đại số thì có một biểu thức giải tích tương đương về logic, và ngược lại.

## Ví dụ

Đây là một cơ sở dữ liệu ví dụ mà tôi sẽ sử dụng trong hầu hết, nếu như không muốn nói là tất cả các phần thảo luận sau này: cơ sở dữ liệu nổi tiếng suppliers-and-parts. Chúng ta có một số định nghĩa:

#### *Suppliers*

Quan hệ `S` biểu diễn nhà cung cấp (supplier), nói đúng hơn là liên hệ của supplier. Mỗi supplier có một mã số `SNO`, là duy nhất với supplier đó (nên `{SNO}` là khóa chính); một tên `SNAME`, không nhất thiết phải là duy nhất (mặc dù các giá trị `SNAME` xuất hiện trong hình 1.3 đều là duy nhất); một giá trị chỉ số hay trạng thái `STATUS`; và một vị trí `CITY`.

#### *Parts*

Quan hệ `P` biểu diễn bộ phận (part), chính xác hơn là loại part. Mỗi part có một mã số `PNO` duy nhất (`{PNO}` là khóa chính); một tên `PNAME`, một màu `COLOR`; một cân nặng `WEIGHT`; và một vị trí `CITY` chỉ nơi part được lưu trữ.

#### *Shipments*

Quan hệ `SP` biểu diễn sự vận chuyển (chỉ ra part nào được cung cấp bởi supplier nào). Mỗi shipment có một mã nhà cung cấp `SNO`, một mã bộ phận `PNO`, và một số lượng `QTY`. Để cho dễ hiểu, tôi giả sử chỉ có một shipment tại một thời điểm cho một supplier và một part (nên `{SNO,PNO}` là khóa chính; `{SNO}` và `{PNO}` là hai khóa ngoại tương ứng với hai khóa chính của `S` và `P`). Chú ý trong hình 1.3 có một supplier `S5` không hề có một shipment nào (chưa bán gì cả).

<figure>
  <img src="{{ site.baseurl }}/images/2017_01_02_suppliers_and_parts_database_sample_values.png" alt="suppliers and parts database sample values" />
  <figcaption>
    FIGURE 1.3: The suppliers-and-parts database&mdash;sample values
  </figcaption>
</figure>

## Lưu ý

[^assignment-operator]: Quy ước trong toàn bộ cuốn sách này là sử dụng thuật ngữ tổng quát "cập nhập" ("update") để chỉ các toán tử INSERT, DELETE, và UPDATE (và gán). Khi tôi muốn chỉ đúng tới toán tử UPDATE, tôi sẽ viết hoa.