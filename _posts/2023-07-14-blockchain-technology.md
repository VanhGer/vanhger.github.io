---
layout: post
title: Kĩ thuật Blockchain
# feature-img: "assets/img/blur/bandb.png"
# img_title: "assets/images/title_img/post1.png"
# tags: [Instruction]
# author: vanhg
# permalink: /blog/2023/07/14/blockchain-technology

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
Hàm Hash H là một hàm mã hoá được sử dụng với ý nghĩa sau:
Với 2 tập hợp M và T có:  ![a](https://latex.codecogs.com/svg.image?%5Cleft%7CM%5Cright%7C%5Cgg%5Cleft%7CT%5Cright%7C) thì hàm hash H: ![a](https://latex.codecogs.com/svg.image?%5Cleft%7CM%5Cright%7C%5Crightarrow%5Cleft%7CT%5Cright%7C). Ví dụ khi input có giá trị lên đến cả megabyte, khi chuyển qua hàm hash H sẽ có dạng 32 bytes. ![a](https://latex.codecogs.com/svg.image?%5CLeftrightarrow%20T=%5Cleft%5C%7B0,1%5Cright%5C%7D%5E%7B256%7D)

Một hàm hash như trên gọi là chống trùng lặp nếu như rất khó để tìm ra 1 cặp (x, y) ∈ M sao cho x ≠ y mà H(x) = H(y). Hàm này còn được gọi là CRF (Collision Resistant Function).

### Commit dữ liệu lên blockchain
Nếu user A có file m:    ![a](https://latex.codecogs.com/svg.image?h=H(m)) là cam kết ràng buộc cho m. Với một danh sách ![a](https://latex.codecogs.com/svg.image?S=(m1,m2,...mn),h=commit(S)(h=H(S))). 
Khi user B đọc h, cho ![a](https://latex.codecogs.com/svg.image?(m_%7Bi%7D%5E%7B%7D,proof%5CPi%20_%7Bi%7D%5E%7B%7D)) có thể kiểm tra xem S[i] có bằng ![a](https://latex.codecogs.com/svg.image?m_%7Bi%7D%5E%7B%7D) hay không.
Điều này tương đương với khi chạy hàm ![a](https://latex.codecogs.com/svg.image?verify(h,i,m_%7Bi%7D%5E%7B%7D,%5CPi%20_%7Bi%7D%5E%7B%7D)) sẽ ra 1 trong 2 kết quả: accept/reject.

Ngoài ra, để đảm bảm security, không thể tìm được bộ ![a](https://latex.codecogs.com/svg.image?%5Csmall%20(S,i,m,%5CPi)) mà m ≠ S[i] và ![a](https://latex.codecogs.com/svg.image?verify(h,i,m_%7Bi%7D%5E%7B%7D,%5CPi%20_%7Bi%7D%5E%7B%7D)) = accept.

### Merkle Tree
Mục tiêu của Merkle tree là khi commit danh sách S ở trên, có thể chứng minh được mi = S[i]. 
Cây merkle được xây dựng như sau:

// Vẽ sau

Các phần tử, cha của chúng là H(nó, sibling kề nó). Ví dụ y1 là H(m1, m2). Bây giờ, với bài toán chứng minh ở trên, ví dụ cần chứng minh S[4] = m4, ta chỉ cần set Proof = (m3, y1, y6).
Lý do:

![a](https://latex.codecogs.com/svg.image?%5Csmall%20y2%5Cleftarrow%20H(m3,m4)%5C%5Cy5%5Cleftarrow%20H(y2,y1)%5C%5Ch'%5Cleftarrow%20H(y5,y6).)

Nếu h = h' thì accept. Khi muốn post 1 block vào trong chain, cần viết commit(S) vào. Merkle tree được sử dụng để chứng minh một Tx có ở trong block.


## Chữ kí số.
Làm cách nào mà các nodes có thể xác thực được các Tx? Thực tế, để xác định cần 3 thuật toán:
- Gen(): tạo ra cặp key  (pK, sK) cho user. 
- Sign(sK, msg): người gửi kí secretKey với msg họ muốn gửi. Output: là 1 sig: σ
- Verify(pK, msg, σ). Output: accept / reject

Khi tạo ra các Tx, user đưa Sk + msg để tạo ra chữ kí σ. Các verifier sẽ sử dụng pK, msg và σ để verify các Tx. Việc sử dụng chữ kí số này giúp đảm bảo uỷ quyền Tx, khả năng bầu cử và sự đồng thuận bỏ phiếu của user.

## Tạm kết
Ở phần này là những kĩ thuật phổ biến được sử dụng trong blockchain. Trong bài tiếp theo, mình sẽ đi sâu về cấu tạo của từng khối và cách hoạt động của PoW.