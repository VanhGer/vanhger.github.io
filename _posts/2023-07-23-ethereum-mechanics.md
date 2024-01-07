---
layout: post
title: Cơ chế Ethereum
# feature-img: "assets/img/blur/bandb.png"
# img_title: "assets/images/title_img/post1.png"
# tags: [Instruction]
# author: vanhg
# permalink: /blog/2023/07/23/ethereum-mechanics

---

Mặc dù bitcoin được ra đời từ rất sớm và được sử dụng cho đến hiện tại, nó vẫn chứa nhiều nhược điểm. Phần chính là các ScriptPk của UTXO không thể thực hiện các quy tắc phức tạp về tài sản. Ví dụ khi muốn đưa ra giới hạn lượng BTC sử dụng trong 1 ngày là 2BTC, không thể làm với UTXO Script được. Do đó, Ethereum ra đời với nhiều ưu điểm vượt trội

### So sánh chung
Bitcoin như một máy chuyển trạng thái. Bitcoin rule: S x I ⇾ S, với tập S là các trạng thái, I là tập tất cả các input. Còn với Ethereum, hàm chuyển trạng thái của nó sẽ phong phú hơn nhiều, và mỗi lần chuyển đổi thì sẽ thực hiện toàn bộ chương trình. 

Ethereum hỗ trợ các DApp, với Program code được lưu trữ trên blockchain, khi chạy cũng sẽ thay đổi các trạng thái. Các block thì sẽ có khoảng 150 Tx, và các Block Proposer sẽ nhận Tx fee cho block và các phần thưởng khác khi tạo khối thành công. 

Ngoài ra, ta đã biết rằng khi các block được proposer trong consensus layer (các beacon block, nên còn được gọi là beacon chain), các node gửi các Tx lên cho execution client hay còn gọi là compute layer để cập nhập các trạng thái (update world state).
## Compute layer
World state: tập hợp các tài khoản được xác định bởi các địa chỉ 32 byte (Các thông tin như địa chỉ, số dư,..)

Có 2 loại tài khoản:
- Owned account: Điều khiển bởi các cặp (Pk, Sk), hay là tài khoản của người dùng.
- Contracts: Điều khiển bởi code, được tạo lúc tài khoản được tạo, không thể thay đổi.

Các dữ liệu cần thiết với mỗi account:

| Account Data   |   Owned     |Contracts                      |
|----------------|-------------|-------------------------------|
| address(tính)  |   H(Pk)     |   H(CreatorAdd, CreatorNonce) |
| storage root   | Không       |   StorageRoot                 |
| code           | không       |   CodeHash                    |
| balance        | balance     |   balance                     |
| nonce          | nonce       |   nonce                       |

Trong đó, nonce là số Tx mà acount đã thực hành, tính bằng (#Tx sent) + (#accounts created). Số nonce được dùng để chống replay và khi tạo mới thì luôn bằng 0 (với contract).  Các contract có thể bị ghi đè code, nhưng chỉ khi contract cũ đã SELFDESTRUCT.   

Mỗi contract có 1 mảng lưu trữ liên quan gồm 2^256 phần tử, mỗi phần tử là 32 bytes. Và storage root là Merkle Patricia hash của mảng.

### Chuyển trạng thái
#### Tx
- Được kí bởi người tạo.
- To: nếu = 0 là tạo account mới, code = (init, body). Còn lại là 32-bytes địa chỉ và data (những thứ mà contract gọi và các đối số).
- From: Địa chỉ khởi tạo và chữ kí vào Tx
- Value: số Ethereum được gửi.
- Tx fees: gasLimit, maxFee, maxPriorityFee (Sẽ nói rõ hơn ở phần sau).
- Nonce: Khớp với nonce hiện tại của người gửi, tránh việc replay Tx.
- Chain-id: đảm bảo giao dịch gửi đúng chuỗi (không thể dùng giao dịch testnet lên mainnet).

#### Các loại Tx
- owned ⇾ owned: chuyển ETH giữa các user
- owned ⇾ contract: gọi contract với ETH & data

#### Messages
Là các Tx ảo được contract tạo ra, nó không có Signature (vì contract không có). Có 2 loại là:
- contract ⇾ onwed: contract gửi tiền cho user
- contract ⇾ contract: contract gọi contract khác.

### Ethereum block 
Khác với Block của Bitcoin, Block Header của Ethereum có nhiều loại dữ liệu hơn, một số chính như:
- Consensus data: các dữ liệu như Proposer Id, Previous hash, vote,...
- Address: Nơi mà gas fee được chuyển đến
- World state root: Merkle Patricia Tree Hash của tất cả các tài khoản
- Tx root: Merkle Tree Hash của Tx
- Tx receipt root: Merkle Hash của các log message
- Gas used: Sử dụng để điều chỉnh giá gas (ở phần sau).

### EVM mechanics
EVM là môi trường thực thi (execution). Các contract được viết bằng solidity, sau đó sẽ được compile thành EVM bytecode và được các validator sử dụng để chạy các contract. <br>
EVM hoạt động như một [Stack Machine] (https://en.wikipedia.org/wiki/Stack_machine) với lệnh JUMP (Các program sẽ bị huỷ nếu quá stack, proposer giữ lại gas).

### Gas
Gas được xem như là nguyên liệu của Ethereum, là khoản phí cần trả để thực hiện các giao dịch hay hoạt động tương tác với smartcontract. Mọi chỉ thị đều tốn gas, giúp ngăn việc gửi Tx có nhiều bước (tốn nhiều tiền hơn). Các Proposer có thể chọn các Tx từ mempool có phí gas cao để tối đa hoá thu nhập.

#### Gas calculation:
Mỗi block có: 
- baseFee: gasPrice nhỏ nhất cho tất cả Tx trong block. BaseFee dựa vào tổng số gas ở block trước (Gas used ở header), nếu gasUsed là giới hạn (30M) thì baseFee tăng 12.5%, nếu block trước trống thì giảm 12.5%. Còn lại thì giữ nguyên. 
- gasLimit: tổng gas cho phép với Tx
- maxFee: giá gas tối đa cho phép
- maxPriorityFee: 'tips' tối đa cho Proposer.

GasPrice = min(maxFee, baseFee + maxPriorityFee) <br>
MaxTxFee = gasLimit x gasPice <br>
<strong> Cụ thể </strong>
- nếu gasPrice < baseFee: dừng
- nếu MaxTxFee > sender.balance: dừng
- trừ MaxTxFee khỏi sender.balance
- Đặt Gas = gasLimit
- Chạy Tx, trừ gas từ Gas với mỗi Tx. Nếu Gas < 0 thì dừng, Proposer giữ gasLimit * gasPrice.
- Xong xuôi, trả lại Gas x gasPrice cho sender
- gasUsed = gasLimit - Gas. BURN gasUsed x baseFee và gửi gasUsed x (GasPrice - baseFee) cho block proposer. 

ETH bị BURN vì giúp giảm lạm phát, tăng tính khan hiếm, có lợi cho nhà đầu tư, chặn các proposer tạo ra các Tx giả, không khuyến khích các thoả thuận ngoài chuỗi.

## Kết luận
Ở trên là toàn bộ hiểu biết của mình về cơ chế Ethereum.
