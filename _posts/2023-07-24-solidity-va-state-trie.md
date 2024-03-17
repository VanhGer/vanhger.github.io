---
layout: post

title: Solidity và State Trie
category: [cs251]
postnum: 14
---

Ở bài viết này, mình sẽ giới thiệu về Solidity và World State Trie

## Solidity

Solidity là ngôn ngữ lập trình cho Blockchain Ethereum, là ngôn ngữ hướng đối tượng, cấp cao để triển khai các smartcontracts. Smartcontract là chương trình chi phối các hành vi của Account trong Ethereum State. Account contract được tạo ra khi chạy một smartcontract. Mọi người có thể đọc account contract state ở trong storage array, nên không bao giờ được lưu trữ các bí mật trong contract.

Một số dạng biến của Solidity như: uint256, address(byte 32), bool,... Các loại tham chiếu là mảng, struct, string, map,... Khi một giao dịch đi từ A ⇾ B ⇾ C ⇾ D thì tại D, msg.sender là C, nhưng tx.origin là A.

ERC20 là API tiêu chuẩn cho fungible token, cung cấp các chức năng cơ bản để chuyển token hoặc cho phép token được sử dụng bởi bên thứ 3.

Các kiểu lưu trữ:

- Stack variables: Rẻ để sử dụng, phù hợp với mọi loại dữ liệu (không qua 32 bytes)
- Calldata: Là 1 mảng byte chỉ đọc, tốn gas
- Memory: 1 byte mảng, rẻ, nhưng chi phí tăng theo cấp số nhân, lưu được dữ liệu > 32 bytes
- Storage: Đắt, mappings và các biến state lưu trong này.
- Event logs: rẻ, không cần truy cập đến contract.

Ở trên là một số hiểu biết của mình về solidity, về mặt coding, mọi người nên xem ở [đây](https://docs.soliditylang.org/en/latest/).

## State trie (Ethereum state trie)

Hình ảnh đây là cấu trúc được tạo bởi các loại State trong Ethereum: (ở góc phải dưới hình ảnh)

<img src="https://i.stack.imgur.com/afWDt.jpg"  width="900" height=" 600">

Có 3 loại state chính là World state, Transaction, và Transaction Receipt.

### World State Trie

World State Trie Là mapping giữa địa chỉ và Account State. Nó được update bởi các Tx, và lưu trữ mọi thông tin về accounts và có thể lấy được qua các truy vấn.
Trong World State Trie có Account Storage Trie, nơi các dữ liệu liên kết với account được lưu trữ. Nó chỉ liên quan đến các contract account và mọi dữ liệu được ánh xạ giữa các số nguyên 32 byte.

Account State là các thông tin về một Ethereum account, như balance, nonce, storage Root, codehash,... và là lá của World State Trie.

### Transaction Trie

Transaction Trie lưu trữ các Tx trong Ethereum. Khi các Tx lưu trữ trong block, nó không thể bị thay đổi. Nó được xây dựng theo cấu trúc Modified Merkel Patricia Trie, và chỉ có node root mới được đưa vào trong block.

### Transaction Receipt Trie(Receipt Trie)

Transaction Receipt Trie lưu trữ đầu ra của các Txs. Đầu ra là kết quả của một Tx mà được chạy thành công, bao gồm H(Txs), block number, gas Used, địa chỉ của contract.

### Ví dụ cấu trúc của World State (một cách đơn giản)

Để đơn giản, ta giả sử các node lá của World State Trie chỉ bao gồm địa chỉ là số dư theo cặp Key - Value.
Các loại thông tin:

- Leaf Node: node lá của cây, chứa các trạng thái của 1 tài khoản có địa chỉ là Key
- Branch Node: gồm 16 ô, là các tiền tố của Key.
- Extension Node: node chỉ có 1 child
- Nibble: Là phần chung trong Key, 1 Nibble = 4 bits, lưu trong keyend hoặc shared nibble.
- Prefixes:
  - 0: extension node có số chẵn Nibble
  - 1: extension node có số lẻ Niblle
  - 2: leaf node, có số chẵn Nibble
  - 3: leaf node, có số lẻ Nibble.

Minh Hoạ:
World State Trie với 4 cặp Key - Value như sau:

- a711355: 45 ETH
- a77d337: 1 WEI
- a7f9365: 1.1 ETH
- a77d397: 0.12 ETH

<img src = "https://miro.medium.com/v2/resize:fit:1400/format:webp/1*SiSYWqaPcJtBlnFR4VAaug.png">

## Kêt luận

Ở trên là một số khái niệm sơ bộ về Solidity và cấu trúc của Ethereum, cụ thể về các State Trie.
