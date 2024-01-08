---
layout: post

title: Mở rộng Blockchain (Phần 2)
category: [blockchain, cs251]
tags: [knowledge]

---

Trong bài viết hôm nay, mình sẽ giới thiệu đến mọi người một kĩ thuật giúp tăng tốc độ các Tx trong mạng blockchain. Đó là Rollups.

## Ý tưởng
Trong layer-1 của blockchain như Ethereum chứa world state. Khi một giao dịch được đưa vào, world state sẽ bị thay đổi. Từ đây, ý tưởng cơ bản của Rollup là hợp hàng trăm Tx lại thành một Tx duy nhất, từ đó giúp tăng tốc độ lên khoảng 100 lần.

Ta có hình minh hoạ như sau:


Từ đây, ta gọi Rollup state là L2, để phân biệt với L1 (layer-1 của blockchain). <br>
Rollup contract ở L1 sẽ giữ các tài sản và Merkle state root của các Rollup account. Rollup state chỉ bao gồm số dư của các Rollup account. 

/// hình ảnh

## Hiệu năng
<strong> Chuyển tiền trong 1 Rollup </strong>
- Chuyển tiền trong 1 Rollup (L2 -> L2) rất dễ dàng. Các coordinators chỉ cần cập nhập lại Merkle Root của Rollup Contract trên L1 là xong. 

<strong> Chuyển tiền từ funds đến Rollup </strong>
- Chuyển tiền từ funds đến Rollup (L1 -> L2) thì chậm và đắt. Lý do vì cần phải update các state trong L1, số dư trong contract và cả trong Rollup và cập nhập lại Root của contract đó.

<strong> Chuyển tiền ra khỏi Rollup </strong>
- Chuyển tiền ra khỏi Rollup cần phí gas thêm từ L1 trong quá trình chuyển vì cần post nhiều dữ liệu. 

