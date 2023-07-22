---

title: Cơ bản về đồng thuận
# feature-img: "assets/img/blur/bandb.png"
# img_title: "assets/images/title_img/post1.png"
# tags: [Instruction]
author: vanhg
permalink: /blog/2023/07/21/fundamentals-of-consensus

---

Ở bài viết này, chúng ta sẽ tìm hiểu về cách Bitcoin chống lại việc những nodes không trung thực tấn công vào mạng lưới. 
Những nodes không trung thực (Adversary) là những nodes gây hại, chúng có mục đích khiến Blockchain không còn minh bạch và nhẩt quán để dễ dàng thêm vào những giao dịch sai mà có lợi cho chúng. 

## Tác hại của Adversary
Có thể gây:
- Lỗi crash: Nếu các nodes này không gửi và nhận các msg (message)
- Lỗi bỏ sót: Nếu các nodes này chọn hoặc bỏ qua các msg
- Lỗi Byzantine: Các node sai có thể đi lệch khỏi giao thức.

Có 2 loại adversary
- Static: phá huỷ node mà nó chọn trước khi bắt đầu giao thức.
- Adaptive: có thể phá huỷ các node khi giao thức xảy ra.
<br>
Giới hạn sức mạnh của adversary là số node:  f. Ví dụ: f < n/2, f < n/3.

## Các nodes giao tiếp với nhau
Các nodes gửi các tin nhắn (message) cho nhau trong giao thức. Giả sử một node adversary đang điều khiển việc gửi tin nhắn:
- Trong một mạng đồng bộ: Adversary phải chuyển tin nhắn bất kì trong thời gian Δ giây (Δ đã biết).
- Trong mạng không đồng bộ: Adversary có thể hoãn msg trong 1 khoảng thời gian hữu hạn, nhưng cuối cùng vẫn phải chuyển.
- Trong mạng đồng bộ 1 phần: tồn tại 1 sự kiện gọi là GST mà: Một msg gửi bởi 1 honest node ở thời gian t sẽ đến với người nhận trong khoảng thời gian:  Δ + Max(t, GST). 
<br>
Điều này tương đương với mạng sẽ không đồng bộ cho tới GST, sau đó sẽ đồng bộ

### State Machine Replication (SMR)
Là phương pháp chung để triển khai dịch vụ chịu lỗi bằng việc sao chép máy chủ và điều phối tương tác của máy client với bản sao đó. Nó cung cấp framework để hiểu và thiết kết giao thức quản lý sao chép.

SMR gồm 2 phần:
- Replicas: Nhận Tx, chạy giao thức SMR và xác định log. Các Log(Ledger) là chuỗi Tx thứ tự tuyến tính.
- Client: Giao tiếp với replicas để nhận log (ví dụ: wallet).
Các wallet không chạy giao thức SMR và cũng không giao tiếp với nhau. Nó hỏi các replicas về các log và chọn kết quả được nhiều replicas gửi về nhất.

### Bảo mật cho SMR
Gọi A ≼ B khi A là tiền tố của B. Ví dụ: A = Tx1Tx2Tx3, B = Tx1Tx2Tx3Tx5Tx6 thì A là tiền tố của B.<br>
Gọi LOG(i, t) là log đưa ra bởi client i ở thời điểm t. Một giao thức SMR là an toàn nếu thoả mãn:
- Safety: Với client i và j, ở 2 thời gian t và s: <br>
    LOG(i, t) ≼ LOG(j, s) hoặc LOG(j, s) ≼ LOG(i, t) hoặc cả 2. => không thể xảy ra Double Spent
- Liveness: với Tx là input của 1 honest replica ở thời điểm t, => với mọi i ở thời điểm s ≥ t thì: <br>
Tx ∈ LOG(i, s). 

## Giao thức Blockchain
Các Txs được đóng thành các block để tăng thông lượng. Gọi CH(i, t) là chuỗi được accept bởi client i tại thời gian t. Giao thức blockchain đảm bảo an toàn nếu thoả mãn:
- Safety: Với client i và j, ở 2 thời gian t và s: <br>
    CH(i, t) ≼ CH(j, s) hoặc CH(j, s) ≼ CH(i, t) hoặc cả 2
- Liveness: với Tx là input của 1 honest replica ở thời điểm t, => với mọi i ở thời điểm s ≥ t thì: <br>
Tx ∈ CH(i, s).

Mong muốn giao thức phải đảm bảo an toàn dưới môi trường bán đồng bộ. Theo DLS(1988), nếu f ≥ n/3 thì giao thức SMR không thể đảm bảo an toàn. Bây giờ với f < n/3, ta sẽ chứng minh là giao thức này an toàn.

