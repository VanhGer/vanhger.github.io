---
layout: post
title: Cấu tạo block và PoW
category: [blockchain, cs251]
tags: [knowledge]
postnum: 7

---

Trong bài viết ngày hôm nay, mình sẽ nói kĩ hơn về cấu tạo của block trong blockchain và cơ chế đồng thuận **PoW** (Proof of Work).

## Cấu tạo block trong bitcoin

Các block chứa các dữ liệu về Tx được lưu vào trong sổ cái công khai. Block có các header, nơi chứa các siêu dữ liệu giúp quản lý block đó. Kích thước blockheader là 80 bytes, trong khi các giao dịch trung bình khoảng 250 bytes và mỗi khối trung bình chứa được 500 Tx.

Ở trong blockheader chứa các dữ liệu liên quan đến các block đó:
- Đầu tiên, là giá trị hash của block liền trước nó (32 bytes), giúp đảm bảo liên kết trong chuỗi.
- Tiếp theo là Version (4 bytes) là phiên bản của block hiện tại. 
- Merkle Root cũng được lưu ở đây (32 bytes), là giá trị hash của của gốc cây Merkle của các Tx.
- 4 bytes Timestamp là chỉ thời gian block được tạo, được tính bằng số giây từ 01/01/1970. 
- Tiếp theo là Hash Target/ Difficult Target (4 bytes) là giá trị hash mục tiêu của PoW cho block này. 
- Cuối cùng là số nonce (4 bytes), là giá trị mà các miner tìm để giải bài toán PoW.

Minh hoạ:
<a class="post-image" >
<img itemprop="image"  src="/assets/images/post_img/post7_1.PNG"/>
</a>


## Proof of Work

PoW là một phần dữ liệu khó tạo (là đáp án cho một bài toán) hoặc rất tốn kém, mất thời gian để tạo nhưng chỉ tốn 1 thời gian rất ngắn (complexity **O(1)**) để có thể kiểm tra xem đáp án đó có đúng không. Quy trình tạo dữ liệu này là ngẫu nhiên với xác suất thấp, cần thử sau rất nhiều lần trước khi tạo ra PoW hợp lệ.

PoW là cách để các nodes cùng đồng thuận về tình trạng của block, tránh việc [chi tiêu kép](https://academy.binance.com/vi/articles/double-spending-explained), từ đó cho phép cập nhập blockchain theo các quy tắc của hệ thống.

### Ví dụ với Bitcoin

Trong quá trình tạo khối, các miner sẽ phải giải được bài toán tìm số <mark>nonce</mark> để đưa vào blockheader và khiến block hợp lệ. Block hợp lệ là block có **hash** nhỏ hơn **target value** hiện tại.
**Hash** của block là lấy tổng của: **Merkle Root, Version, Timestamp, Target, PreviousHash, Nonce** và hash 2 lần, nếu **Hash** này nhỏ hơn hoặc bằng **Target value**, tức là block này hợp lệ. Block này sau đó sẽ được đưa vào chuỗi sau khi các nodes khác verify rằng nonce này là hợp lệ (với độ phức tạp **O(1)**). Ngược lại, các miner sẽ phải tìm số Nonce khác.

Độ khó (**Target value**) sẽ được thay đổi sau 2016 block (2 tuần) được sinh ra. Nếu thời gian sinh 2016 block này nhanh hơn, Target sẽ được điều chỉnh xuống, ngược lại sẽ tăng lên. Sau khi cập nhập, các nodes cùng chia sẻ một Target.  

### Incentive

Sau khi tạo được block, miner sẽ nhận phần thưởng và Tx phí thông qua **coinbase** mà họ đưa vào block. <br>
Coinbase là giao dịch đầu tiên trong block, được tạo bởi miner nhằm mục đích nhận phần thưởng và Tx phí. Do đó, coinbase không có input, chỉ có output là địa chỉ ví của miner. Bitcoin nhận được từ coinbase không thể được sử dụng cho đến khi đạt 100 xác nhận trong blockchain.

## Kết luận

Ở trên là những hiểu biết của mình về cấu tạo chung của blockchain. Tuy nhiên mỗi loại blockchain sẽ có cấu tạo khác nhau một chút. Thêm vào đó, PoW là một cơ chế đồng thuận được sử dụng ở nhiều blockchain khác nhau, tuy nhiên nó cũng mang lại nhiều bất lợi. Những cơ chế đồng thuận khác sẽ được viết ở những bài sau.
