---
layout: post
title: 'Database in Depth 2.2: Kiểu vô hướng so với Kiểu có hướng'
---

Chúng ta thường nghĩ kiểu dữ liệu hoặc là *vô hướng* (*scalar*) hoặc là *có hướng* (*nonscalar*). Nói chung, một kiểu dữ liệu là vô hướng nếu nó không có thành phần hữu hình và có hướng thì ngược lại&mdash;và giá trị, biến, thuộc tính, toán tử, tham số, và biểu thức thuộc kiểu `T` là vô hướng hay có hướng tùy thuộc vào kiểu `T` là vô hướng hay có hướng. Ví dụ:

 * Kiểu INTEGER là vô hướng; nên giá trị, biến, vân vân có kiểu INTEGER cũng là vô hướng, tức là chúng không có thành phần hữu hình.

 * Kiểu Tuple và Quan hệ là có hướng&mdash;thành phần hữu hình của chúng là thuộc tính&mdash;nên giá trị tuple và quan hệ, biến tuple và quan hệ, vân vân, đều là có hướng.

Nhưng, tôi phải nhấn mạnh rằng những khái niệm này là không chính thức. Thật vậy, chúng ta đã thấy rằng khái niệm *tính nguyên tử* hoàn toàn không có ý nghĩa gì cả, và "tính vô hướng" chỉ là khái niệm của tính nguyên tử với một cái tên khác. Do đó, mô hình quan hệ không dựa vào phân biệt giữa vô hướng với có hướng. Tuy nhiên trong cuốn sách này, tôi sẽ dựa vào phân biệt đó (không chính thức); cụ thể, tôi sử dụng thuật ngữ *vô hướng* để liên kết với các kiểu dữ liệu không phải tuple hay quan hệ; và thuật ngữ *có hướng* để liên kết với kiểu tuple hay kiểu quan hệ.

Cùng xét một ví dụ sau. Đây là một định nghĩa trong ngôn ngữ **Tutorial D** cho relvar cơ sở `S` (suppliers):

```
1    VAR S BASE
2        RELATION { SNO SNO, SNAME NAME, STATUS INTEGER, CITY CHAR }
3        KEY { SNO } ;
```

*Giải thích:*

 * Từ khóa `VAR` trong dòng 1 nghĩa là đây là một khai báo biến; từ khóa `BASE` nghĩa là biến này là một relvar cơ sở.

 * Dòng 2 chỉ ra kiểu dữ liệu của biến. Từ khóa `RELATION` thể hiện đây là kiểu quan hệ; đoạn còn lại chỉ ra tập hợp các thuộc tính tạo nên heading tương ứng. Kiểu dữ liệu này, tất nhiên, là một kiểu có hướng.
 
 * Dòng 3 định nghĩa `{SNO}` là khóa ứng cử cho relvar này.

Thực tế, ví dụ này cũng miêu tả một điểm khác, kiểu dữ liệu này:

```
RELATION { SNO SNO, SNAME NAME, STATUS INTEGER, CITY CHAR }
```

là một *generated* type (kiểu được tạo).  Nói chung, generated type là kiểu dữ liệu thu được từ lời gọi *type generator* (bộ tạo kiểu) (trong ví dụ này, type generator là `RELATION`). Bạn có thể hiểu type generator là một loại toán tử đặc biệt; đặc biệt vì (a) nó trả về một kiểu thay vì một giá trị và (b) nó được gọi trong complie time thay vì run time. Ví dụ, hiện nay đa số ngôn ngữ lập trình đều hỗ trợ một type generator gọi là ARRAY, cho phép người dùng định nghĩa các kiểu array khác nhau. Tuy nhiên, trong cuốn sách này, chúng ta chỉ quan tâm tới hai type generator, đó là `TUPLE` và `RELATION`.  Dưới đây là ví dụ cho lời gọi type generator `TUPLE`:

```
VAR SINGLE_SUPPLIER
    TUPLE { STATUS INTEGER, SNO SNO, CITY CHAR, SNAME NAME } ;
```

Từ đây, chúng ta có thể tưởng tượng một đoạn code mà, đầu tiên, lấy ra một quan hệ chỉ có một tuple (có thể chỉ chứa tuple cho `S1`) từ giá trị hiện tại của relvar `S`, sau đó, lấy ra tuple đơn lẻ từ quan hệ chỉ có một tuple đó; và cuối cùng, gán tuple đó cho biến `SINGLE_SUPPLIER`. Đoạn code này được thể hiện trong **Tutorial D** như sau:

```
SINGLE_SUPPLIER := TUPLE FROM ( S WHERE SNO = SNO('S1') ) ;
```

Chú ý rằng một tuple `t` và một quan hệ `r` (mà chỉ chứa một tuple `t`) không phải là một. Chúng có kiểu dữ liệu khác nhau&mdash;`t` có một kiểu tuple nào đó, và `r` có một kiểu quan hệ nào đó (mặc dù chúng có cùng heading).

*Chú ý:* Đừng hiểu nhầm. Trong khi `SINGLE_SUPPLIER` có thể được cần đến trong tầng ứng dụng mà truy cập tới cơ sở dữ liệu suppliers-and-parts, tôi *không* nói rằng một biến như thế có thể xuất hiện trong chính cơ sở dữ liệu. Cơ sở dữ liệu quan hệ chỉ đúng một loại biến, là biến quan hệ (relvar).