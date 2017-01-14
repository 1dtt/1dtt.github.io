---
layout: post
title: 'Database in Depth 3.3: No Duplicates'
---

Trong chương trước, tôi đã nói:

 * Quan hệ không bao giờ các tuple trùng nhau, vì body của chúng là một tập hợp (tập hợp các tuple) và tập hợp trong toán học không chứa các phần tử trùng nhau.
 * Quan hệ không bao giờ chứa null, vì body của quan hệ là một tập hợp các tuple, và tuple không bao giờ chứa null.

Như vậy, chủ đề này và chủ đề tiếp theo, null, là hai chủ đề của SQL, không phải mô hình quan hệ; nên tại đây tôi sẽ sử dụng các thuật ngữ của SQL.

## Trùng có gì sai?

Có nhiều lý do thực tế để bảo vệ quan điểm các hàng trùng nhau nên bị loại bỏ. Ở đây tôi muốn nhấn mạnh chỉ một luận điểm&mdash;nhưng tôi nghĩ là rất chắc chắn. Tuy nhiên, luận điểm này dựa trên một số khái niệm mà tôi chưa thảo luận, nên tôi cần cho rằng bạn đã biết trước hai điều sau:

 * Tôi cho rằng bạn đã biết các hệ thống DBMS quan hệ có chứa một thành phần tên là *optimizer*, có nhiệm vụ tìm ra cách tốt nhất để thực thi các truy vấn (tốt nhất ở đây nghĩa là *hiệu năng tốt nhất*).
 * Tôi cũng cho rằng bạn đã biết có một công việc optimizer thực hiện là cái mà đôi khi được gọi là *sửa câu truy vấn (query rewrite)*. Query rewrite là một quá trình biến đổi một biểu thức quan hệ `exp1` thành một biểu thức khác `exp2` mà đảm bảo tạo ra cùng kết quả nhưng việc tính toán `exp2` được thực hiện nhanh hơn.

Bây giờ, tôi có thể trình bày luận điểm của mình. Điểm cơ bản tôi muốn chứng minh ở đây là một số biến đổi biểu thức, tức là một số tối ưu (optimization), có thể là hợp lệ nếu SQL thực sự có tính quan hệ, nhưng lại không hợp lệ nếu tồn tại hàng trùng nhau. Cơ sở dữ liệu (không có tính quan hệ) tôi sử dụng trong quá trình chứng mình như sau (chú ý các bảng đều không có khóa):

<figure>
  <img src="{{ site.baseurl }}/images/2017_01_15_a nonrelational_database_with_ duplicates.png" alt="A nonrelational database, with duplicates" />
  <figcaption>
    FIGURE 3.1: A nonrelational database, with duplicates
  </figcaption>
</figure>

Trước khi tiếp tục, có lẽ tôi nên hỏi một câu: có ý gì khi có ba hàng `<P1, Screw>` mà không phải hai, bốn, hay mười bảy? Nó phải có một ý nghĩa nào đó, vì nếu không, thì tại sao nó lại ở đấy. Tôi có một lần nghe Ted Codd nói rằng, "Nếu một điều đúng, phát biểu nó hai lần không làm cho nó đúng hơn".

Nên tôi phải phỏng đoán có một ý nghĩa nào đó đi kèm với hàng trùng nhau, mặc dù ý nghĩa này rất khó để nhận ra. Tức là, ý nghĩa của hàng trùng nhau có lẽ được quyết định bởi nghiệp vụ của hệ thống, như là có ba hàng `<P1, Screw>` chứ không phải hai, bốn, hay mười bảy.

Bây giờ, xét truy vấn sau: "Lấy ra các part numbers cho các parts hoặc là `Screw` hoặc được cung cấp bởi supplier `S1`, hoặc cả hai". Dưới đây là một số công thức SQL cho truy vấn này, đầu ra nằm dưới mỗi công thức:

 1. 

    <pre><code>SELECT P.PNO
    FROM   P
    WHERE  P.PNAME = 'Screw'
    OR     P.PNO IN
         ( SELECT SP.PNO
           FROM SP
           WHERE SP.SNO = 'S1' )</code></pre>

   Kết quả: `P1` * 3, `P2` * 1.

 2. Item Three