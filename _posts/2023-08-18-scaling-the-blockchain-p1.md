---

title: Mở rộng Blockchain (Phần 1)
# feature-img: "assets/img/blur/bandb.png"
# img_title: "assets/images/title_img/post1.png"
# tags: [Instruction]
author: vanhg
permalink: /blog/2023/08/18/scaling-the-blockchain-p1

---

Với Bitcoin, tốc độ thực hiện giao dịch chỉ có khoảng 7 Tx / sec, còn với Ethereum, con số này là 15. Trong khi đó, với VISA là 2000, Paypal là 200. Vậy để các loại Blockchain này được sử dụng nhiều trên thế giới, cần phải có những giải pháp để tăng tốc độ các giao dịch. Trong bài viết này, mình sẽ giới thiệu đến một cách là sử dụng Payment Channel, hạn chế tương tác với chuỗi càng ít càng tốt, giúp tăng tốc độ.

## Payment Channel: Ý tưởng cơ bản
A và B đang muốn giao dịch với nhau. A gửi cho B 5 lần, mỗi lần là 0.01 BTC, là 5 Txs. Thay vì làm như thế, có thể làm như sau: A gửi cho B 1BTC. Sau 1 khoảng thời gian (ví dụ 1 tháng), B sẽ trả lại số BTC dư mà chưa dùng. Vậy số giao dịch chỉ giới hạn đến 2.

## Unidirectional Payment Channel: Kênh thanh toán 1 chiều
Ta có: UTXO A: 1BTC. 
- Tx1: 0.99 cho A/ 0.01 cho B từ UTXO A.
- Tx2: 0.98 cho A/ 0.02 cho B
- ....
- Tx5: 0.95 cho A/ 0.05 cho B

Xong xuôi, B post Tx5 lên Blockchain. Nhưng có vấn đề xảy ra là nếu A sử dụng UTXO trước khi B post lên thì Tx5 mà B post lên sẽ không hợp lệ. Do đó, cần sử dụng 1 địa chỉ chung UTXO AB có dạng Multisig 2-2. Điều này sẽ ngăn chặn việc A dùng UTXO vì chưa có chữ kí của B.

Nhưng nếu B không post Tx5 lên chuỗi thì sao? Liệu A có lấy lại được 0.95 BTC. Để giải quyết vấn đề này, cần sử dụng một time-locked Tx, đảm bảo sau một khoảng thời gian, nếu B không post Tx5 lên, thì A sẽ lấy lại được 1BTC (B phải gửi cho A). Ngoài ra, Tx này phải được kí trước khi đưa BTC vào trong AB.

Minh hoạ:


Sau khi trả hoặc lấy lại tiền, kênh sẽ đóng. 

## Bidirectional Payment Channel: Kênh thanh toán đa chiều
Không sử dụng 2 kênh thanh toán 1 chiều thay thế. Thay vào đó, ta sẽ sử dụng contract.

### Trên Ethereum
A và B sẽ tạo một contract chung, mỗi người đóng góp 0.5 ETH. Lúc này, trạng thái của contract sẽ là: <br>
A: 0.5 ETH      B: 0.5 ETH      Nonce: 0. <br>
Off chain: B gửi 0.1 ETH cho A bằng cách cả 2 ký vào state mới: <br>
A: 0.6,  B: 0.4,  Nonce: 1, A sig,  B sig   <br>
Ở trên chuỗi, contract vẫn không đổi. Giả sử, offchain, A và B đã gửi đi gửi lại nhiều giao dịch:<br>
A: 0.3,  B: 0.7,  Nonce: 7, A sig,  B sig <br>

Lúc này, A muốn kết thúc kênh, sẽ gửi số dư cuối cùng và các chữ ký cho contract. Lúc này sẽ bắt đầu Challenge Period (ví dụ 3 ngày). <br>
Onchain: A: 0.3,    B: 0.7,     Nonce: 7. <br>
- Nếu trong 3 ngày, B không làm gì thì số tiền và trạng thái của contract sẽ theo những gì A gửi lên.
- Ngược lại, nếu B gửi lên 1 state với số Nonce lớn hơn (ví dụ = 9), thì contract sẽ theo state của B.

