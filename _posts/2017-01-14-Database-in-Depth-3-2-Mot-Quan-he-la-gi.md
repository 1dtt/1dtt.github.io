---
layout: post
title: 'Database in Depth 3.2: Một Quan hệ là gì?'
---

## Một Quan hệ là gì?

Tôi sẽ sử dụng quan hệ suppliers `S` làm ví dụ cho phần này. Đây là hình ảnh:

![s relation]({{ site.baseurl }}/images/2017_01_14_s_relation.png)

Và đây là định nghĩa:

<div class="definition">
  <strong>Định nghĩa</strong>: Cho <em>{H}</em> là một heading và <em>t1, t2, ..., tm (m &#8805; 0)</em> là các tuple khác nhau có cùng heading <em>{H}</em>. Một kết hợp <em>r</em> của <em>{H}</em> và tập hợp <em>{t1, t2, ..., tm}</em> là một <em>giá trị quan hệ</em> (hay gọi tắt là <em>quan hệ</em>) trên các thuộc tính <em>A1, A2, ..., An</em>, trong đó <em>A1, A2, ..., An</em> là các thuộc tính trong <em>{H}</em>. <em>Heading</em> của <em>r</em> là <em>{H}</em>; <em>r</em> có cùng thuộc tính (do đó có kiểu dữ liệu) và cùng degree với heading. <em>Body</em> của <em>r</em> là tập hợp <em>{t1, t2, ..., tm}</em>. Giá trị <em>m</em> là <em>cardinality</em> của <em>r</em>.
</div>

Bạn hãy tự diễn giải quan hệ suppliers từ định nghĩa trên. Ở đây, tôi chỉ giải thích tại sao chúng ta lại gọi là quan hệ. Về cơ bản, mỗi tuple trong một quan hệ biểu diễn một mối quan hệ *n* ngôi (*n*-ary), theo ngôn ngữ tự nhiên, là mối quan hệ giữa *n* giá trị (mỗi giá trị cho một thuộc tính trong tuple); và một tập hợp tất cả các tuple đó là một tập hợp các mối quan hệ tại một thời điểm nhất định&mdash;trong ngôn ngữ toán học, đó là một quan hệ. Do đó, "giải thích" mà chúng ta hay nghe thấy, rằng mô hình quan hệ được gọi như thế bởi vì nó cho phép chúng ta "liên hệ một bảng này với một bảng khác", chính xác chỉ là một loại nghĩa thứ hai, và nó đã bỏ quên điểm chính yếu. Mô hình quan hệ được gọi là mô hình quan hệ bởi vì nó thao tác với các đối tượng trừu tượng mà chúng ta hay gọi là "bảng", thật ra, là các quan hệ.

Bây giờ, một quan hệ, giống như một tuple, chính nó cũng là giá trị và cũng có kiểu dữ liệu, và kiểu dữ liệu này cũng có một tên. Trong **Tutorial D**, tên kiểu dữ liệu cho quan hệ có dạng `RELATION{H}`, trong đó `{H}` là heading. Ví dụ:

```
RELATION { SNO SNO, SNAME NAME, STATUS INTEGER, CITY CHAR }
```

(Thứ tự các thuộc tính là tùy ý.) Cũng như thế, tất cả giá trị quan hệ đều được trả về bởi một lời gọi relation selector nào đó&mdash;ví dụ:

```
RELATION 
   { TUPLE { SNO 'S1' , SNAME 'Smith' , STATUS 20 , CITY 'London' } , 
     TUPLE { SNO 'S2' , SNAME 'Jones' , STATUS 10 , CITY 'Paris'  } , 
     TUPLE { SNO 'S3' , SNAME 'Blake' , STATUS 30 , CITY 'Paris'  } , 
     TUPLE { SNO 'S4' , SNAME 'Clark' , STATUS 20 , CITY 'London' } , 
     TUPLE { SNO 'S5' , SNAME 'Adams' , STATUS 30 , CITY 'Athens' } }
```

Thứ tự các tuple là tùy ý.

Vậy SQL có hỗ trợ những khái niệm trên không? Câu trả lời là: không nhiều. SQL hoàn toàn không có khái niệm tương đương với kiểu dữ liệu quan hệ (relation type); mà một bảng được xem như là chứa các *hàng* (chính xác là một *multiset* hay *bag* của các hàng) cùng kiểu&mdash;row type. (SQL có một thứ gọi là *named table type*, nhưng cấu trúc rất khác với relation type, tôi sẽ không thảo luận tại đây.) Từ đó SQL cũng không thực sự có thứ gì tương đương với RELATION type generator. Nhưng nó lại có *table value constructor", mà hơi na ná với relation selector. Ví dụ:

```
VALUES ( 1, 2 ), ( 2, 1 ), ( 1, 1 ), ( 1, 2 )
```

Biểu thức này tạo ra một bảng chứa bốn&mdash;không phải ba!&mdash;hàng và hai cột (không tên).

## Một số hệ quả quan trọng

Hầu hết các tính chất của quan hệ mà tôi đã đề cập trong Chương 1 đều là các hệ quả trực tiếp từ những định nghĩa trên. Nhưng vẫn còn vài hệ quả quan trọng mà tôi chưa nói rõ, và bây giờ tôi muốn xem xét chúng một cách tỉ mỉ.

Điểm thứ nhất đó là *tất cả tập con của một body là một body* (nên nói chung, tất cả tập con của một quan hệ là một quan hệ). Cụ thể, tập rỗng là một tập con của mọi quan hệ, nên một quan hệ có thể chứa body là rỗng (chúng ta có thể gọi quan hệ như thế là *quan hệ rỗng*). Ví dụ, giả sử hiện tại không có shipments nào. Khi đó, relvar `SP` sẽ có giá trị là một quan hệ rỗng, có thể vẽ như sau:

![empty relation]({{ site.baseurl }}/images/2017_01_14_empty_shipments_relation.png)

Chú ý là với bất kỳ kiểu dữ liệu quan hệ nào, chỉ có đúng một quan hệ rỗng thuộc kiểu dữ liệu đó&mdash;các quan hệ rỗng của các kiểu quan hệ khác nhau không giống nhau, vì chúng khác kiểu dữ liệu. Ví dụ, quan hệ suppliers `S` rỗng không bằng với quan hệ parts `P` rỗng; body của chúng bằng nhau nhưng heading thì không.

Điểm thứ hai mà tôi chưa nói đầy đủ là. Nhớ lại từ Chương 1, một quan hệ được vẽ thành một bảng, nhưng nó không phải là một bảng. (Xin nhắc lại lần nữa, một hình ảnh của một vật không phải là vật đó.) Tuy nhiên, thực tế chúng ta có thể coi quan hệ, một cách không chính thống, là bảng&mdash;tường minh hơn, là *bảng phẳng (flat table)* hay *bảng 2 chiều (two-dimensional table)*&mdash;thực sự, điều đó đã khiến cho các hệ thống quan hệ dễ hiểu hơn, dễ sử dụng hơn, cũng như dễ hình dung ra hành vi của hệ thống. Nói cách khác, đây là một điểm tốt của mô hình quan hệ, rằng cấu trúc dữ liệu cơ bản của nó, tức là quan hệ, có thể được biểu diễn một cách trực quan, dễ hiểu trên hình vẽ.

Nhưng không may, rất nhiều người dường như bị bịt mắt bởi biểu diễn hình ảnh trực, quan dễ hiểu đó, và họ luôn nghĩ rằng *quan hệ* là "phẳng" hay "2 chiều". Không phải vậy. Nếu một quan hệ `r` có `n` thuộc tính, thì <em>mỗi tuple trong <code>r</code> biểu diễn một điểm trong một không gian <code>n</code> chiều</em> (và một quan hệ biểu diễn một tập hợp toàn bộ các điểm đó). Ví dụ, mỗi tuple trong năm tuple mẫu trong relvar `S` của chúng ta biểu diễn một điểm trong không gian 4 chiều, nên quan hệ `S` là 4 chiều. Nói tóm lại, quan hệ là *n* chiều, không phải 2 chiều.

Bây giờ, tôi muốn chuyển tới ba vấn đề khác. Chúng là: các tuple trùng nhau, null, và quan hệ không có thuộc tính. Mỗi vấn đề sẽ trong một phần riêng.