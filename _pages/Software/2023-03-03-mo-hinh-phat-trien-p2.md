---
title: Các mô hình phát triển phần mềm (P2)
date: "2023-03-03"
tags:
  - study
thumbnail: "/assets/img/thumbnail/software_dev.jpg"
postnum: 3
---

Ở phần 2 này, mình sẽ giới thiệu về 2 loại mô hình truyền thống còn lại là Bản mẫu (Prototype) và mô hình xoắn ốc (Spiral Model).

## 3. Bản mẫu (Prototype)

Thực tế, các yêu cầu đặc tả từ khách hàng rất hiếm khi rõ ràng, đầy đủ ngay từ đầu để thuận tiện cho việc sử dụng mô hình **thác nước** hay **chữ V**, nên mô hình bản mẫu được ra đời nhằm giải quyết vấn đề này. Bản mẫu là mô hình phát triển dựa trên việc thiết kế các bản thử của phần mềm theo yêu cầu của khách hàng, và khách hàng tham gia vào quá trình phát triển, từ đó giúp có cái nhìn tổng quát về hệ thống.

Mô hình bản mẫu:
<a class="post-image" >
<img itemprop="image"  src="/assets/img/post_img/post3_1.PNG"/>
</a>


Trong sơ đồ trên, các pha có nhiều điểm tương đồng với mô hình thác nước. Tuy nhiên, ở pha tinh chế bản mẫu, các bản mẫu sau khi được khách hàng đánh giá, sẽ phải tinh chế theo các yêu cầu của khách hàng, rồi xây dựng lại bản mẫu khác đến khi đáp ứng những yêu cầu của khách hàng thì đưa sản phẩm.

Bản mẫu là mô hình có nhiều ưu điểm. Ưu điểm dễ nhận thấy nhất là việc nhanh có sản phẩm thử. Việc thực hiện bản mẫu cần có sản phẩm thử (bản mẫu) để khách hàng có thể đánh giá, từ đó giúp yêu cầu đặc tả kĩ càng hơn. Thứ hai là giải quyết các yêu cầu không rõ ràng. Việc đưa ra 1 bản mẫu, và thu thập yêu cầu của khách hàng dựa trên bản mẫu đó sẽ giúp ích cho khách hàng có thể hình dung ra được sản phẩm họ cần là gì, từ đó sẽ có đặc tả yêu cầu rõ ràng từ khách hàng, giúp cho việc phát triển phần mềm chính xác và đáp ứng được những gì khách hàng đưa ra.

Thực tế hiện nay, các bản mẫu được sử dụng để đề xuất cho khách hàng hoặc là một kĩ thuật thu thập yêu cầu cho các mô hình khác. Có nhiều lí do cho việc này. Thứ nhất, khi dồn chi phí và nhân lực cho việc phát triển bản mẫu, các công đoạn còn lại sẽ thiếu thời gian và chi phí. Nên kết quả là dù có đầy đủ yêu cầu rõ ràng nhưng sản phẩm vẫn gặp vấn đề về chất lượng, nhất là phần tài liệu và code.Thứ hai, việc chú trọng quá vào bản mẫu cho yêu cầu chức năng dẫn đến dễ bỏ qua các yêu cầu phi chức năng và đa số các dự án thường không đạt yêu cầu phi chức năng (ví dụ như không đạt hiệu suất thời gian, độ bảo mật, toàn vẹn dữ liệu, dễ tương tác,…).

## 4. Mô hình xoắn ốc (Spiral model)

Mô hình xoắn ốc là một mô hình chú trọng vào tính rủi ro của những dự án. Xoắn ốc là sự kết hợp giữa các mô hình Thác nước và Bản mẫu và thêm phần Phân tích rủi ro ở trong. Ở đây, bản mẫu sử dụng để thu thập yêu cầu cho mô hình cũng như phát triển tăng dần, tuần tự. Mỗi giai đoạn trong mô hình xoắn ốc sẽ bắt đầu với yêu cầu, mục đích thiết kế và kết thúc với việc khách hàng kiểm tra tiến độ của sản phẩm.

Đây là mô hình có tỉ lệ thất bại cao khi áp dụng cho các dự án lớn. Bởi vì trước khi thực hiện một dự án, các nhà phân tích rủi ro sẽ nhận thông tin dự án, sau đó đánh giá rủi ro và quyết định xem có nên làm dự án này hay không. Nhưng việc khả thi của phân tích rủi ro là rất thấp vì các công ty phải cần người có chuyên môn cao và kinh nghiệm để có thể phân tích được những loại rủi ro này. Mà trên thị trường, việc các công ti vừa và nhỏ có thể có những con người như thế là rất hiếm gặp.

Ngoài ra, việc quản lý rủi ro, thực hiện các pha nhiều lần cần công sức rất lớn và tài nguyên, con người nên mô hình xoắn ốc được ít dự án sử dụng, chỉ có những công ty lớn với dự án quy mô rộng, nhiều nguồn lực, nhân lực ưu tú và việc phân tích rủi ro cho dự án rất quan trọng thì mới sử dụng mô hình này.

## 5. Tổng hợp

Ở trên là các mô hình phát triển phần mềm truyền thống, được sử dụng qua rất nhiều dự án. Tuy nhiên, trong thực tế người phát triển sẽ dựa vào những mô hình này mà cải tiến, điều chỉnh sao cho phù hợp nhất đối với dự án của mình.
