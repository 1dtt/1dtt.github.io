---
layout: post
title: 'SQL and Relational Theory: Nhìn lại mô hình gốc'
---

Mục đích của phần này là điểm bắt đầu cho các cuộc thảo luận tiếp theo; ta sẽ xem xét một số khía cạnh cơ bản nhất của mô hình quan hệ khi lần đầu được định nghĩa. Chú ý cụm từ &mdash; "lần đầu được định nghĩa"! Rất nhiều người đã nhận thức sai rằng: mô hình quan hệ là một thứ hoàn toàn không biến đổi. Không phải như vậy. Nó giống như toán học: Toán học không phải là một thứ tĩnh, mà biến đổi theo thời gian. Thực tế, mô hình quan hệ có thể nói chính là một phần của toán học; tức là, nó phát triển theo thời gian &mdash; mỗi khi có một định lý mới được tìm ra hay được chứng minh. Hơn nữa, những đóng góp mới có thể được tạo ra bởi bất cứ ai, miễn là có đủ khả năng. Giống như các nhánh khác của toán học, mô hình quan hệ, mặc dù được ban đầu chỉ được phát minh ra bởi một người, nhưng nó đã trở thành tài sản của cả thể giới và được phát triển bởi cả cộng đồng.

Nhân tiện, thì người đó là E. F. Codd, lúc đó là một nhà nghiên cứu tại IBM (E viết tắt cho Edgar và F là Frank &mdash; nhưng ông ấy luôn ký tên bằng các chữ cái đầu; với bạn bè mình, trong đó có tác giả, ông ấy là Ted). Vào cuối năm 1968, Codd là một nhà toán học, đã nhận ra rằng có thể sử dụng các quy tắc toán học để bơm một số nguyên lý chắc chắn và chặt chẽ vào một lĩnh vực, quản trị cơ sở dữ liệu, mà trước đó đang còn rất đơn sơ. Định nghĩa đầu tiên về mô hình quan hệ của ông ấy xuất hiện trong một Báo cáo Nghiên Cứu IBM vào năm 1969.

## Cấu trúc

Mô hình ban đầu có ba thành phần chính &mdash; *structure*,  *integrity*, và *manipulation* &mdash; sẽ được trình bày lần lượt. Chú ý là, tất cả "định nghĩa" được đưa ra trong phần này (và hai phần sau nữa) chỉ là chung chung; sẽ được trình bày rõ hơn trong các chương sau.

Đầu tiên là structure (cấu trúc). Cấu trúc, chính là quan hệ (*relation*), và như tất cả mọi người đều biết, nó thường được vẽ ra trên sách vở, thành bảng.

<figure>
  <img src="{{ site.baseurl }}/images/post2_The departments_and_employees_ database_sample_values.png" alt="departments-and-employees database" />
  <figcaption>
    H1.1: departments-and-employees database
  </figcaption>
</figure>

Ví dụ cơ sở dữ liệu bộ-phận-và-nhân-viên trong hình 1.1 có hai quan hệ `DEPT` và `EMP`. Một quan hệ được định nghĩa dựa trên nhiều *type* (kiểu dữ liệu), hay còn gọi là *domain* (miền giá trị); một kiểu dữ liệu về cơ bản là một tập giá trị; mà từ tập đó, ta có thể lấy ra giá trị để gán cho thuộc tính. Ví dụ, có thể coi có một kiểu dữ liệu tên là `DNO` ("department numbers"), là tập hợp tất cả mã bộ phận hiện có. Thuộc tính `DNO` trong quan hệ `DEPT` ("departments") và `DNO` trong quan hệ `EMP` ("employees") đều lấy giá trị từ miền `DNO` này.