## Chứng minh
Thời gian chia thành các epoch, mỗi epoch là 2Δ giây. Có n replicas cố định, mỗi epoch e được chỉ định 1 leader Le bởi hàm Hash.
### Baby streamlet
<strong>Đề xuất khối: </strong> Ở đầu mỗi epoch, Le xác định chuỗi dài nhất nó từng thấy cho đến nay và sẽ đề xuất 1 khối liền sau chuỗi đó. <bn>
<strong>Finalization: </strong> 1 replica finalizes block (và tiền tố của nó) ở đầu chuỗi. Nếu có nhiều block như thế, chọn block có epoch nhỏ nhất.<br>
Tuy nhiên, có một nhược điểm là nếu Leader là adversary và network chưa đồng bộ, thì Adversary có thể gửi thông báo các Block cho 1 user bất kì, nhưng thông báo chưa tới các user còn lại cho tới sau GST. 
/// hình minh hoạ

### Teen streamlet
Votes: 1 vote trên block bởi replica là chữ kí của nó trên block. <br>
Notarization: 1 block được xem là Notarized dưới góc nhìn của replica nếu block đó có hơn 2n/3 chữ kí từ các replica khác.<br>
<strong>Đề xuất khối: </strong> Đầu mõi epoch e, Le xác định chuỗi Notarized dài nhất và đề xuất khối sau chuỗi Notarized này.<br>
<strong> Vote: </strong> ∆ giây ở epoch e,mỗi replica vote cho đề xuất hợp lệ đầu tiên dưới góc nhìn của Le (mở rộng chuỗi dài nhất theo góc nhìn của nó).<br>
<strong>Finalization: </strong>  1 replica finalize 1 block và tiền tố của nó sau khi thấy khối được notarized.

Ví dụ: /// hình ảnh <br>
Chứng minh: với f < n/3, với mỗi epoch e, chỉ có tối đa 1 notarized block với epoch là e trong góc nhìn của bất kì replica trung thực nào. <br>
Giả sử 2 khối cùng epoch e là B và B'. Vì f < n/3 nên tồn tại replica vote cho cả B, B'. Mà số vote của B và B' lớn hơn 2/3 => số replica vote cho cả 2 là > n/3. Đây là vô lý. Vậy khi network chưa đồng bộ, giao thức này đảm bảo an toàn.<br>
Với các block có epoch khác nhau, có thể cùng ở chung 1 độ cao. Gọi 2 block đó là B1 và B2. Ban đầu, ở B, adversarial đề xuất B1 cho 66 replica trung thực, nên B1 chưa được notarized (vì có 66 vote). Tiếp theo, B2 được đề xuất bởi 1 replica trung thực, và được notarized nhưng trong đó có 1 adversarial vote cho B1, nên B1 cũng được notarized. Từ đó dẫn đến vi phạm safety.

### Streamlet
<strong>Finalization: </strong> Khi thấy 3 khối kề nhau trong chuỗi được notarized với epoch liên tiếp, thì client sẽ finalize block thứ 2 trong 3 block trên và tẩt cả tiền tố của nó.

Độ phức tạp giao tiếp là O(N^3) message cho mỗi block, vì mỗn honest replica chuyển tiếp các votes nó nhận từ các replica khác cho tất cả các replica.
Chứng minh tính an toàn:<br>
<strong> Safety: </strong> Giả sử có 2 block đang trong tình trạng confict ở epoch e và e'. Nếu: <br>
- e = e': không thể (đã chứng minh ở Teen Streamlet).<br>
- e < e': suy ra e+1 < e'. Vì B3 được notarized nên có ít nhất S > n/3 replica trung thực vote cho B3 tại e+1. Vậy S sẽ thấy B2 được notarized ở đầu epoch e+1, do đó, S sẽ không vote cho B' vì B' không mở rộng từ chuõi dài nhất. Mà S > n/3 nên B' không được notarized. <br>
- e > e': suy ra e-1 > e'. Nếu B' được notarized, có S > n/3 replica trung thực vote cho nó tại epoch e'. Vậy S sẽ thấy B" được notarized ở đầu epoch e", do đó S sẽ không vote cho B1. Tương tự, B1 không được notarized. Điều này trái với giả thiết.<br>
Hình Minh Hoạ: <br>
Vậy, Streamlet đảm bảo safety<br>

<strong> Liveness: </strong> Giả sử có 4 slot liên tiếp: e, e + 1, e + 2, e + 3 với leader trung thực sau GST. Ở cuối các slot này, client sẽ finalize block được đề xuất bởi các replica trung thực. Trong đó:
- 1 slot để undo các hành động của adversary. <br>
- 3 slot để finalize block. <br>
Ngoài ra, đảm bảo không có 2 block liên tiếp nhau mà chưa được notarized.




