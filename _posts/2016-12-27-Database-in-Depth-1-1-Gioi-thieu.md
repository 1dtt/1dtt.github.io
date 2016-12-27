---
layout: post
title: 'Database in Depth 1.1: Giới thiệu'
---

**Bất kỳ một chuyên gia trong lĩnh vực nào cũng phải có hiểu biết về nền móng của lĩnh vực đó.** Do đó, nếu muốn trở thành một chuyên gia về cơ sở dữ liệu, bạn cần hiểu biết mô hình quan hệ (relational model), bởi vì mô hình này là nền móng của lĩnh vực cơ sở dữ liệu. Ngày nay, tất cả khóa học quản trị cơ sở dữ liệu, dù là học thuật hay thương mại, cũng đều có ý thức dạy về mô hình quan hệ&mdash;nhưng dường như lại dạy rất tệ, và kết quả thì không đi đến đâu. Tôi cho rằng: Cộng đồng cơ sở dữ liệu chắc chắn không hiểu rõ mô hình quan hệ. Dưới đây là một số lý do:

 * Mô hình này chỉ được dạy thoáng qua. Ít nhất là đối với những người mới, rất khó để thấy được sự xác đáng của sách vở, hay rất khó để hiểu được các vấn đề mà mô hình muốn giải quyết, hoặc cả hai.
 * Chính những người dạy cũng không có hiểu biết đầy đủ.
 * (Có thể.) Mô hình này hoàn toàn không được dạy&mdash;thay vào đó, người ta dạy ngôn ngữ SQL hay một biến thể (dialect) nào đó của SQL, như T-SQL của Microsoft.

Nếu sự hiểu biết của bạn về mô hình quan hệ được suy ra từ kiến thức SQL, thì tôi e rằng bạn đã hiểu sai về "thứ mà nó không phải như thế".

<div class="definition">
  <strong>Chú ý</strong>: <strong>SQL</strong> &ne; mô hình quan hệ!
</div>