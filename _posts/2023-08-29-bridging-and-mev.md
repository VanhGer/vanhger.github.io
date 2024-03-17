---
layout: post

title: Bridging và MEV
category: [cs251]
postnum: 21
---

Trong bài viết này, mình sẽ giới thiệu các bạn đến MEV và Bridging giữa các blockchain khác nhau.

## MEV

### Khái niệm

MEV là Maximal Extractable Value. Đây là giá trị lớn nhất có thể thu được từ việc sản xuất các block vượt qua các phần thưởng block tiêu chuẩn và phí gas bằng việc thêm, loại bỏ, thay đổi thứ tự các Tx trong khối.

### Searchers

Searchers là những người post các Tx để cân bằng thị trường, thanh lý và lấy lợi nhuận. Ví dụ: <br>
Uniswap DAI/USDC có ExchangeRate là 1.001, trong khi Sushiswap là 1.002.  Lúc này, Searchers có thể vay 1 khoản DAI, đổi DAI thành USDC trong Uniswap rồi dùng nó để đổi lại DAI trong Sushiswap.  <br>
Ngoài ra, còn nhiều cách để nhận MEV từ việc thay đổi chuỗi Tx (Bundle).\

### Vấn đề của MEV

Khi searchers post Tx lên mempool, những điều sau có thể xảy ra:

- Validator: có thể tự tạo Tx của họ với cách tương tự Tx của searchers nhưng người thụ hưởng sẽ là validator đó và đặt nó trước Tx của searchers.
- Searchers khác: tự tạo Tx của họ nhưng với phí gas ưu tiên (maxPriorityFee) cao hơn. (Thường được thực hiện bởi các bots).

#### Hậu quả

<Strong> Với Honest users: </strong> Việc submit lại nhiều lần các Tx với phí gas ưu tiên cao hơn cho đến khi validator chọn được 1 Tx trong ít giây (Đấu giá gas) sẽ dẫn đến việc tắc nghẽn và phí gas cao.  

<Strong> Với consensus: </strong> Tấn công vào chuỗi dài nhất. Lý do vì MEV sẽ tạo lợi nhuận cho miner nhiều hơn phần thưởng khối.

<Strong> Gây ra Centralization </strong>: Các validator có thể trộm MEV Tx từ các searchers. Do đó nhiều searchers chỉ gửi Tx cho 1 vài validator mà họ tin tưởng. Lâu dần sẽ khiến một số Validators giữ số lượng lớn các Tx.

### Giải pháp: Proposer Builder Separation (PBS)

<strong> Mục tiêu: </strong>

- Giảm việc đấu giá gas
- Thay vào đó, tạo ra một open market mà searchers cạnh tranh về vị trí bundle của họ trong block.
- Mọi validators có thể kiếm MEV payment từ searchers. Từ đó tránh việc centralization.

Minh hoạ:
<a class="post-image" >
	<img itemprop="image"  src="/public/images/post_img/post21_1.PNG"/>
	</a>

<strong> Builder </strong>: thu thập các Bundles và Txs sau đó build 1 block. (trong block bao gồm feeRecipient (MEV offer) cho validator).

<strong> Relay </strong>: thu nhận các blocks, chọn block với MEV offer lớn nhất:

- Gửi block header (và MEV offer) cho block proposer.
- Không tiết lộ nội dung bên trong khối cho propser.

<strong> Proposer </strong>: chọn offer tốt nhất và kí header với staking key của nó. Sau đó Relay sẽ tiết lộ nội dung block. Sau đó proposer sẽ gửi block lên network.

<strong> Cụ thể </strong>

- Searchers sẽ phải thắng 2 cuộc đấu giá để bundle của họ được đưa vào block (là builder auction và relay auction).
- Searchers phải làm việc với builder mà họ tin tưởng và phải tin tưởng relay làm việc cùng với builder đó. Searchers không cần phải tin Proposer.
- Proposer không thể bỏ lỡ slot. Nếu relay không đưa cho họ block hợp lệ, proposer sẽ tự tạo block từ mempool của nó.
- Khi proposer kí header, nó không thể kí khối khác, nếu không sẽ bị slash.

