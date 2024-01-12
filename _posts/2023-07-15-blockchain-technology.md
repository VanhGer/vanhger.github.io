---
layout: post
title: Kĩ thuật Blockchain
category: [blockchain, cs251]
tags: [knowledge]
postnum: 6
---

Blockchain cung cấp sự tin cây cho nhiều bên (mà không cần đến trung gian), đây là ứng dụng có thể xem là mạnh mẽ nhất của blockchain. Dưới đây, mình sẽ viết sâu hơn về các kĩ thuật được sử dụng trong blockchain.

## Các tầng của Blockchain

Blockchain có thể chia thành các tầng chính như sau:

#### Application layer

Bao gồm các DApps, Smartcontract, các phần mềm chạy trên đỉnh các network. Đây là tầng cho phép build các Apps và Service phục vụ người dùng.

#### Consensus layer

Là tầng mà các nodes đồng ý về tính hợp lệ của các giao dịch, dựa trên các cơ chế đồng thuận.

#### Network layer

Là tầng mà các nodes giao tiếp với nhau (P2P). Tầng này phục vụ việc kết nối, lan truyền các Tx và phân phối các dữ liệu trên mạng. Đây cũng là tầng mà các block được tạo ra và thêm vào chuỗi.

#### Data layer

Là tầng lưu trữ dữ liệu an toàn và bất biến. Các dữ liệu bao gồm sổ cái, cơ sở dữ liệu trạng thái (Hash, Merkle Tree, Tx, Chữ ký,..).

#### Hardware layer

Là tầng vật lý, các thiết bị như máy tính, servers, routers,...

Ở tầng Consensus, các nodes phải đảm bảo các tính chất sau:

+ Persistence: Khi các Tx và block được thêm vào chuỗi, không thể xoá bỏ
+ Safety: Các thành viên tham gia (các nodes, người dùng) đều có chung 1 dữ liệu về sổ cái
+ Liveness: các thành viên Honest (không phá hoại mạng blockchain) thêm được các giao dịch
+ Open: Ai cũng có thể thêm các giao dịch.

Cùng với đó, có những vấn đề xảy ra ở tầng Consensus này cần giải quyết đó chính là việc Network delays, Network Partition, Crash, Malice,.. dẫn đến danh sách các Tx có thể bị sai thứ tự, không đầy đủ,...

## Mã hoá

Hàm Hash H là một hàm mã hoá được sử dụng với ý nghĩa sau:
Với 2 tập hợp M và T có:  $|M| \gg |T|$  thì hàm hash H: $|M| \rightarrow |T|$ có ý nghĩa là nhận input thuộc tập $M$ và đưa ra output thuộc tập $T$. Ví dụ khi input có giá trị lên đến cả megabyte, $T=\lbrace0, 1\rbrace^{256}$ khi chuyển qua hàm hash **H** sẽ có dạng 32 bytes. 

Một hàm hash như trên gọi là chống trùng lặp nếu như rất khó để tìm ra 1 cặp $(x, y) \in M$ sao cho $x \ne y$ mà $H(x) = H(y)$. Hàm này còn được gọi là <mark>CRF</mark> (Collision Resistant Function).

### Commit dữ liệu lên blockchain

Nếu user $A$ có file $m: h = H(m)$  là cam kết ràng buộc cho $m$. Với một danh sách  $S = (m_1,...m_n)$, $h = H(S)$.  Khi user $B$ đọc $h$, cho $(m_i, proof \pi_i)$ có thể kiểm tra xem $S[i]$ có bằng $m_i$ hay không.
Điều này tương đương với khi chạy hàm  $verify(h, i, m, \pi_i)$ sẽ ra 1 trong 2 kết quả: *accept/reject*.

Ngoài ra, để đảm bảm security, không thể tìm được bộ $(S, i, m, \pi)$ mà $m \ne S[i]$ và $verify(h, i, m_i, \pi_i)$= accept.

### Merkle Tree

Mục tiêu của Merkle tree là khi commit danh sách $S$ ở trên, có thể chứng minh được $mi = S[i]$.
Cây merkle được xây dựng như sau:
<a class="post-image" >
<img itemprop="image"  src="/assets/images/post_img/post6_1.PNG"/>
</a>


Các phần tử $i$, cha của chúng là $H(i, sib(i))$ với $sib(i)$ là con kề với $i$. Ví dụ $y1$ là $H(m_1, m_2)$. Bây giờ, với bài toán chứng minh ở trên, ví dụ cần chứng minh $S[4] = m_4$, ta chỉ cần set $Proof = (m_3, y_1, y_6)$. Lý do:

$y_2 \leftarrow H(m_3, m_4)$

$y_5 \leftarrow H(y_2, y_1)$

$h' \leftarrow H(y_5, y_6)$

Nếu $h = h'$ thì *accept*. Khi muốn post 1 block vào trong chain, cần viết **commit(S)** vào. Merkle tree được sử dụng để chứng minh một Tx có ở trong block hay không, hiển thị số dư,...

## Chữ kí số

Làm cách nào mà các nodes có thể xác thực được các Tx? Thực tế, để xác định cần 3 thuật toán:
+ $Gen()$: tạo ra cặp key  $(pK, sK)$ cho user.
+ $Sign(sK, msg)$: người gửi kí secretKey với $msg$ họ muốn gửi. Output: là 1 sig: $σ$
+ $Verify(pK, msg, σ)$. Output: accept / reject

<a class="post-image" >
<img itemprop="image"  src="/assets/images/post_img/post6_2.PNG"/>
</a>

Khi tạo ra các Tx, user đưa $Sk$ + $msg$ để tạo ra chữ kí $σ$. Các verifier sẽ sử dụng $pK$, $msg$ và $σ$ để verify các Tx. Việc sử dụng chữ kí số này giúp đảm bảo uỷ quyền Tx, khả năng bầu cử và sự đồng thuận bỏ phiếu của user.



## Tạm kết

Ở phần này là những kĩ thuật phổ biến được sử dụng trong blockchain. Trong bài tiếp theo, mình sẽ đi sâu về cấu tạo của từng khối và cách hoạt động của PoW.
