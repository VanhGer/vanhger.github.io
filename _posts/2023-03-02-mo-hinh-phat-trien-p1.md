---
title: Các mô hình phát triển phần mềm (P1)
# feature-img: "assets/img/blur/red1.png"
img_title: "assets/images/title_img/post2.png"
# tags: [Software]
author: vanhg
permalink: /blog/2023/03/02/mo-hinh-phat-trien-p1

---

Mô hình phát triển phần mềm là một quy trình tiêu chuẩn để phát triển phần mềm. Nó xác định các giai đoạn/ pha trong xây dựng phần mềm, từ lúc bắt đầu định hình yêu cầu cho đến khi phần mềm hoạt động được. Mô hình phát triển đóng vai trò rất quan trọng trong việc giúp các nhà phát triển thực hiện phần mềm một cách có hệ thống và hiệu quả, đảm bảo chất lượng và yêu cầu từ khách hàng.
Có rất nhiều loại mô hình phát triển như thác nước, bản mẫu, RAD, TDD,… Trong bài viết này, mình sẽ giới thiệu về 2 mô hình đầu tiên trên 4 loại mô hình phát triển truyền thống cơ bản là mô hình thác nước (Waterfall Model), mô hình chữ V (V-model), bản mẫu (Prototype) và mô hình xoắn ốc (Spiral Model).

## 1. Mô hình thác nước.
Mô hình thác nước là mô hình phát triển phần mềm theo quy trình tuần tự và liên tiếp. Sau khi kết thúc giai đoạn trước, thì mới chuyển sang giai đoạn sau, không thể có 2 giai đoạn được xử lý song song. Lý do bởi vì kết quả của giai đoạn này sẽ đóng vai trò là đầu vào, yêu cầu của giai đoạn tiếp theo.

Minh hoạ mô hình thác nước: 

<!-- {% include aligner.html images="post-pic/post2-1.png" column = 3%} -->

Trong giai đoạn định nghĩa yêu cầu, các yêu cầu từ khách hàng sẽ được xác định và đặc tả chi tiết trong tài liệu, làm cơ sở để thực hiện giai đoạn thiết kế. Ở giai đoạn thiết kế, các nhà phát triển phải thảo luận, tìm ra yêu cầu của phần cứng, và đưa ra kiến trúc tổng thể của hệ thống phần mềm. Trong pha triển khai, nhà phát triển sẽ lập trình các chương trình nhỏ để tích hợp trong giai đoạn tiếp theo. Cuối cùng, sẽ là việc tích hợp các chương trình và kiểm thử trước khi đưa ra cho khách hàng sản phẩm.

Mô hình thác nước có những ưu điểm nổi trội về chất lượng và thoả mãn các yêu cầu phi chức năng. Thứ nhất là việc dễ học, dễ áp dụng. Việc làm các giai đoạn một cách tỉ mỉ, cẩn thận kèm theo việc viết tài liệu song song sẽ khiến cho phần mềm có tài liệu hướng dẫn kĩ càng, phục vụ cho việc sử dụng và đào tạo khách hàng. Về chất lượng đây là mô hình giúp sản phẩm có chất lượng cao vì các giai đoạn được làm cẩn thận, dành nhiều thời gian cho việc kiểm thử, tích hợp.

