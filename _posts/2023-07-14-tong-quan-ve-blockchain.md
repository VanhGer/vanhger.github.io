---

title: Tổng quan về Blockchain
# feature-img: "assets/img/blur/bandb.png"
# img_title: "assets/images/title_img/post1.png"
# tags: [Instruction]
author: vanhg
permalink: /blog/2023/07/14/tong-quan-ve-blockchain

---

Blockchain là một xu thế đang khá nổi ở thời điểm hiện tại. Theo những gì mình tìm hiểu và học hỏi được, đây là những chia sẻ tổng quan của mình về blockchain

## Blockchain là gì
Blockchain là một sổ cái số phi tập chung ghi lại các dữ liệu giao dịch một cách an toàn, được duy trì bởi các máy tính chuyên dụng trên mạng. Nó đảm bảo tính toàn vẹn bất biến thông qua cơ chế mã hoá và đồng thuận. Khi một giao dịch đã được record, không thể thay đổi.

Nói đơn giản, blockchain như một loại cơ sở dữ liệu đặc biệt, được duy trì phi tập trung bởi nhiều máy tính phân bổ khắp thế giới. Ngoài ra, dữ liệu trên từng block sắp xếp theo trình tự thời gian, và bảo mật bằng mật mã để tránh bị làm giả dữ liệu

## Sự phi tập trung trong blockchain
Trong Blockchain, quyền quyết định và điều khiển của mạng lưới thì phân bổ cho các user thay vì tập trung tại 1 user hoặc 1 tổ chức. Các giao dịch (Tx) được verified và ghi vào mạng máy tính phân tán hoạt động cùng nhau để duy trì tính toàn vẹn.

Ở trong core, blockchain lưu giao dịch giữa 2 bên. Khi 1 user gửi 1 Tx, nó được truyền đi khắp mạng lưới. Các node (các máy tính đặc biệt) xác minh giao dịch bằng xác minh chữ kí số (Digital Signature) và các thông tin khác. Khi giao dịch được xác minh, nó sẽ cùng với các Tx khác được thêm vào block. Các block sẽ liên kết với nhau bằng phương pháp mã hoá, tạo thành chuỗi. Quá trình xác minh và thêm vào chuỗi sẽ được thông qua cơ chế đồng thuận.

## Cơ chế đồng thuận và mã hoá
Cơ chế đồng thuận là tập hợp các quy tắc chi phối các node có đồng thuận về trạng thái của chuỗi hiện tại và tính hợp lệ của giao dịch hay không. Điều này đảm bảo các node trong mạng sẽ đều có bản copy đầu đủ của sổ cái. 

Mã hoá là chìa khoá duy trì bảo mật, minh bạch và chống giả mạo của blockchain. Có 2 cách mã hoá chính được sử dụng là Hash và cặp khoá. Với Hash, là hàm không thể đảo ngược, giúp chuyển 1 input ở 1 size bất kì sang 1 size cố định. Mỗi khối trong chuỗi chứa hash của khối liền trước nó, nên khi muốn thay đổi 1 khối thì cần phải thay đổi các khối tiếp theo. Điều này giúp giảm khả năng bị thay đổi khối xuống còn rất nhỏ. Người dùng có 1 cặp khoá secret - public của mình. Khi tạo 1 Tx, người đó sử dụng secret key để kí và tạo chữ kí số. Những người khác xác nhận tính xác nhận của Tx này bằng cách áp dụng public key của người gửi.

## Crypto Currency
Tiền điện tử (Tiền mã hoá) là 1 loại tiền kĩ thuật số phi tập trung, sử dụng mật mã để bảo mật. Nó có thể hoạt động độc lập với các trung gian như ngân hàng, cổng thanh toán,..
Việc phi tập trung tạo điều kiện thuận lợi cho các giao dịch ngang hàng (P2P) giữa các cá nhân thông qua các ví điện tử hoặc sàn giao dịch.
Các tiền điện tử luôn nằm trong blockchain. Trong trường hợp các sàn giao dịch, nó chứa secret key cho phép người dùng truy cập vào các tiền đó.

### Crypto Currency hoạt động như thế nào ?
Nó sử dụng thuật toán toán học tiên tiến để bảo mật các giao dịch và bảo vệ dữ liệu khỏi bị truy cập hoặc thao tác trái phép. Các thuật toán phục vụ 2 chức năng chính là: Druy trì quyền riêng tư của người dùng và xác minh tính xác thực của Tx.

Crypto whitepaper là một document mổ tả chi tiết kèm các thông số kĩ thuật về 1 dự án blockchain. Các thông tin như: project goals, works, tech, roadmap,...

### Stable coin
Một loại tiền điện tử được thiết kế để duy trì một giá trị cố định. Nó có thể trace giá trị tiền tệ fiat (tiền tệ được các nước sử dụng chính thức) hoặc các loại tài sản khác.

## SmartContract
Hợp đồng thông minh là một thoả thuận kĩ thuật số được lập trình và lưu trữ trong blockchain, tự vận hành không cần trung gian. Nó có tính bảo mật, minh bạch, cung cấp cho người dùng tự do trao đổi, an toàn, khi triển khai các giao dịch. Hợp đồng thông minh là xương sống của các DApps.
Bất cứ ai truy cập vào blockchain có thể gọi SmartContract và tương tác với nó.

## Nodes
Là các moderators xây dựng cơ sở cho mạng phi tập trung. Nó lưu trữ bản copy của sổ cái, được đồng bộ hoá và lập trình để thực hiện các giao dịch theo sự đồng thuận của đa số. Các nodes có thể là các máy tính, router,...

## Tạm Kết
Đây chỉ là những khái niệm cơ bản về blockchain. Ở bài sau, mình sẽ đi sâu vào cách hoạt động cụ thể của Blockchain.