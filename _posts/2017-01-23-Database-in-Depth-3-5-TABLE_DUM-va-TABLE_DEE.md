---
layout: post
title: 'Database in Depth 3.5: TABLE_DUM và TABLE_DEE'
---

Nhớ lại rằng tập hợp rỗng là một tập hợp con của mọi tập hợp, nên tồn tại một thứ gọi là tuple rỗng (còn gọi là 0-tuple), và tất nhiên nó có một heading rỗng. Lúc trước tôi đã không chỉ ra&mdash;hiển nhiên&mdash;một quan hệ cũng có thể có một heading rỗng (một heading là một tập hợp các thuộc tính, không có lý do gì tập hợp không thể là rỗng). Một quan hệ như thế có kiểu `RELATION{}`, và degree của nó là 0. Đôi khi ta cũng gọi nó là 0-ary (hay *nullary*). (*Nullary* không liên quan tới null; null xấu, nhưng quan hệ nullary thực sự lại tốt. Tuy nhiên, tôi thường tránh sử dụng thuật ngữ *nullary*, chính vì nó nghe có vẻ liên quan đến null.)

Giả sử `r` là một quan hệ có degree 0, thì. Tồn tại bao nhiêu quan hệ như thế? Câu trả lời: chỉ có hai. Thứ nhất, tất nhiên, `r` có thể là rỗng (không chứa tuple)&mdash;nhớ rằng chỉ có đúng một quan hệ rỗng cho mỗi kiểu dữ liệu quan hệ. Thứ hai, nếu `r` không là rỗng, thì các tuple nó chứa đều phải là 0-tuple. Nhưng chỉ có một 0-tuple!&mdash;tương đương với, tất cả 0-tuple đều trùng lặp lẫn nhau&mdash;nên `r` không thể chứa nhiều hơn một 0-tuple. Do đó thực sự chỉ có hai quan hệ có degree 0: một cái chỉ có một tuple, một cái không có tuple nào. Vì nhiều lý do hiển nhiên, tôi sẽ không thử vẽ hình ảnh của hai quan hệ này; thực tế, đây là một nơi mà ý tưởng coi quan hệ là bảng bị đổ vỡ.

Bây giờ, có thể bạn sẽ nghĩ: "Vậy thì sao? Tại sao tôi lại cần một quan hệ không có một thuộc tính nào?" Cho dù nếu chúng đáng kể đối với toán học (đúng vậy), chúng cũng không có ý nghĩa thực tiễn? Nhưng hóa ra chúng có ý nghĩa thực tiễn rất lớn, đến nỗi chúng ta đặt biệt danh cho chúng&mdash;chúng ta gọi chúng là TABLE_DUM và TABLE_DEE, hay chỉ DUM và DEE (DUM là quan hệ rỗng, DEE là quan hệ chứa một tuple). Và lý do chúng rất có ý nghĩa là ở *ý nghĩa* của chúng, DUM là FALSE (hay NO) và DEE là TRUE (hay YES).

Nhân tiện, có một cách dễ nhớ là: YES và DEE đều có "E"; NO và DUM thì không.

Tôi vẫn chưa truyền tải đủ để có thể đưa ra một ví dụ cụ thể cho DUM và DEE, nhưng chúng ta sẽ bắt gặp rất nhiều ví dụ về sử dụng chúng trong các phần còn lại. Ở đây tôi chỉ muốn đề cập một điểm tại nơi chuyển tiếp này: DEE đóng vai trò trong đại số quan hệ tương đương với vai trò của số không trong số học. Tất cả chúng ta đều biết số không quan trọng như thế nào; trên thực tế, rất khó tưởng tượng ra một số học không có số không, TABLE_DEE trong đại số quan hệ cũng như thế.