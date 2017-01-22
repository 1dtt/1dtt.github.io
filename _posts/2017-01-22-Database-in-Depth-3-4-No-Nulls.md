---
layout: post
title: 'Database in Depth 3.4: No Nulls'
---

Có nhiều lý do thực tiễn bảo vệ cho quan điểm nên loại bỏ null. Ở đây tôi muốn nhấn mạnh chỉ một luận điểm&mdash;nhưng tôi nghĩ rất chắc chắn. Tuy nhiên, luận điểm này dựa trên một số khái niệm tôi chưa bao giờ thảo luận, nên tôi cần giả sử bạn đã biết trước:

 * Tôi cho rằng bạn đã biết bất kỳ so sánh nào mà trong đó một hoặc cả hai toán hạng là null đều tạo ra một giá trị chân lý UNKNOWN chứ không phải TRUE hay FALSE. Do null là một *giá trị không xác định*: Nếu giá trị của `A` không xác định, thì hiển nhiên ta không thể xác định `A > B`, dù giá trị của `B` bằng bao nhiêu (kể cả&mdash;đặc biệt hơn&mdash;nếu giá trị của `B` cũng là không xác định). Tình cờ, đây cũng là nguồn gốc cho thuật ngữ *three-valued logic (3VL)*: khái niệm null dẫn tới một logic có ba giá trị chân lý thay vì hai. (Ngược lại, mô hình quan hệ, dựa trên *two*-valued logic, 2VL).

 * Tôi cũng cho rằng bạn biết bảng chân lý 3VL cho ba toán tử cơ bản NOT, AND, và OR (T = TRUE, F = FALSE, U = UNKNOWN):<br/>
   ![3VL Truth Table]({{ site.baseurl }}/images/2017_01_22_3VL_truth_table.png)
   NOT trả về UNKNOWN nếu đầu vào của nó là UNKNOWN; AND trả về UNKNOWN nếu một đầu vào là UNKNOWN và đầu vào còn lại là UNKNOWN hoặc TRUE; OR trả về UNKNOWN nếu một đầu vào là UNKNOWN và đầu vào còn lại là UNKNOWN hoặc FALSE.

Bây giờ tôi có thể trình bày luận điểm của mình. Điểm cơ bản tôi muốn chứng minh ở đây là một số biểu thức boolean&mdash;vì thế một số truy vấn&mdash;tạo ra kết quả đúng đối với three-valued logic nhưng *không* đúng trong thế giới thực. Ví dụ, xét cơ sở dữ liệu (không quan hệ) trong hình 3.2, trong đó "`CITY` là null" với part `P1`. Đặc biệt chú ý rằng bôi đen thay thế cho *hoàn toàn không có gì*&mdash;kể cả một chuỗi ký tự các khoảng trắng hay một chuỗi ký tự rỗng&mdash;trong vị trí thuộc tính (nghĩa là "tuple" cho `P1` không thực sự là một tuple).

<figure>
  <img src="{{ site.baseurl }}/images/2017_01_22_a_nonrelational_database_with_ a_null.png" alt="A nonrelational database, with a null" />
  <figcaption>
    FIGURE 3.2: A nonrelational database, with a null
  </figcaption>
</figure>

Xét truy vấn sau trên cơ sở dữ liệu này: "Lấy ra các cặp `(SNO, PNO)` mà hai thành phố `CITY` của supplier và part khác nhau hoặc thành phố của part không phải `Paris` (hoặc cả hai)." Dưới đây là công thức SQL cho truy vấn này:

```
SELECT S.SNO , P.PNO 
FROM   S , P 
WHERE  S.CITY <> P.CITY 
OR     P.CITY <> 'Paris' 
```

Hãy tập trung vào biểu thức boolean trong mệnh đề WHERE (thêm dấu ngoặc đơn để cho rõ ràng):

```
( S.CITY <> P.CITY ) OR ( P.CITY <> 'Paris' ) 
```

Chỉ với giá trị mẫu như hình 3.2, biểu thức này tạo ra UNKNOWN OR UNKNOWN, từ đó tạo ra UNKNOWN. Như ta đã biết, các truy vấn SQL lấy về dữ liệu sao cho thỏa mãn mệnh đề WHERE là TRUE, không phải FALSE, và không phải UNKNOWN; nên truy vấn trên không lấy về được một giá trị nào.

Nhưng đương nhiên, part `P1` phải có một thành phố *nào đó* trong thế giới thực; tức là "null `CITY`" cho part `P1` có đại diện cho một giá trị thực, giả sử gọi là `xyz`. Hiển nhiên, `xyz` có thể là `Paris` hoặc không. Nếu nó đúng là `Paris`, thì biểu thức:

```
( S.CITY <> P.CITY ) OR ( P.CITY <> 'Paris' ) 
```

trở thành (chỉ với giá trị mẫu đang có):

```
( 'London' <> 'Paris' ) OR ( 'Paris' <> 'Paris' )
```

Biểu thức này có kết quả là TRUE, do số hạng thứ nhất là TRUE. Ngược lại, nếu `xyz` không phải `Paris`, biểu thức trở thành (cũng chỉ với giá trị mẫu đang có):

```
( 'London' <> xyz) OR ( xyz <> 'Paris' )
```

Biểu thức này cũng có kết quả TRUE, do số hạng thứ hai là TRUE. Do đó, biểu thức boolean trên luôn luôn tạo ra kết quả TRUE trong thế giới thực, và câu truy vấn đáng ra phải có trả về giá trị `(S1, P1)`, *bất kể giá trị thực mà null đại diện có là gì*. Nói cách khác, kết quả đúng với logic (3VL) và kết quả đúng với thế giới thực là hai kết quả khác nhau.

Nói tóm lại: Nếu bạn có null trong cơ sở dữ liệu, bạn sẽ nhận được câu trả lời sai cho một số câu truy vấn. Hơn nữa, bạn không có cách nào nhận ra, câu truy vấn nào trả về kết quả sai; *tất cả* kết quả đều đáng ngờ. *Bạn không bao giờ có thể tin tưởng vào câu trả lời bạn nhận được từ một cơ sở dữ liệu có chứa null*. Với tôi, nhận định này rất đáng tán thưởng.

Giống như sự trùng lặp trong phần trước, có rất nhiều điều có thể nói về vấn đề null, nhưng tôi muốn kết thúc phần này chỉ với một số luận điểm *chính thức* chống lại null. Nhớ lại, theo định nghĩa, null không phải một giá trị. Một số luận điểm là:

 * Một "kiểu dữ liệu" chứa null không phải một kiểu dữ liệu (vì kiểu dữ liệu chứa giá trị).

 * Một "tuple" chứa null không phải một tuple (vì tuple chứa giá trị).

 * Một "quan hệ" chứa null không phải một quan hệ (vì quan hệ chứa tuple, và tuple không chứa null).

Cuối cùng, nếu có mặt null, chắc chắn chúng ta đang không nói chuyện về mô hình quan hệ (tôi không biết chúng ta đang nói về cái gì, nhưng nó không phải mô hình quan hệ).