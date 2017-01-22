---
layout: post
title: 'Database in Depth 3.4: No Nulls'
---

Có nhiều lý do thực tiễn bảo vệ cho quan điểm nên loại bỏ null. Ở đây tôi muốn nhấn mạnh chỉ một luận điểm&mdash;nhưng tôi nghĩ rất chắc chắn. Tuy nhiên, luận điểm này dựa trên một số khái niệm tôi chưa bao giờ thảo luận, nên tôi cần giả sử bạn đã biết trước:

 * Tôi cho rằng bạn đã biết bất kỳ so sánh nào mà trong đó một hoặc cả hai toán hạng là null đều tạo ra một giá trị chân lý UNKNOWN chứ không phải TRUE hay FALSE. Do null là một *giá trị không xác định*: Nếu giá trị của `A` không xác định, thì hiển nhiên ta không thể xác định `A > B`, dù giá trị của `B` bằng bao nhiêu (kể cả&mdash;đặc biệt hơn&mdash;nếu giá trị của `B` cũng là không xác định). Tình cờ, đây cũng là nguồn gốc cho thuật ngữ *three-valued logic (3VL)*: khái niệm null dẫn tới một logic có ba giá trị chân lý thay vì hai. (Ngược lại, mô hình quan hệ, dựa trên *two*-valued logic, 2VL).
 * Tôi cũng cho rằng bạn biết bảng chân lý 3VL cho ba toán tử cơ bản NOT, AND, và OR (T = TRUE, F = FALSE, U = UNKNOWN):<br/>
   ![3VL Truth Table]({{ site.baseurl }}/images/2017_01_22_3VL_truth_table.png)
   NOT trả về UNKNOWN nếu đầu vào của nó là UNKNOWN; AND trả về UNKNOWN nếu một đầu vào là UNKNOWN và đầu vào còn lại là UNKNOWN hoặc TRUE; OR trả về UNKNOWN nếu một đầu vào là UNKNOWN và đầu vào còn lại là UNKNOWN hoặc FALSE.

Bây giờ tôi có thể trình luận điểm của mình.