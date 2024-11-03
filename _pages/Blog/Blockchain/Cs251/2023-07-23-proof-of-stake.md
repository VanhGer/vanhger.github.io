---
title: Proof Of Stake
date: "2023-07-23"
tags: blockchain
thumbnail: "/assets/img/thumbnail/cs251.png"
postnum: 13
---
> Đây là kiến thức mình tìm hiểu được từ khóa học [CS251](https://cs251.stanford.edu/syllabus.html) của đại học Stanford. Mọi người có thể truy cập vào khóa học này để đọc thêm các tài liệu và bài giảng bằng tiếng Anh.

Ở bài viết này, mình sẽ giới thiệu một cơ chế đồng thuận đang được sử dụng rất nhiều trong các blockchain hiện nay, đó là Proof of Stake.

### Accountable Safety

Một giao thức với resilence là n/3 khi giao thức đó an toàn nếu nó có ít hơn n/3 Adverserial node (Ví dụ streamlet). <br>
Một giao thức với Accountable Safety resilence n/3 là giao thức:

- An toàn nếu ít hơn Adverserial node.
- Nếu vi phạm safety, thì những người giám sát (các node không phải người được chọn để đề xuất khối) có thể xác định rõ các node vi phạm.
- Không có cáo buộc sai với các node trung thực.

### Finality và Dynamic availability

Gọi một giao thức là finality nếu nó duy trì safety trong thời gian không đồng bộ (trước GST). Ở các giao thức này, Tx được finalize nhanh hơn confirm trong bitcoin (60 phút). <br>
Gọi một giao thức là Dynamic availability nếu giao thức đó có thể tiếp tục confirm các giao dịch ngay cả khi nhiều node offline.

Không có giao thức SMR nào đảm bảo cung cấp cả 2. Do đó, một cách giải quyết là sử dụng Nested chains.

### Nested chains

- Available chain được xác định bởi giao thức Π_ava, thoả mãn Dynamic availability (VD: Nakamoto consensus).
- Finalized chain được xác định bởi giao thức checkpoint $Π_{fin}$ thoả mãn security dưới mạng đồng bộ 1 phần.
- Chuỗi confirm bởi Π_ava là available chain.
- $Π_{fin}$ kiểm tra các block trong availbale chain.
- Tiền tố của checkpoint cuối cùng tạo thành finalized chain. Tương đương với Finalized chain là prefix của available. Chuỗi này safe dưới mạng bất đồng bộ
- Các block của available chain luôn extend từ điểm checkpoint cuối cùng.

Minh hoạ:
<a class="post-image" >
	<img itemprop="image"  src="/assets/img/post_img/post13_1.PNG"/>
</a>

## Proof of Stake

### Tổng quan

- Trong giao thức Pos, các nodes khoá (Stake) tiền của họ trong giao thức để đủ điều kiện tham gia consensus.
- Càng nhiều coin được stake bởi 1 node thì khả năng cao node đó được chọn làm validator. Vì thuật toán lựa chọn validator là giả ngẫu nhiên.
- Nếu node bị bắt quả tang thực hiện 1 hành động bất lợi (xác thực giao dịch sai) thì node đó sẽ bị burn số tiền đã stake và không thể tham gia vào quá trình đề xuất khối tiếp theo. (Slashing).

Do đó, trong PoS, nodes sẽ có trách nhiệm trong cách hành động của mình. PoS giúp thay đổi cơ chế block được verify. Ngoài ra, để trở thành validator, các node phải có điều kiện (ví dụ: có ít nhất 32 ETH,..), sau khi đóng 1 khối, khối đó sẽ được các validator khác validate theo nhiều method khác nhau tuỳ vào blockchain.

### Cụ thể với Ethereum 2.0

#### Chọn Validator và Block Proposer

Để thành một validator, phải gửi ít nhất 32 ETH vào contract và chạy 3 phần mềm riêng biệt (sẽ giới thiệu ở sau) và tham gia hàng đợi. Thời gian của blocks chia thành các slot: 12s, epoch: 32 slot. Sau khi được active, validator đó sẽ tham ra vào quá trình phê duyệt. 1 validator ngẫu nhiên sẽ được chọn để xác thực giao dịch và đề xuất khối ở mỗi slot (Block proposer). Số còn lại sẽ làm giám sát để phê duyệt và kiểm tra xem validator đó có gian lận không.

Chọn Block Proposer quá trình giả ngẫu nhiên gồm nhiều yêu tố như staking age, số tiền stake,... Ở đây, mình sẽ giới thiệu 2 phương pháp lựa chọn là:

- <strong> Randomize block selection: </strong> lựa chọn bằng cách tìm kiếm nodes có sự kết hợp của hash value thấp nhất và stake cao nhất. Vì size của các stakes là công khai nên có thể dự đoán được.
- <strong> Coin Age Selection: </strong> là phương thức chọn các nodes dựa vào khoảng thời gian stake. Coin Age được tính bằng: số ngày đặt cược x số lượng coin. Khi một node forge một block, coin age của nó được cài về 0.

#### Quy trình xác thực

Khi một user tạo Tx và submit lên Ethereum, nó sẽ được kiểm tra tính hợp lệ (Ví dụ đảm bảo số tiền, key đúng,...) sau đó được đưa đến mempool và phát tán nó đến các node khác. <br>
Một node được chọn làm Block Proposer để đề xuất khối ở slot này, có trách nhiệm đóng và phát tán block vào chuỗi và cập nhập trạng thái. Nodes chạy 3 phần mềm:

- **Execution client**: gộp các Txs từ mempool thành "execution payload" và chạy nó ở local để tạo ra sự thay đổi trạng thái. Thông tin này được chuyển đến consensus client
- **Consensus client**: đóng gói payload thành 1 phần của "beacon block" (gồm payload, slashing,...) cho phép mạng thống nhất trình tự về các khối.
- **Validator client**: Các node nhận được beacon block qua tầng gossip network. Sau đó chuyển đến Execution client của nó để chạy lại, nhằm chắc chắn trạng thái là đúng. Sau đóm Validator client sẽ chứng thực khối đó hợp lệ và là khối tiếp theo trong góc nhìn của chuỗi (available chain). Khối cũng được thêm vào cơ sở dữ liệu local của các node.

#### Finality

Như đã nói ở trên, các block của available chain luôn extend từ điểm checkpoint cuối cùng. Checkpoint là các block đầu tiên ở mỗi epoch. Các validators có thể vote cho cặp checkpoint (1 epoch), nếu ít nhất 2/3 vote cho nó, cặp checkpoint này sẽ được cập nhập, và các block nằm giữa sẽ được cập nhập thành finalized chain. Nếu chuỗi không thể finalized trong nhiều hơn 4 epoch liên tục, stake ETH từ các validator chống lại đa số sẽ bị biến mất, cho phép đa số chiếm hơn 2/3 và finalized chuỗi.

## Lời kết

Ở trên là cách hoạt động cơ bản của POS. Mọi người có thể xem thêm các thông tin về POS trong Ethereum tại [đây](https://ethereum.org/vi/developers/docs/consensus-mechanisms/pos/).