Như đã nói, hình 1.1 biểu diễn hai quan hệ, chính xác là hai [Quan hệ *n* ngôi](#quan-h-n-ngi). Một quan hệ *n* ngôi có thể được vẽ ra là một bảng với *n* cột; một cột biểu diễn một thuộc tính (*attribute*) của quan hệ; và một hàng biểu diễn một *tuple*. *n* có thể là bất kỳ số nguyên không âm nào. Một quan hệ 1 ngôi có tên là *unary*, 2 ngôi là *binary*; 3 ngôi là *ternary*...

Mô hình quan hệ cũng hỗ trợ các loại khóa (*key*). Có một điểm cốt yếu &mdash; tất cả quan hệ đều có ít nhất một khóa ứng cử *candidate key*[^candidate-key]. Một khóa ứng cử là một tên; tức là, kết hợp nhiều thuộc tính &mdash; nhưng không phải lúc nào cũng là sự "kết hợp", đôi khi chỉ là một thuộc tính đơn lẻ &mdash; mà từ đó, xác định được tính duy nhất của tất cả *tuple*. Ví dụ, mỗi bộ phận đều có một mã bộ phận duy nhất và mỗi nhân viên đều có một mã nhân viên duy nhất, nên ta có thể nói `{DNO}` là một khóa ứng cử cho `DEPT` và `{ENO}` là một khóa ứng cử cho `EMP`. Chú ý dấu ngoặc nhọn; xin nhắc lại, khóa ứng cử luôn luôn là kết hợp, hay tập hợp (*set*) các thuộc tính (kể cả khi tập hợp chỉ chứa một thuộc tính), và ký hiệu của tập hợp trong sách vở là một [*commalist*](#commalist) đóng bên trong hai ngoặc nhọn.

Tiếp theo, một khóa chính (*primary key*) là một khóa ứng cử được chọn lựa theo cách nào đó. Nếu quan hệ chỉ có một khóa ứng cử thì ta chọn luôn nó làm khóa chính. Nhưng nếu quan hệ có nhiều khóa ứng cử, thì ta thường chọn khóa chính dựa trên "nó có điểm hơn các ứng viên khác". Giả sử, mỗi bộ phận đều có một mã bộ phận duy nhất, và một tên bộ phận duy nhất &mdash; tuy không thực tế &mdash; vì thế `{DNO}` và `{DNAME}` là hai khóa ứng cử cho `DEPT`. Thì ta có thể chọn `{DNO}` làm khóa chính.

Để ý là cách nói: *thường* chọn. Nếu chỉ có một khóa ứng cử, nghĩa là ta không có sự lựa chọn nào, nên không phải suy nghĩ nhiều; nhưng nếu có nhiều khóa ứng cử, thì việc chọn ra một khóa làm khóa chính có vẻ hơi tùy tiện. Chắc chắn sẽ có lúc ta cũng không có lý do thực sự tốt cho việc lựa chọn này. Vì thế, thường thì mọi người sẽ tuân theo nguyên tắc khóa chính &mdash; và trong hình 1.1, khóa chính được ký hiệu bằng hai dấu gạch dưới &mdash; nhưng điều thực sự muốn nhấn mạnh ở đây là, khóa ứng cử, chứ không phải khóa chính mới có ý nghĩa đối với tính chất quan hệ. Vì lý do đó, từ lúc này tác giả sẽ sử dụng từ *khóa* (đứng độc lập), để chỉ tới khóa ứng cử, cho dù khóa ứng cử này được thiết kế làm khóa chính. (Khi nhắc đến khóa, nó có nghĩa là khóa ứng cử; khóa chính sẽ được nói rõ ra là khóa chính.) (Đối đãi đặc biệt dành cho khóa chính hơn các khóa ứng cử khác chỉ có ý nghĩa văn vẻ, nó không thực tế, và cũng không quá quan trọng.) 

Cuối cùng, một khóa ngoại (*foreign key*) trong một quan hệ *r2* nào đó là kết hợp, hay tập hợp, các thuộc tính *FK*, mà mỗi giá trị *FK* phải bằng với một giá trị nào đó của khóa *K* trong một quan hệ *r1* nào đó (*r1* và *r2* không nhất thiết phải khác nhau). Ví dụ, quay lại hình 1.1, `{DNO}` là một khóa ngoại trong `EMP`, các giá trị của khóa ngoại này phải khớp với các giá trị của khóa `{DNO}` trong `DEPT`. *Phải khớp* ở đây, nghĩa là, ví dụ `EMP` chứa một tuple trong đó thuộc tính `DNO` bằng `D2`, thì `DEPT` cũng phải tồn tại một tuple mà trong đó `DNO` bằng `D2` &mdash; nếu không, `EMP` đang chứa một nhân viên thuộc về một bộ phận không có thực, và cơ sở dữ liệu này không phải là "một mô hình thật trong thực tế".

## Yêu cầu, ràng buộc về tính toàn vẹn

Một ràng buộc tính toàn vẹn (*integrity constraint*) về cơ bản chỉ là một biểu thức boolean mà kết quả phải bằng TRUE. Ví dụ, chúng ta có một ràng buộc như sau, giá trị `SALARY` phải lớn hơn không. Bất kỳ một cơ sở dữ liệu nào cũng chịu nhiều ràng buộc; tuy nhiên chỉ là riêng cho cơ sở dữ liệu đó, vì thế ràng buộc sẽ được diễn giải theo các quan hệ nằm trong cơ sở dữ liệu. Ngược lại với đó, mô hình quan hệ ban đầu được xây dựng gồm hai ràng buộc *tổng quát* &mdash; tổng quát, nghĩa là áp dụng với tất cả cơ sở dữ liệu nói chung. Một ràng buộc liên quan đến khóa chính, và một liên quan đến khóa ngoại:

 1. *The entity integrity rule:* Thuộc tính khóa chính không được phép null.
 2. *The referential integrity rule:* Không được phép có giá trị khóa ngoại không khớp.

Ta sẽ giải thích quy tắc thứ hai trước. Cụm từ *giá trị khóa ngoại không khớp*, nghĩa là một giá trị khóa ngoại mà không bằng với bất kỳ giá trị đang tồn tại nào của khóa ứng cử tương ứng. Ví dụ, sẽ vi phạm quy tắc *referential integrity* nếu `EMP` có một tuple trong đó `DNO` có giá trị `D2`, nhưng không có tuple nào của `DEPT` mà trong đó `DNO` bằng `D2`. Ta có thể thấy rằng quy tắc referential integrity đơn giản chính là định nghĩa của khóa ngoại; cái tên "referential integrity" kế thừa từ luận điểm một giá trị khóa ngoại có thể được xem là *tham chiếu* tới tuple có chứa khóa cùng giá trị. Thực vậy, quy tắc này có thể được phát biểu thành: Nếu *B* tham chiếu tới *A*, thì *A* phải đang tồn tại.

Đối với quy tắc *entity integrity*, ở đây chúng ta có một vấn đề. Tác giả loại bỏ hoàn toàn khái niệm "null"; ông ấy có một quan điểm rất kiên cố rằng "*không có chỗ cho null trong mô hình quan hệ.*" (Codd thì nghĩ khác, đương nhiên, nhưng tác giả cũng có những lý do rất chắc chắn của mình.) Vì thế, để giải thích cho quy tắc entity integrity, tạm thời tác giả sẽ gác bỏ ý kiến...

Vậy thì, về bản chất, một null là một con dấu, đánh dấu *giá trị chưa biết*. Và, cực kỳ quan trọng, bản thân nó không phải là một giá trị; xin nhắc lại, nó là một *con dấu*, hay *cờ*. Ví dụ, giả sử chúng ta không biết lương của nhân viên `E2`. Vậy thì, thay vì nhập một giá trị `SALARY` cho nhân viên `E2` &mdash; điều không thể, vì không biết giá trị thực sự là bao nhiêu &mdash; ta *đánh dấu* thành phần `SALARY` của tuple là null, giống như sau:

![null]({{ site.baseurl }}/images/post2_null.png)

Cần rất chú ý rằng tuple này chứa *không gì cả* trong thành phần `SALARY`. Nhưng rất khó để vẽ không gì cả! Trong ảnh này, ta bôi đen để ám chỉ thành phần `SALARY` là rỗng, nhưng sẽ chính xác hơn nhiều nếu hoàn toàn không vẽ thành phần này ra (hoặc không vẽ ô giá trị của thành phần này). Nhưng thôi cứ giữ nguyên như thế, ta sẽ sử dụng ký hiệu này thay cho *không gì cả* ở tất cả mọi nơi trong sách &mdash; nhưng, xin nhắc lại, một bôi đen như thế không thể hiểu thành bất kỳ giá trị nào, nó là *hoàn toàn không có giá trị nào cả*. Hãy nghĩ bôi đen là thay thế cho con dấu null, hay cờ null.

Quay trở lại quy tắc entity integrity. Xét quan hệ `EMP`, quy tắc này phát biểu rằng, một cách không chắc chắn, với một tuple cho trước, có thể có tên chưa biết, hay mã bộ phận chưa biết, hay lương chưa biết, nhưng không thể có một mã nhân viên chưa biết. Lời giải thích cho điều này là nếu như không biết mã nhân viên, ta hoàn toàn không biết mình đang nói đến "thực thể" (nhân viên) nào.

Đây là tất cả những điều mà tác giả muốn nói đến null. Hãy quên nó đi cho đến khi được đề cập lại.

## Thao tác

Phần thao tác của mô hình lại tiếp tục được chia ra làm hai phần:

 * *Đại số quan hệ* (*relational algebra*), là tập hợp của các toán tử (ví dụ *difference*, hay *MINUS*), mà có thể áp dụng tới các quan hệ.
 * Một toán tử *gán quan hệ* (*relational assignment*), cho phép giá trị của một biểu thức đại số quan hệ (ví dụ *r1* MINUS *r2*, trong đó *r1* và *r2* là hai quan hệ) có thể được gán tới một quan hệ nào đó.

Toán tử gán về cơ bản là cách các cập nhật (*update*) được thực hiện trong mô hình quan hệ, xem thêm phần "Relations vs. Relvars". *Chú ý:* Thuật ngữ tổng quát *cập nhật* được sử dụng để chỉ chung chung tới các toán tử INSERT, DELETE, và UPDATE (và gán). Khi muốn nói chính xác tới toán tử UPDATE, ta sẽ dùng từ UPDATE viết hoa tất cả giống như thế này.

Về đại số quan hệ, nó là một tập hợp các toán tử &mdash; nói một cách không chặt chẽ &mdash; cho phép chúng ta tạo ra các quan hệ "mới" từ các quan hệ "cũ". Một toán tử nhận vào một hay nhiều quan hệ và tạo ra một quan hệ khác; ví dụ, *difference* (MINUS) nhận vào hai quan hệ và lấy quan hệ này "trừ" quan hệ kia, từ đó tạo ra một quan hệ khác. Điều quan trọng ở đây là tạo ra một quan hệ khác: Đây là tính chất *closure* nổi tiếng của đại số quan hệ. Tính chất closure là cơ sở để chúng ta viết các biểu thức quan hệ lồng nhau; bởi vì đầu ra của một toán tử có thể trở thành đầu vào cho một toán tử khác. Ví dụ, ta có thể lấy hiệu của *r1* MINUS *r2*, nạp hiệu này cho một *union* với *r3*, rồi lại lấy kết quả đó, nạp cho một *intersection* với *r4*, vân vân.

Số lượng của tập hợp toán tử là vô định, chỉ cần các toán tử "nhận vào một hay nhiều quan hệ, và tạo ra đúng một quan hệ". Chúng ta sẽ cùng xem qua một số toán tử được định nghĩa từ những ngày đầu tiên (bởi Codd).

#### *Restrict*

Trả về một quan hệ chứa các tuple từ một quan hệ nào đó mà thỏa mãn một điều kiện nhất định. Ví dụ, ta có thể giới hạn quan hệ `EMP` chỉ lấy ra các tuple có giá trị `DNO` bằng `D2`.

#### *Project*

Trả về một quan hệ chứa các tuple (con) thừa lại, sau khi loại bỏ một số thuộc tính trong một quan hệ. Ví dụ, ta có thể project quan hệ `EMP` để chỉ lấy ra hai thuộc tính `ENO` và `SALARY` (bỏ đi `ENAME` và `DNO`).

#### *Product*

Trả về một quan hệ chứa các tuple, mỗi tuple là kết hợp của hai tuple từ hai quan hệ đầu vào. *Chú ý:* Toán tử này còn được biết đến với các tên khác như *cartesian product* (tích Đề-các), *cross product*, *cross join*, và *cartesian join*; thực tế, nó chỉ là một trường hợp đặc biệt của join.

<figure>
  <img src="{{ site.baseurl }}/images/post2_relational_algebra.png" alt="relational algebra" />
  <figcaption>
    H1.2: Đại số quan hệ thủa ban đầu
  </figcaption>
</figure>

#### *Union*

Trả về một quan hệ chứa các tuple mà xuất hiện trong một hoặc cả hai quan hệ.

#### *Intersect*

Trả về một quan hệ chứa các tuple xuất hiện trong cả hai quan hệ. (Đây cũng là một trường hợp đặc biệt của join.)

#### *Difference*

Trả về một quan hệ chứa các tuple xuất hiện trong quan hệ đầu tiên, nhưng không xuất hiện trong quan hệ thứ hai.

#### *Join*

Trả về một quan hệ chứa các tuple, mỗi tuple là kết hợp của hai tuple từ hai quan hệ đầu vào. Nếu hai tuple có chung giá trị đối với các thuộc tính chung thì chúng sẽ tạo ra một tuple mới (giá trị chung này chỉ xuất hiện một lần trong tuple mới). *Chú ý:* Loại join này ban đầu được gọi là *natural* join, để phân biệt với các loại khác (sẽ được thảo luận trong các phần sau). Bởi vì natural join là loại join quan trọng hơn hẳn, nên quy ước khi nhắc đến *join* (đứng độc lập), nó có nghĩa là natural join.

Còn một điểm cuối cùng để kết thúc phần này. Có một thứ gọi là giải tích quan hệ (*relational calculus*). Giải tích quan hệ có thể được xem như là một lựa chọn khác thay cho đại số quan hệ; tức là, thay vì nói rằng, phần Thao tác bao gồm đại số quan hệ (cộng với một phép gán), ta có thể nói nó bao gồm giải tích quan hệ (cộng với một phép gán). Hai thứ này đương nhau và có thể đổi chỗ cho nhau, theo ý nghĩa, với mỗi biểu thức đại số thì ta có một biểu thức giải tích tương đương về logic, và ngược lại. Giải tích sẽ được nói thêm trong chương 10.

## Ví dụ

<figure>
  <img src="{{ site.baseurl }}/images/post2_suppliers_and_parts_database_sample_values.png" alt="suppliers and parts database sample values" />
  <figcaption>
    H1.3: Cơ sở dữ liệu nhà cung cấp (supplier) và bộ phận của thiết bị (part) &mdash; giá trị mẫu
  </figcaption>
</figure>

Hình 1.3 vẽ ra giá trị mẫu cho một cơ sở dữ liệu mà sẽ được dùng cho hầu hết, nếu như không muốn nói là tất cả các thảo luận sau này: cơ sở dữ liệu nhà-cung-cấp-và-bộ-phận. Một số định nghĩa:

#### *Supplier*

Quan hệ `S` biểu diễn nhà cung cấp (Supplier), nói đúng hơn là liên hệ của nhà cung cấp. Mỗi nhà cung cấp có một mã số `SNO`, dùng để phân biệt với nhà cung cấp khác (có hai dấu gạch dưới như trong hình, `{SNO}` là khóa chính); một tên `SNAME`, có thể trùng với nhà cung cấp khác (mặc dù các giá trị trong ví dụ đều khác nhau); một giá trị trạng thái `STATUS`, biểu diễn một loại xếp hạng hay mức độ yêu thích nào đó; và một vị trí `CITY`.

#### *Part*

Quan hệ `P` biểu diễn bộ phận, chính xác hơn là loại bộ phận. Mỗi loại bộ phận có một mã số `PNO` duy nhất (`{PNO}` là khóa chính); một tên `PNAME`, một màu `COLOR`; một cân nặng `WEIGHT`; và một vị trí `CITY` chỉ nơi bộ phận được lưu trữ.

#### *Shipment*

Quan hệ `SP` biểu diễn sự mua bán (nó chỉ ra bộ phận nào được bán bởi nhà cung cấp nào). Mỗi một sự mua bán có một mã nhà cung cấp `SNO`, một mã bộ phận `PNO`, và một số lượng `QTY`. Để cho dễ hiểu, ta giả sử mỗi lần mua bán tại một thời điểm chỉ có một nhà cung cấp và một bộ phận (`{SNO,PNO}` là khóa chính; `{SNO}` và `{PNO}` là hai khóa ngoại tương ứng với hai khóa chính của `S` và `P`). Chú ý là trong hình 1.5 có một nhà cung cấp `S5` không hề bán một bộ phận nào.

## Các khái niệm

#### Quan hệ *n* ngôi

Tiếng Anh là *n-ary relation*.

#### Commalist

Commalist có thể được định nghĩa như sau: Giả sử *xyz* là một loại gì đó (ví dụ "tên thuộc tính"). Thì *xyz commalist* biểu diễn một chuỗi chứa không hoặc nhiều *xyz*, hai *xyz* cạnh nhau được ngăn cách bởi dấu phẩy (tất cả khoảng trống xuất hiện ngay trước và sau dấu phẩy bị bỏ qua). Ví dụ, nếu `A`, `B`, và `C` là tên thuộc tính (*attribute name*), thì tất cả chuỗi dưới đây đều là *attribute name commalist*:  
`A , B , C`  
`C , B , A`  
`B`  
`A , C`

Một chuỗi trống cũng được tính.

Ngoài ra, khi một commalist đóng trong hai dấu ngoặc nhọn, nó biểu diễn một tập hợp, và (a) các khoảng trắng xuất hiện ngay sau dấu ngoặc nhọn mở và ngay trước dấu ngoặc nhọn đóng bị bỏ qua, (b) thứ tự các phần tử bên trong commalist bị bỏ qua (vì tập hợp không tính thứ tự giữa các phần tử), và (c) nếu một phần tử xuất hiện nhiều hơn một lần, ta coi như chỉ một lần (vì tập hợp không chứa phần tử trùng nhau).

## Lưu ý

[^candidate-key]: Nói một cách chặt chẽ, câu này nên đọc thành "Tất cả *relvar* đều có ít nhất một khóa ứng viên." (xem mục "Relations vs. Relvars").