Vấn đề được đưa ra là việc A và B phải quan sát thường xuyên xem đối phương có gửi state cũ lên không để có thể kịp thời ngăn chặn. Điều này được giải quyết bằng sự trợ giúp của WatchTower.

Tóm lại, việc giao dịch sẽ chỉ tốn 2 lần onchain:
- Tạo channel
- Đóng channel và gửi tiền.

### Trên Bitcoin
Vì trên UTXO không có trạng thái, nên sẽ khó khăn hơn để tạo 1 kênh thanh toán 2 chiều. Giải pháp được đưa ra là khi update kênh theo A, thì A sẽ nhận được Tx mà làm trạng thái cũ của B mất hiệu lực.

#### UTXO
Ta sẽ tạo UTXO mà có thể dùng theo 1 trong 2 cách (sử dụng IF trong Opcode):
- Relative time-lock: UTXO chứa số t. 1 Tx được kí hợp lệ có thể sử dụng UTXO này sau t blocks (hoặc nhiều hơn) sau khi nó được tạo ra.
- Hash lock: UTXO chứa một số X. Một Tx được kí hợp lệ có thể sử dụng UTXO này nếu có số x mà: SHA256(x) = X.
(x được gọi là hash preimage của X).

#### Ví dụ:
Giả sử A và B đưa BTC vào trong 2-2 Multisig UTXO AB, với A là 7BTC, B là 3BTC. <br>
- A tạo số ngẫu nhiên x, và X = SHA256(x). 
- B tạo số ngẫu nhiên y, và Y = SHA256(y).
- Sau đó, A đưa X cho B và ngược lại.
- A tạo Tx1 với input là UTXO AB, output là: <br>
    1: pay 7 -> A,  <br>
    2: 3 -> B (7 ngày timelock) hoặc 3 -> A (với điều kiện đưa ra số y mà Y = SHA256(y)).
- B tạo Tx2 với input là UTXO AB, output là: <br>
    1: pay 3 -> B, <br>
    2: 7 -> A (7 ngày timelock) hoặc 7 -> B (với điều kiện đưa ra số x mà X = SHA256(x)).
- A có thể post Tx2, đợi 7 ngày và lấy 7 BTC về.
- Giờ nếu A gửi 1BTC cho B offchain. A tạo x' và X' = SHA256(x'). Sau đó đưa X' cho B.
- A tạo Tx3 với input là UTXO AB, output là: <br>
    1: pay 6 -> A,  <br>
    2: 4 -> B (7 ngày timelock) hoặc 4 -> A (với điều kiện đưa ra số y mà Y = SHA256(y)).
- B tạo Tx4 với input là UTXO AB, output là: <br>
    1: pay 4 -> B, <br>
    2: 6 -> A (7 ngày timelock) hoặc 6 -> B (với điều kiện đưa ra số x' mà X' = SHA256(x')).
- Lúc này, A có thể post Tx3 và đợi 7 ngày. Nhưng nếu A post stale state là Tx2, B sẽ sử dụng x để lấy hết BTC. Do đó, A không thể gian lận và post lên trạng thái cũ.

## Multihop payments: Thanh toán nhiều lần
A muốn thanh toán cho C qua trung gian không tin cậy là B (Vì A, C có channel với B). 

### The lightning network
Nhiều open payment channel 2 chiều. Khi A muốn tạo kênh với B thì chỉ cần tìm tuyến đường qua đồ thị với đỉnh là các trung gian. 

## Kết luận
Đây là cách tiếp cận đầu tiên để tăng lượng Tx trong 1sec. Ở bài viết sau, mình sẽ giới thiệu các khác giúp giải quyết vấnd dề này.