<strong> Chuyển tiền từ 2 Rollup </strong>
- Nếu chuyển tiền từ 2 Rollup (L2 -> L2') mà qua L1 thì sẽ rất đắt, nhưng nếu qua 1 cầu trực tiếp L2 <=> L2 thì sẽ rẻ.

### Chạy contract trong Rollup
Khi contract chạy trong Rollup (Ví dụ Uniswap), thì việc tương tác với contract đó sẽ rẩt rẻ cho người dùng. <br>
<strong> Coordinator </strong> duy trì trạng thái của tất cả contracts trong hệ thống Rollup:
- Nó cập nhập Uniswap Merkle Leaf mỗi khi 1 Tx nào đó đến Uniswap
- Ghi update cho Rollup state root trên L1.

Rollup có chức năng giống Ethereum, nhưng không có cơ chế đồng thuận vì nó dựa vào L1 để chứng thực trạng thái hiện tại.

## Các vấn đề
<strong> Vấn đề 1 </strong>: Điều gì xảy ra nếu Coordinator không trung thực. Nó có thể lấy hết tiền trong Rollup hoặc tạo các giao dịch giả.

<strong> Vấn đề 2 </strong>: Nếu Coordinator ngừng cung cấp dịch vụ ? Làm sao để lấy lại trạng thái Rollup để đưa cho Coordinator mới.

## Các phương án giải quyết
### Vấn đề 1: Coordinator không trung thực
Coordinator không thể lấy tiền từ Rollup users, vì L1 sẽ xác minh update trạng thái của Rollup bằng cách kiểm tra xem tất cả các Tx có valid và được ký hợp lệ bởi người dùng hay không. <br>
Để làm điều này một cách rẻ, có thể có các cách tiếp cận sau:

#### Validity Proofs (còn gọi là zk-Rollup)
Khi coordinator gửi updated root và Tx List cho L1, nó cần gửi kèm theo một SNARK proof cho valid Tx để chứng minh tập hàng trăm các Tx này là hợp lệ. <br>
<strong> SNARK proof </strong> là loại proof ngắn và nhanh để xác minh, từ đó giúp việc xác minh trên L1 trở nên rẻ. (với sự giúp đỡ của EVM)  <br>
Bao gồm:
- Public statement:   (old state root,  new state root,  Tx list)
- Witness: (state trước - sau của những tài khoản bị thay đổi, bằng chứng Merkle, chữ kí của user)
- SNARK proof sẽ chứng minh rằng: 
    + Tất cả chữ ký trên đều valid
    + Tất cả bằng chứng Merkle đều valid
    + Trạng thái sau = trạng thái trước + Tx. 

<strong> zkEVM </strong>  <br>
Khi một contract (Uniswap) chạy trong Rollup:
- Coordinator sẽ tạo 1 SNARK proof về việc thực hiện đúng 1 chương trình EVM. Đó gọi là zkEVM. 
- Tạo bằng chứng thì sẽ yêu cầu rất cao về tính toán nhưng khi xác minh thì lại rất nhanh.

Có 2 chức năng của zkEVM là:
- Chứng minh EVM bytecode chạy đúng
- Biên dịch Solidity to SNARK-friendly circuit. 

<strong> Kết quả </strong>
- Rollup contract đảm bảo coordinator không thể gian lận và sẽ chấp nhận các update nếu có proof phù hợp
- Ai cũng có thể làm coordinator nếu có đủ sức mạnh tính toán

#### Fraud proof (còn gọi là Optimistic Rollup)
- Coordinator sẽ stake 1 khoản vào L1 Rollup Contract
- Coordinator sẽ đưa updated root lên L1 mà không cần bằng chứng
- Nếu update không valid, trong vòng 7 ngày ai cũng có thể chứng minh điều này bằng việc gửi fraud proof.
- Nếu fraud proof là đúng, coordinator sẽ bị Slash, ngược lại người gửi proof sẽ tốn phí. 

Để chứng minh Fraud đến Rollup contract trong L1, ta sẽ sử dụng Binary Search. <br>
Việc Coordinator và Người gửi Proof (A) tạo ra state(n) và state(n') khác nhau buộc L1 cần kiểm tra xem ai mới là người đúng. Do đó, sử dụng Merkle tree và tìm kiếm nhị phân, với việc chia đôi từ state(n/2), state(n/4)... có thể dễ dàng tìm được xem state nào bị sai sau Log2(n) round. 

Tuy nhiên, cách này gặp 1 số khó khăn như:
- Giao dịch chỉ giải quyết sau 7 ngày (Sau khi hết hạn gian lận)
- Khi 1 bằng chứng Fraud được chấp nhận, các Tx sau phải được gửi lại

<strong> Kết quả </strong>
- Có thể dễ dàng đưa smartcontract vào optimistic Rollup
- Thông lượng Tx cao: ~ 4000Tx / s
- Ai cũng có thể làm coordinator và verifier
- Giải quyết giao dịch chậm 7 ngày.


### Vấn đề 2: Coordinator ngừng cung cấp dịch vụ
Cần tạo Coordinator mới, nhưng lại cần trạng thái mới nhất của Rollup.

Ta thấy, Rollup state có thể phục hồi từ dữ liệu trong L1 bằng việc đọc các msg và re-excecute Tx. Nhưng việc này sẽ rất đắt vì tương tác nhiều trên L1 và cần nhiều dữ liệu. 

Để giảm Tx fees:
- Lưu L2 state root trong L1
- Lưu Tx data với Data Availability Committee (DAC):
    + Gồm tập hợp các node tin cậy giữ cho data available
    + Rẻ hơn lưu ở L1
    + L1 chấp nhận update khi và chỉ khi tất cả các node trong DAC đều kí. Từ đó giúp đảm bảo các member trong DAC đều chấp nhập dữ liệu Tx. 
- Tạo 1 coordinator mới phụ thuộc vào tính khả dụng của DAC

#### Validium
1 L2 sử dụng DAC để lưu trữ và Validity proof (SNARKs):
- Phù hợp cho tài sản giá trị thấp
- Chỉ các thành viên DAC mới thấy được dữ liệu

#### Điều gì xảy ra nếu Coordinator từ chối 1 Tx
- Người tạo có thể post Tx trực tiếp lên L1 Rollup contract. 
- L1 Rollup contract chỉ chấp nhận update khi mà update có cả Tx trên. Từ đó dẫn đến toàn bộ Rollup bị đóng băng.

## Lời kết
Ở trên đây là các cách được áp dụng để tăng tốc độ Tx trong blockchain.
