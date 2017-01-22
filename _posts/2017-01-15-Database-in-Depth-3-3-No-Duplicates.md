---
layout: post
title: 'Database in Depth 3.3: No Duplicates'
---

Trong chương trước, tôi đã nói:

 * Quan hệ không bao giờ chứa các tuple trùng nhau, vì body của chúng là một tập hợp (tập hợp các tuple) và tập hợp trong toán học không chứa các phần tử trùng nhau.

 * Quan hệ không bao giờ chứa null, vì body của quan hệ là một tập hợp các tuple, và tuple không bao giờ chứa null.

Như vậy, chủ đề này và chủ đề về null sẽ là hai chủ đề của SQL, không phải mô hình quan hệ; nên tôi sẽ sử dụng các thuật ngữ của SQL.

## Trùng có gì sai?

Có nhiều lý do thực tiễn bảo vệ cho quan điểm nên loại bỏ hàng trùng lặp. Ở đây tôi muốn nhấn mạnh chỉ một luận điểm&mdash;nhưng tôi nghĩ rất chắc chắn. Tuy nhiên, luận điểm này dựa trên một số khái niệm tôi chưa bao giờ thảo luận, nên tôi cần giả sử bạn đã biết trước:

 * Tôi cho rằng bạn đã biết các hệ thống DBMS quan hệ có chứa một thành phần tên là *optimizer*, có nhiệm vụ tìm ra cách tốt nhất để thực thi các truy vấn (tốt nhất ở đây nghĩa là *hiệu năng tốt nhất*).

 * Tôi cũng cho rằng bạn đã biết optimizer có thực hiện một công việc gọi là *sửa truy vấn (query rewrite)*. Query rewrite là một quá trình biến đổi một biểu thức quan hệ `exp1` thành một biểu thức khác `exp2`&mdash;*expression transformation*&mdash;mà đảm bảo tạo ra cùng kết quả nhưng việc tính toán `exp2` được thực hiện nhanh hơn.

Sau giả sử, bây giờ tôi có thể trình bày luận điểm của mình. Điểm cơ bản tôi muốn chứng minh ở đây là một số biến đổi biểu thức, tức là một số sự tối ưu (optimization), có thể hợp lệ nếu SQL thực sự có tính quan hệ&mdash;nghĩa là không chứa hàng trùng lặp, nhưng lại không hợp lệ nếu tồn tại hàng trùng lặp. Dưới đây là cơ sở dữ liệu có chứa hàng trùng lặp (không có tính quan hệ) tôi sử dụng để chứng minh rằng có một số biến đổi biểu thức không hợp lệ (chú ý hai bảng đều không có khóa):

<figure>
  <img src="{{ site.baseurl }}/images/2017_01_15_a nonrelational_database_with_ duplicates.png" alt="A nonrelational database, with duplicates" />
  <figcaption>
    FIGURE 3.1: A nonrelational database, with duplicates
  </figcaption>
</figure>

Trước khi tiếp tục, có lẽ tôi nên hỏi một câu: có ý gì khi có ba hàng `<P1, Screw>` mà không phải hai, bốn, hay mười bảy? Nó phải có một ý nghĩa nào đó, vì nếu không, thì tại sao nó lại ở đấy. Tôi từng một lần nghe Ted Codd nói rằng, "Nếu một điều đúng, phát biểu nó hai lần không làm cho nó đúng hơn".

Nên tôi phải phỏng đoán có một ý nghĩa nào đó đi kèm với hàng trùng lặp, mặc dù ý nghĩa này rất khó nhận ra. Tức là, ý nghĩa của hàng trùng nhau có lẽ được quyết định bởi nghiệp vụ của bài toán, như là có ba hàng `<P1, Screw>` chứ không phải hai, bốn, hay mười bảy.

