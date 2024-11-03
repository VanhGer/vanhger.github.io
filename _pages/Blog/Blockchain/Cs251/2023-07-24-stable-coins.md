---
title: Stablecoins
date: "2023-07-24"
tags:
  - blockchain
thumbnail: "/assets/img/thumbnail/cs251.png"
postnum: 15
---
> Đây là kiến thức mình tìm hiểu được từ khóa học [CS251](https://cs251.stanford.edu/syllabus.html) của đại học Stanford. Mọi người có thể truy cập vào khóa học này để đọc thêm các tài liệu và bài giảng bằng tiếng Anh.

Stable coin là 1 loại tiền điện tử được tạo để giao dịch với một giá trị không đổi. Mục đích sinh ra Stablecoin là tích hợp tiền tệ trên thế giới thật vào các ứng dụng trên chuỗi và cho phép những người không thể có USD nắm giữ và giao dịch 1 tài sản tương đương.

## Custodial Stablecoin: Stablecoin kí gửi

Custodian giữ kho bạc trong 1 ngân hàng truyền thống, mọi giao dịch đều thông qua nó. Ngoài ra, nó có quyền mạnh mẽ như kiểm duyệt khác hàng rút tiền hoặc xoá số dư người dùng. Điều này sẽ nguy hiểm nếu chẳng may bị hack hoặc nhầm lẫn và không đảm bảo sự phi tập trung. Do đó, cách này không được ưu tiên và sử dụng ở thực tế.

## Synthetic

Mục tiêu là xây dựng 1 loại non-custodial stablecoin. Tuy nhiên, có một vấn đề là ETH thường xuyên dao động, không ổn định so với USD.<br>
<strong> Maker DAO: </strong>  xây dựhng một stablecoin từ tài sản không ổn định. <br>
Hệ thống MakerDAO:

+ DAI: stablecoin (giá: 0.99 ~ 1.01 USD)
+ MKR:
  + ai cũng có thể mua để kiếm lãi.
  + Sử dụng để quản trị, ổn định giá DAI trong trường hợp khẩn cấp.

### Đúc DAI

Giả sử: A muốn trả B bằng DAI nhưng A chỉ có 1 ETH. A sẽ tạo 1 vault trên MakerDAO contract:
+ 1 wallet: tài sản mà A kiểm soát.
+ 1 vault: tài sản mà A khoá để vay DAI.

Ta có:
+ Ban đầu, A có: 1ETH, 0DAI trong wallet, 0ETH, 0DAI trong vault.
+ Khi A khoá 1 ETH vào vault: trong wallet sẽ không còn ETH, trong vault có 1ETH
+ Việc khoá ETH xem như một cách thế chấp để vay DAI. Vì số lượng thế chấp = 130% số lượng tối đa có thể vay, nên khi A thế chấp 1 ETH (~3000USD) thì chỉ có thể vay tối đa 2300 DAI.
+ Do đó, trong wallet A có 2300DAI, còn vault: 1ETH, -2300DAI.
+ A trả cho B bằng DAI, và có thể trả nợ cho vault để lấy lại 1 ETH.

### Stabilization

A khi thế chấp ETH và vay DAI, phải trả lãi: Phí ổn định. Hầu hết các phí đều đưa cho DAI holders (thông qua DSR), một số còn lại đưa cho MKR holders.

<strong> The DAI saving rate (DSR)</strong> <br>
Bất kì ai giữ DAI có thể khoá DAI của mình trong MakerDAO contract. DSR là tỉ lệ lãi từ DAI đã khoá ở trên contract. Users có thể rút DAI ra từ contract bất cứ lúc nào.

<strong> Cơ chế ổn định: </strong> <br>
+ DAI có giá trị < 1$  ⇒ tăng phí ổn định và DSR:
  + Người đúc DAI được khuyến khích hoàn trả món nợ đã vay sớm vì lãi đang tăng
  + Khuyến khích DAI holders gửi nhiều DAI vào contract
  + Từ đó giúp giảm nguồnn cùng DAI, giúp DAI tăng giá
+ DAI có giá trị > 1$  ⇒ giảm phí ổn định và DSR.

### Thanh toán

Nếu vault debt vượt quá 130% (do lãi), thì tài sản thế chấp sẽ được bán đấu giá. (Tiền lãi - phí) sẽ được trả nợ vault cho A đến khi đạt dưới 130%.

## NFTs

NFTs là:
+ Quyền sở hữu token của 1 tài sản kĩ thuật số (như Digital Artwork, vitual games item,...).
+ Không thể có NFT nào giống nhau, nên không thể trao đổi lẫn nhau.
+ Được xác định bởi lịch sử, mức độ tiện ích, tầm quan trọng,...

NFTs được quản lý dưới blockchain vì:
+ Blockchain đảm bảo quyền sở hữu dài hạn cho đến khi bán.
+ Cung cấp một hồ sơ đáng tin cậy về xuất xứ (chống giả mạo).

### Sở hữu tài sản kĩ thuật số

NFTs hoạt động như một chứng thư pháp lý:
+ Có thể chuyển quyền sở hữu từ bên này sang bên khác
+ Quyền sỏ hữu thể hiện tình trạng sở hữu tài sản, cấp quyền hợp pháp,...

### Nhận NFTs như thế nào

Có 2 cách chính là tìm người sở hữu và mua từ học, hoặc lên thị trường và mua.

### Thị trường NFT

Được xây dựng như một tập các smartcontract, thành một hệ thống tài sản và trao đổi. NFT mang lại sự sở hữu đích thực trên Internet, kích hoạt thương mại kĩ thuật số,... Ví dụ: một số NFT đặc biệt mang lại cho người dùng sự truy cập vào một số IP, trang trên Internet.

### NFT cost issue

Đa phần các NFT đều có giá cao, do đó muốn sở hữu thì có thể thuê hoặc tiết kiệm. Sẽ khó bắt đầu kinh doanh NFT nếu không có tiền mặt hoặc nguồn cung cấp đủ lớn.

Một số dịch vụ dựa trên NFTs như: Gameing guild (các item trong game: NFTs), EveryRealm (Bất động sản ảo), Credit Provider,...