Một số ví dụ về relay là Flashbot, BloXroute.

## Khả năng tương tác giữa các blockchain

<strong> Tương tác </strong>: Cho phép user chuyển tài sản qua các chain khác nhau. <br>
<strong> Kết hợp </strong>: Cho phép 1 DApp từ 1 chuỗi có thể  gọi DApp từ chuỗi khác.

Để làm được điều này, cần sử dụng các Bridges.

### Ví dụ: Chuyển BTC trong Ethereum

Sử dụng wrapped coins.

Với wBTC

- Alice lock 1 BTC trong custodian's BTC address
- Custodian trung gian sau khi thấy điều này, sẽ gửi msg đến ERC20 (bridge contract), xác nhận
- ERC20 sẽ tạo wBTC cho Alice trong Ethereum
- Sau khi dùng xong, Alice sẽ burn wBTC.
- Custodian trung gian sau khi thấy điều này, sẽ gửi msg đến custodian BTC.
- Custodian BTC sẽ unlock 1 BTC cho Alice.

Vấn đề đưa ra là liệu các Custodian có tin tưởng được không? <br>
Để giải quyết, ta sẽ sử dụng tBTC như sau:

- Chọn ngẫu nhiên 3 custodian và tạo P2PKH Bitcoin address cho Alice.
- Khoá chữ ký 3-3 là bí mật giữa 3 người.
- Alice gửi BTC vào địa chỉ P2PKH, nhận tBTC
- Custodians cần khoá 1.5x ETH stake cho BTC họ quản lý. Nếu BTC locked mà bị mất, Alice sẽ lấy staked ETH.

### 2 loại Bridges

<strong> Loại 1: </strong> lock-and-mint bridge

- SRC ⇾ DEST: user lock tiền ở SRC, wrapped token sẽ được mint ở DEST.
- DEST ⇾ SRC: fund khi được burned từ DEST, thì SRC sẽ release lock.

<strong> Loại 2: </strong> liquidity pool bridge

- Cả 2 phía đều có Liquidity provider
- SRC -> DEST: người dùng gửi funds ở SRC, số tiền tương đương sẽ được giải phóng từ DEST.

### Bridging smart chain

- Bước 1: Tạo một hệ thống message xuyên chuỗi an toàn
- Bước 2: Xây 1 bridge sử dụng hệ thống message

Minh hoạ

<a class="post-image" >
	<img itemprop="image"  src="/public/images/post_img/post21_2.PNG"/>
	</a>

Có 2 loại hệ thống message: <br>
<strong> Externally verified </strong>: các bên bên ngoài (Trustee) sẽ xác minh các message trên chuỗi S. <br>
RelayerT chuyển msg cho người nhận khi các trustees đều kí ⟹ nếu DApp-Y tin trustees, thì sẽ biết được DApp-X gửi msg. <br>
Nếu trustee kí và post fake msg cho RelayerT thì những bên offchain có thể gửi signature của trustee cho relayerS, khiến trustee bị slashed.

<strong> Onchain verified </strong>: chuỗi T verifies block header của chuỗi S. <br>
Oracle gửi msg cho relayerT, cùng với finalized block header của chuỗi S. Oracle sẽ không được gọi là trustee vì nó chỉ chuyển các finalized block.<br>
RelayerT sẽ chạy client nhẹ để xác thực xem chuỗi S có nhận msg mà gửi đi hay không.

Tuy nhiên, vấn đề đặt ra là việc chuỗi T verify state của chuỗi S sẽ tốn gas. Để giải quyết điều này, cần sử dụng zkBridge: sử dụng SNARK để giảm việc tính toán cho relayerT.

## Tạm kết

Với briding, trong tương lai sẽ giúp việc di chuyển các assets nhanh và rẻ qua các chain, khiến các project thanh khoản sẽ được thực hiện ở trong tất cả các chain.