Bên cạnh các ưu điểm, mô hình này cũng bao gồm nhiều nhược điểm sau. Thứ nhất, đây là mô hình vận hành lâu, chi phí cao. Lí do là vì việc phát triển theo mô hình thác nước đảm bảo sản phẩm có chất lượng cao, do đó trong từng giai đoạn, phải làm một cách kĩ càng và cẩn thận bởi lẽ nếu sai phạm ở giai đoạn nào đó, việc quay ngược lên để sửa chữa là rất tốn thời gian. Ngoài ra, việc làm tài liệu, giảng giải các hoạt động ở giai đoạn trước để những người thực hiện giai đoạn sau có thể dễ dàng hiểu cũng tốn rất nhiều công sức của người làm phần mềm. Thứ hai là chỉ phù hợp với dự án vừa và nhỏ, có yêu cầu rõ ràng từ đầu các dự án nhỏ, vừa thì yêu cầu sẽ đơn giản hơn, do đó dễ thiết kế và thực hành hơn. Các dự án lớn thì có yêu cầu phức tạp, phạm vi dự án có thể thay đổi nên mô hình thác nước là rất khó xác định và đặc tả tổng thể các yêu cầu cùng 1 lúc. Thứ ba, mô hình thác nước sẽ khiến cho việc sản phẩm có chậm. Việc làm tuần tự và phải đảm bảo chất lượng cao dẫn đến tốc độ ra sản phẩm chậm. Do đó ảnh hưởng đến tiến độ của dự án. Ngoài ra, việc ra sản phẩm chậm thì sẽ có ít thời gian hướng dẫn khách hàng sử dụng sản phầm. Điều này sẽ dẫn đến việc trong quá trình sử dụng, khách hàng sẽ vô tình gây ra các lỗi cho phần mềm.

Trong thực tế, việc xây dựng các pha đầy đủ và không mắc phải lỗi nào là điều rất khó xảy ra. Nên khi trong quá trình phát triển, gặp phải vấn đề thì người phát triển phải quay lại các pha trước đó, tìm và sửa lỗi, rồi lại tiếp tục đi theo quá trình tuần tự và liên tục của model.
## 2. V-model:
Về vấn đề kĩ thuật, mô hình thác nước còn có các điểm cần khắc phục là việc kiểm thử vẫn đang quá chung chung, chưa được đề cập rõ ràng. Hơn nữa, việc sinh test muộn (bước cuối cùng mới sinh test để kiểm tra sản phẩm) sẽ dễ đến việc chât lượng bộ test thấp, không đủ tính khách quan. Từ đó khiến chất lượng của phần mềm sẽ không đảm bảo.Do vậy, V-model ra đời nhằm cải tiến những điểm yếu về kĩ thuật này. 

<!-- Minh hoạ V-model: {% include aligner.html images="post-pic/post2-2.png" column = 3%} -->
 
Mô hình chữ V cũng bao gồm các pha gần giống như thác nước. Tuy nhiên để tránh việc chung chung trong kiểm thử, mô hình này đã cụ thể các pha kiểm thử là kiểm thử tích hợp, kiểm thử hệ thống và kiểm thử cấp nhận. V-model đưa việc sinh các test song song với các giai đoạn. Ví dụ khi ở giai đoạn đặc tả yêu cầu, khi có yêu cầu, nhà phát triển sẽ sinh bộ test cho việc đặc tả đó, vv. Sau khi cài đặt xong, sẽ lấy những bộ test đó để kiểm tra hiểu quả của từng pha. Việc sinh test này sẽ khách quan hơn rất nhiều so với mô hình thác nước, và chất lượng bộ test cũng sẽ cao hơn. Lý do chính là việc sinh test do chủ quan người phát triển. Nếu như sinh test trước từng pha, test sẽ cụ thể hơn và giúp tăng khả năng phát hiện lỗi hơn rất nhiều. Ngược lại, việc sinh test sau khi có sản phẩm, bộ test sẽ rất chung chung, dễ pass. 

Bên cạnh đó, việc kiểm thử được chia ra rất rõ ràng các pha như đã nói ở trên. Cụ thể thì unit test (kiểm thử đơn vị) sẽ chịu trách nhiệm kiểm tra từng thành phần phần mềm như mô hình thác nước. Kiểm thử tích hợp là nhóm các thành phần phần mềm do kiến trúc quy định, để kiểm tra sự tương tác của chúng với nhau. Đối tượng kiểm thử không phải là một hệ thống hoàn chỉnh. Ở pha kiểm thử hệ thống, hệ thống hoàn chỉnh sẽ được đội phát triển kiểm tra, đánh giá trước khi gửi cho khách hàng. Cuối cùng là kiểm thử chấp nhận, pha này sẽ do khách hàng dùng thử sản phẩm và đánh giá cuối cùng.

## Tạm kết
Ở trên là 2 trên 4 loại mô hình phát triển phần mềm truyền thống. Ở phần 2, mình sẽ giới thiệu thêm về bản mẫu và mô hình xoắn ốc.


