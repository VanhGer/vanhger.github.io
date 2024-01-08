---
layout: post
title: Bitcoin Scripts và Ví
category: [blockchain, cs251]
tags: [knowledge]

---

Ở bài viết này, chúng ta sẽ đi sâu hơn về một số ví dụ của Bitcoin Script và các loại ví thường được sử dụng.

### Một số lưu ý
- Các Tx sau khi thêm vào khối sẽ không thể bị xoá, do đó khi Tx nhầm thì sẽ bị khoá hoặc mất tiền.
- Các TxID là độc nhất, vì Input của chúng là độc nhất. Có trường hợp ngoại lệ là các Coinbase vì chúng không có input. Do đó có thể có trùng TxID. Vì vậy, người ta thường thêm Block Height và mỗi ScriptPk của coinbase.

## Ví dụ về các loại Bitcoin Scripts
### Bảo vệ tài sản với co-signatory
addr = 2 - of - 2 (PkA, PkS) với PkS là của custody server.

### Escrow Service
Giả định: A muốn mua balo từ B với giá 0.1 BTC, nhưng A chỉ có thể trả tiền khi balo đến, và không thể không trả. Do vậy, addr có dạng: 2-of-3 (PkA, PkB, PkJ).

Cách vận hành: 
- A thông báo cho B rằng muốn mua balo với giá 0.1 BTC
- A post 1 Tx 0.11 BTC đến addr trên (0.01BTC để đảm bảo A sẽ trả tiền cho B).
- B khi thấy Tx, gửi balo cho A
- Khi A nhận, gửi SigA trong Tx trên cho B
- B sẽ sử dụng SigA và SigB để lấy số tiền mà A gửi lên Addr.

Nếu vi phạm xảy ra:
- B không đưa balo, A sẽ lấy lại tiền với sự trợ giúp của Judge (PkJ).
- A không đưa SigA, B sẽ lấy tiền với sự giúp đỡ của Judge, A mất thêm 0.01BTC.

## Ví
Ví giúp user tạo ra các cặp Pk- Sk, tạo ra và xác minh các Tx, đưa ra số dư mà user có. 

### Giúp đơn giản hoá xác minh thanh toán
Để xác minh một thanh toán đã thực hiện, ví thường tải các block headers. Sau đó, ví gửi lên server danh sách các địa chỉ mà ví đang nắm giữ. Sau đó, server sẽ gửi về ví các Tx liên quan đến địa chỉ và Merkle Proof của Tx đó. Từ đó giúp ví hiển thị đúng số dư và xác minh Tx.

### Vấn dề
- Để giải quyết được yếu tố bảo mật, blockheader sẽ được tải từ 10 servers khác nhau hoặc từ 1 nodes tin cậy.
- Remote server có thể kiểm tra xem addr gửi lên có thuộc về ví hay không.

### Các loại ví
#### Hot wallet
- Dạng ví điện tử có kết nối Internet, có nhiều chức năng như lưu trữ, gửi nhận Tx, tokens,...
- Có thể truy cập từ các thiết bị Internet như máy tính, điện thoại,..
- Có thể bị hack hoặc xâm nhập bởi các hacker.

### Cold wallet
- Không cho phép kết nối Internet.
- Có thiết bị riêng cho cold wallet, thiết bị này phải được khởi động bất cứ khi nào sử dụng và người dùng phải sao chép vật lý dữ liệu từ online device sang offline và ngược lại.

## Kết luận
Có nhiều loại Bitcoin Script khác như CrossChain,... được sử dụng rất phổ biến ở hiện tại.
