---

title: Kĩ thuật Blockchain
# feature-img: "assets/img/blur/bandb.png"
# img_title: "assets/images/title_img/post1.png"
# tags: [Instruction]
author: vanhg
permalink: /blog/2023/07/14/blockchain-technology

---

Blockchain cung cấp sự tin cây cho nhiều bên (mà không cần đến trung gian), đây là ứng dụng có thể xem là mạnh mẽ nhất của blockchain. Dưới đây, mình sẽ viết sâu hơn về các kĩ thuật được sử dụng trong blockchain.

## Các tầng của Blockchain
Blockchain có thể chia thành các tầng chính như sau:
#### Application layer: 
Bao gồm các DApps, Smartcontract, các phần mềm chạy trên đỉnh các network. Đây là tầng cho phép build các Apps và Service phục vụ người dùng.

#### Consensus layer:
Là tầng mà các nodes đồng ý về tính hợp lệ của các giao dịch, dựa trên các cơ chế đồng thuận.

#### Network layer:
Là tầng mà các nodes giao tiếp với nhau (P2P). Tầng này phục vụ việc kết nối, lan truyền các Tx và phân phối các dữ liệu trên mạng. Đây cũng là tầng mà các block được tạo ra và thêm vào chuỗi.

#### Data layer:
Là tầng lưu trữ dữ liệu an toàn và bất biến. Các dữ liệu bao gồm sổ cái, cơ sở dữ liệu trạng thái (Hash, Merkle Tree, Tx, Chữ ký,..).

#### Hardware layer:
Là tầng vật lý, các thiết bị như máy tính, servers, routers,...

Ở tầng Consensus, các nodes phải đảm bảo các tính chất sau:
+ Persistence: Khi các Tx và block được thêm vào chuỗi, không thể xoá bỏ
+ Safety: Các thành viên tham gia (các nodes, người dùng) đều có chung 1 dữ liệu về sổ cái
+ Liveness: các thành viên Honest (không phá hoại mạng blockchain) thêm được các giao dịch
+ Open: Ai cũng có thể thêm các giao dịch.

Cùng với đó, có những vấn đề xảy ra ở tầng Consensus này cần giải quyết đó chính là việc Network delays, Network Partition, Crash, Malice,.. dẫn đến danh sách các Tx có thể bị sai thứ tự, không đầy đủ,...

## Mã hoá
