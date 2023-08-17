---

title: Hệ thống vay DeFi
# feature-img: "assets/img/blur/bandb.png"
# img_title: "assets/images/title_img/post1.png"
# tags: [Instruction]
author: vanhg
permalink: /blog/2023/08/17/defi-lending-system

---

Ở bài viết này, mình sẽ giới thiệu về hệ thống vay trong môi trường phi tập trung. Nhưng trước hết, ta sẽ đi tìm hiểu việc vay trong hệ thống tập trung sẽ như thế nào và chứa những rủi ro gì.

## Vay trong hệ thống tập trung
Vì sao cần vay? Có các lý do chính như sau:
+ Ví dụ, Bob cần vay ETH để mua một NFT trong game nhưng anh ấy không muốn bán tài sản của mình đi.
+ Là chiến lược đầu tư, khi thế chấp ETH để nhận UNI, chờ tỉ giá UNI/ETH giảm, thì số UNI bỏ ra để mua 1ETH trả khoản vay sẽ ít hơn, từ đó nhận được UNI lãi.

Trong hệ thống tập trung, người cho vay gửi tiền vào một Tổ chức tài chính tập trung. Người vay sẽ thế chấp 1 loại tài sản nào đó và sẽ được vay. Sau đó, người vay sẽ phải trả tiền lãi cho đến khi trả xong phần đã vay. Người cho vay sẽ được nhận 1 phần lãi đó. <br> Tuy nhiên, nếu người vay không trả được khoản vay, Tổ chức tài chính tập trung sẽ mua lại tài sản thế chấp, và trả lại cho người vay phần còn thừa. <br> Nếu giá trị tài sản thế chấp bị thay đổi và ít hơn tài sản vay, việc thanh lý tài sản thế chấp sẽ xảy ra. 

Tuy nhiên, với hệ thống tập trung, có các vấn đề được thấy rõ như sau:
- User cần tin tưởng Tổ chức, nhưng nếu bị hack hoặc bị xâm nhập trái phép, khả năng mất tiền rất cao.
- Tổ chức điều chỉnh tỉ lệ lãi,..
- Người cho vay sẽ nhận ít tiền lãi hơn, vì phải chia 1 phần cho tổ chức.

Do đó, việc tạo ra hệ thống vay phi tập trung không cần bên thứ 3 và áp dụng tính lập trình là mục tiêu được người dùng hướng đến.

## Các thuật ngữ
<Strong> Collateral </Strong>: Tài sản thế chấp. Vai trò là đặt cọc để vay. 

<Strong> Over-collateralization </Strong>: Thế chấp vượt mức. Người vay phải đảm bảo giá trị thế chấp > giá trị vay.

<Strong> Under-collateralization </Strong>: Thế chấp dưới mức. Xảy ra khi giá trị thế chấp < giá trị vay. 

<strong> Collateral Factor </strong>: Giá trị lớn nhất có thể vay, tính theo giá trị thế chấp. Ví dụ CF = 0.6, thế chấp có giá trị là 1000 DAI thì giá trị vay lớn nhất có thể là 0.6 * 1000 = 600 DAI.<br>
Với tài sản có độ biến động cao, CF thường thấp, ngược lại thì CF cao. Ví dụ: ETH, DAI:  83%, UNI: 75%, MKR:  73%

<strong> Thanh lý </strong> : Nếu số tiền vay + lãi (gọi là nợ - Debt) > CF * Collateral (1) <br>
=> Tài sản thế chấp sẽ được thanh lý đến khi (1) không còn xảy ra.  Tỉ lệ thanh lý giúp người cho vay có thời gian thanh lý trước khi gặp rủi ro.