Bây giờ, ta có một truy vấn sau: "Lấy ra các part numbers của các parts hoặc là có tên `Screw` hoặc được cung cấp bởi supplier `S1`, hoặc cả hai". Dưới đây là một số công thức SQL cho truy vấn này, đầu ra nằm dưới mỗi công thức:

 1. 

    <pre class="highlight"><code>SELECT P.PNO
    FROM   P
    WHERE  P.PNAME = 'Screw'
    OR     P.PNO IN
         ( SELECT SP.PNO
           FROM SP
           WHERE SP.SNO = 'S1' )</code></pre>

    Kết quả: `P1` * 3, `P2` * 1.

 2. 

    <pre class="highlight"><code>SELECT SP.PNO
    FROM   SP
    WHERE  SP.SNO = 'S1'
    OR     SP.PNO IN
         ( SELECT P.PNO
           FROM P
           WHERE P.PNAME = 'Screw' )</code></pre>

    Kết quả: `P1` * 2, `P2` * 1.

 3. 

    <pre class="highlight"><code>SELECT P.PNO
    FROM   P , SP
    WHERE  ( SP.SNO = 'S1' AND
             SP.PNO = P.PNO )
    OR     P.PNAME = 'Screw'</code></pre>

    Kết quả: `P1` * 9, `P2` * 3.

 4. 

    <pre class="highlight"><code>SELECT SP.PNO
    FROM   P , SP
    WHERE  ( SP.SNO = 'S1' AND
             SP.PNO = P.PNO )
    OR     P.PNAME = 'Screw'</code></pre>

    Kết quả: `P1` * 8, `P2` * 4.

 5. 

    <pre class="highlight"><code>SELECT P.PNO
    FROM   P
    WHERE  P.PNAME = 'Screw'
    UNION  ALL
    SELECT SP.PNO
    FROM   SP
    WHERE  SP.SNO = 'S1'</code></pre>

    Kết quả: `P1` * 5, `P2` * 2.

 6. 

    <pre class="highlight"><code>SELECT DISTINCT P.PNO
    FROM   P
    WHERE  P.PNAME = 'Screw'
    UNION  ALL
    SELECT SP.PNO
    FROM   SP
    WHERE  SP.SNO = 'S1'</code></pre>

    Kết quả: `P1` * 3, `P2` * 2.

 7. 

    <pre class="highlight"><code>SELECT P.PNO
    FROM   P
    WHERE  P.PNAME = 'Screw'
    UNION  ALL
    SELECT DISTINCT SP.PNO
    FROM   SP
    WHERE  SP.SNO = 'S1'</code></pre>

    Kết quả: `P1` * 4, `P2` * 2.

 8. 

    <pre class="highlight"><code>SELECT DISTINCT P.PNO
    FROM   P
    WHERE  P.PNAME = 'Screw'
    OR     P.PNO IN
         ( SELECT SP.PNO
           FROM   SP
           WHERE  SP.SNO = 'S1' )</code></pre>

    Kết quả: `P1` * 1, `P2` * 1.

 9. 

    <pre class="highlight"><code>SELECT DISTINCT SP.PNO
    FROM   SP
    WHERE  SP.SNO = 'S1'
    OR     SP.PNO IN
         ( SELECT P.PNO
           FROM   P
           WHERE  P.PNAME = 'Screw' )</code></pre>

    Kết quả: `P1` * 1, `P2` * 1.

 10. 

    <pre class="highlight"><code>SELECT P.PNO
    FROM   P
    GROUP  BY P.PNO , P.PNAME
    HAVING P.PNAME = 'Screw'
    OR     P.PNO IN
         ( SELECT SP.PNO
           FROM SP
           WHERE SP.SNO = 'S1' )</code></pre>

    Kết quả: `P1` * 1, `P2` * 1.

 11. 

    <pre class="highlight"><code>SELECT P.PNO
    FROM   P , SP
    GROUP  BY P.PNO , P.PNAME , SP.SNO , SP.PNO
    HAVING ( SP.SNO = 'S1' AND
             SP.PNO = P.PNO )
    OR     P.PNAME = 'Screw'</code></pre>

    Kết quả: `P1` * 2, `P2` * 2.

 12. 

    <pre class="highlight"><code>SELECT P.PNO
    FROM   P
    WHERE  P.PNAME = 'Screw'
    UNION
    SELECT SP.PNO
    FROM   SP
    WHERE  SP.SNO = 'S1'</code></pre>

    Kết quả: `P1` * 1, `P2` * 1.

*Chú ý:* Thực ra, một số công thức ở trên có thể không đúng, chúng cần điều kiện tất cả parts mà có tên là `Screw` đều phải được cung cấp bởi ít nhất một supplier. Nhưng điều này không ảnh hưởng tới luận điểm.