<strong> Health </strong> : Health của 1 món nợ được tính bằng công thức sau: <br> ![Alt text](https://latex.codecogs.com/svg.image?%20BorrowCapacity=%5Csum_%7Bi%7D%5E%7B%7Dvalue(collateral_%7Bi%7D)*CF_%7Bi%7D) <br>
![](https://latex.codecogs.com/svg.image?health=%5Cfrac%7BBorowCapacity%7D%7Btotal%20Debt%7D) <br>

Nếu health < 1: Thanh lý sẽ được kích hoạt đến khi health >= 1.

<strong> Liquidity </strong>: Thanh khoản. Là loại tài sản mà việc mua bán trên thị trường không thay đổi giá trị thị trường của nó. Nó cũng có khả năng chuyển thành tiền mặt.

## DeFi Lending
### Ý tưởng với OrderBook Dapp
Lưu trữ các giao dịch trên một OrderBook. Người vay tạo giao dịch vay và chờ người cho vay fill.
Bất lợi:
- Nhiều Txs, dẫn đến tính toán phức tạp
- Rủi ro: Không trả được nợ,..
- Rút tiền phức tạp

### Liquidity Pool
Over-collateralized lending:  Compound and Aave
Các LP (Liquidity Provider) cung cấp tài sản vào pool, và nhận những token tương ứng với tài sản mà họ đã cung cấp. Số lượng các token dựa vào Exchange Rate hiện tại. Exchange Rate sẽ được tính lại sau mỗi block.

Với người vay, họ gửi tài sản vào Pool và nhận cTokens. Sau đó khi muốn vay (ETH), thì token của họ sẽ bị khoá như là Collateral, sau đó Compound sẽ gửi ETH cho họ. <br>
Tiền lãi tích luỹ khi vay của người vay sẽ làm tăng Exchange Rate ETH/token, từ đó giúp những người cho vay (giữ các token) được lợi ích khi token sẽ tăng giá. 

### The Exchange Rate
Giả sử với thị trường ETH:
- Supplying ETH: thêm vào UnderlyingBalance
- Borrowing ETH: thêm vào totalBorrowBalance
- Lãi phải trả: thêm lần lượt vào totalBorrowBalance<br>

![](https://latex.codecogs.com/svg.image?ExchangeRate_%7BETH/token%7D=%5Cfrac%7BUnderlyingBalance&plus;totalBorrowBalance-reserve%7D%7BcTokenSupply%7D)

Do đó, nếu tổng lượng vay càng nhiều, thì Exchange Rate sẽ càng tăng. Ngược lại nếu lượng UnderlyingBalance tăng thì lượng cTokenSupply tương ứng cũng sẽ tăng, từ đó dẫn đến ExchangeRate giảm. 

### Interest Rate: Lãi xuất
Lãi xuất được cập nhập một cách liên tục. Xác định bởi nhu cầu về tài sản với quy mô thị trường.<br>
Tỉ lệ sử dụng: <br>
![](https://latex.codecogs.com/svg.image?U_%7BETH%7D=%5Cfrac%7BtotalBorrowBalance%7D%7BavailableBalance&plus;totalBorrowBalance%7D) <br>
Tỉ lệ này thuộc khoảng [0,1]. <br>
Tỉ lệ lãi: interestRate = BaseRate + U(ETH) x slope(ETH).

### Thanh lý
Nếu health < 1, thì ai cũng có thể gọi hàm: <br>
liquidate(borrower,   CollateralAsset,   BorrowAsset,  uint amount) <br>
Trong đó:
- borrower: address của người vay
- CollateralAsset: người thanh lý muốn token từ tài sản này (VD: cDAI)
- BorrowAsset: người thanh lý cung cấp tài sản này (ví dụ ETH) <br>

Hàm này chuyển ETH của người thanh lý ra thị trường ETH và cung cấp cho người thanh lý cDAI từ tài sản thế chấp của người dùng. Điều này tương đương với người thanh lý đang trả khoản nợ ETH cho người vay và nhận cDAI của người dùng. 


## Flash loan
Là khoản vay nhanh được thực hiện và hoàn trả trong 1 giao dịch. <br>
=> zero risk cho người gửi, và không cần thế chấp.
### Các trường hợp được sử dụng
#### Chênh lệch giá:
Người dùng Alice thấy chênh lệch giá USDC/DAI trong 2 pool, có thể dùng flash loan để kiếm lời. Ví dụ ở pool A có ExchangeRate là: 1.00, trong khi ở pool B là 1.01 thì Alice có thể flash loan 1 USDC, dùng để đổi được 1.01 DAI từ pool B. Sau đó dùng 1.00 DAI đổi lấy 1 USDC từ pool A, và trả flash loan. Do đó, Alice sẽ lời được 0.01 DAI.

#### Hoán đổi tài sản thế chấp
Nhận thấy việc thế chấp bằng ETH có thể bị sụt giá dẫn đến thanh lý, người dùng có thể đổi tài sản thế chấp thành USDC. Quá trình như sau:
- Nhận 1000 DAI flash loan
- Trả 1000 DAI để nhận 1 cETH
- Đổi 1 cETH lấy 3000 cUSDC
- Gửi 3000 cUSDC làm thế chấp để Mượn 1000 DAI
- Trả 1000 DAI flash loan 

## Một số thông tin khác

### DAO: Decentralized orgs 
Là 1 DApp được triển khai onchain tại 1 địa chỉ cụ thể. <br>
Ai cũng có thể gửi tiền vào kho bạc của DAO và gửi đề xuất lên DAO. Đề xuất được thông qua và được các thành viên tham gia vote.
Ví dụ:
- PleaseDAO: đầu tư vào NFTs
- Gitcoin: Tài trợ cho các dự án openSource.

### Giao thức Compound
Là giao thức trên Ethereum, thiết lập thị trường tiền tệ, là những nhóm tài sản có lãi suất theo thuật toán, dựa và cung và cầu của tài sản đó. Các nhà cung cấp và người vay sẽ tương tác trực tiếp với giao thức, không cần phải thương lượng các điều khoản như kì hạn, lãi suất, hoặc tài sản thế chấp,..

### Supplying Assets
Tổng hợp nguồn cung từ các user. Khi user cung cấp assets, nó trở thành tài nguyên thay thế được, và mang lại tính thanh khoản cáo hơn khi cho vay trực tiếp. Từ đó người cho vay có thể rút tiền bất cứ lúc nào.

### Borrowing Assets
Cho phép người dùng vay dễ dàng, sử dụng cTokens làm thế chấp. Người dùng không cần điều khoản đàm phán, ngày đáo hạn,..

Chi tiết về giao thức Compound, các bạn có thể xem ở [đây](https://compound.finance/documents/Compound.Whitepaper.pdf)

### Cross-chain AtomicSwap
#### Các thuật ngữ cơ bản
<strong> HTLC</strong>: hash time-locked contract <br>

<strong> Time-lock </strong> Khoá thời gian, khi đúng khoá thì cần 1 khoảng thời gian nhất định để pass, tức là hết thời gian thì mới mở được. <br>

<strong> Hash lock</strong> Yêu cầu key đúng + secret code. Sau khi nhập code thì sẽ hiển thị lên cho mọi người.

#### Kịch bản
Alice muốn Bitcoin, Bob muốn BCash. A và B quyết định đổi cho nhau. Các bước như sau:
- Alice tạo 2 Hashlock, yêu cầu cùng 1 SecretCode. Nhưng key thì 1 cái là key của A, 1 cái cần key của B.
- Alice đặt HashlockA và HashlockB vào lần lượt mailBoxA và mailBox B.
- Alice đưa BCash vào mailbox B, Bob đưa Bitcoin vào mailbox A.
- Sau đso, Alice đến mailboxA, nhập keyA và secretCode, nhận bitcoin. Đồng thời, sau khi Alice nhập, secretCode hiển thị cho mọi người nên Bob sẽ nhập theo + key vào mailBoxB, nhận được BCash.

Trường hợp A không nhập SecreteCode hoặc cố ý chỉnh sai SecretCode ở 2 lock, thì time-lock sẽ được sử dụng:
- Độ dài của locktime của A phải lớn hơn của B (1 tuần >< 1 ngày) vì A biết secretCode. Do đó, khi B biết A lừa dối, B có thể quay lại mailBoxA và lấy tiền về ngay lập tức (vì lúc đó A vẫn đang đợi hết timelock để lấy được tiền). Ngược lại nếu locktime của A nhỏ hơn hoặc bằng B, thì A có thể đợi đến khi hết hạn, nhập code và lấy tiền (vì A có key của B lúc tạo lock).


## Kết luận
Ở trên là toàn bộ hiểu biết của mình về hệ thống vay phi tập trung.