Hiển nhiên điểm đầu tiên cần chú ý đó là, ở đây có mười hai công thức khác nhau, và tạo ra chín kết quả khác nhau: khác nhau *độ trùng lặp (degree of duplication)*. (Tôi không nói mười hai công thức và chín kết quả này là tất cả khả năng có thể xảy ra.) Do đó, nếu một người dùng thực sự quan tâm đến sự trùng lặp, thì anh ấy phải cực kỳ cẩn thận khi xây dựng công thức cho một câu truy vấn như thế, để có thể tạo ra kết quả theo ý muốn.

Tiếp theo, một chú ý tương tự cũng áp dụng cho chính hệ thống. Vì các công thức khác nhau có thể tạo ra các kết quả khác nhau, nên optimizer cũng phải cực kỳ cần trọng trong nhiệm vụ biến đổi biểu thức. Ví dụ, optimizer không thể tùy ý biến đổi, giả sử, công thức 1 thành công thức 12, dù nó muốn. Dưới đây là một số hệ quả:

 * Hệ thống khó viết code cho optimizer, khó bảo trì code, và có thể gây ra nhiều bug&mdash;từ đó khiến cho sản phẩm tốn kém hơn và không đáng tin, cũng như chậm tung ra thị trường.

 * Hiệu năng hệ thống có thể kém hơn.
 
 * Người dùng bị dính vào vấn đề hiệu năng. Cụ thể, họ phải bỏ thời gian và công sức tìm ra một công thức SQL tốt nhất (hiệu năng tốt nhất) cho một câu truy vấn&mdash;mô hình quan hệ tuyệt đối tránh điều này.

Sự kìm hãm của hàng trùng nhau lên optimizer còn đặc biệt gây nản lòng ở chỗ, trong đa số trường hợp, người dùng có lẽ *không* quan tâm tới các hàng trùng lặp trong kết quả truy vấn. Tức là, họ có lẽ không hề bận tâm tới việc các công thức khác nhau tạo ra các kết quả khác nhau; nhưng optimizer không thể nhận ra điều đó, nên nó bị cản trở, một cách quá mức cần thiết, thực hiện các biến đổi mà đáng ra nó phải làm như thế.

Như vậy, tôi khuyên bạn nên đảm bảo kết quả của truy vấn không chứa hàng trùng lặp&mdash;ví dụ, bằng cách chỉ ra DICTINCT trong truy vấn SQL&mdash;từ đó bạn sẽ không phải bận tâm tới toàn bộ vấn đề.

Còn rất nhiều điều tôi có thể nói về vấn đề này, nhưng tôi chỉ đủ chỗ cho hai điểm nữa. Thứ nhất, một tùy chọn khác thay cho DICTINCT trong SQL là SELECT ALL&mdash;không may lại là tùy chọn mặc định. Và có lẽ DICTINCT mất nhiều thời gian thực thi hơn SELECT ALL. Tôi sẽ không thảo luận điểm này xa hơn, ngoài chú ý rằng các hệ thống SQL thường không thể tối ưu hóa một cách chính xác trong việc khử hàng trùng lặp là do thiếu hiểu biết về *kế thừa khóa (key inheritance)* (tức là, khả năng tìm ra khóa cho kết quả của một biểu thức quan hệ).

Thứ hai, có lẽ bạn sẽ phản đối rằng các bảng cơ sở trong thực tế không bao giờ chứa hàng trùng lặp, nên ví dụ của tôi đã sai. Điều này đúng; nhưng vấn đề là, SQL có thể *tạo ra* trùng lặp trong kết quả của truy vấn. Thật vậy, các công thức khác nhau của cùng một truy vấn có thể tạo ra các kết quả có độ trùng lặp khác nhau, như ta đã thấy, kể cả khi bảng đầu vào không chứa hàng trùng lặp. Ví dụ, xét hai công thức sau của truy vấn "Lấy ra các supplier numbers của các suppliers mà cung cấp ít nhất một part" trong cơ sở dữ liệu suppliers-and-parts:

```
SELECT SNO              │     SELECT SNO 
FROM   S                │     FROM S NATURAL JOIN SP 
WHERE  SNO IN           │
     ( SELECT SNO       │
       FROM SP )        │
```

Nên nếu bạn không muốn coi các bảng trong hình 3.1 là các bảng cơ sở, thì hãy coi chúng là đầu ra của hai câu truy vấn trên, và phần còn lại của quá trình phân tích không thay đổi.

