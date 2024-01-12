---

title: Cơ chế Bitcoin
layout: post
category: [blockchain, cs251]
tags: [knowledge]
postnum: 8
---

# Cách vận hành

Trong mạng lưới Bitcoin, với mỗi Miner, họ sẽ thông thường được connect với 8 người khác. Khi một Tx được gửi đến, Miners sẽ thông báo là nhận được Tx trên mạng P2P. Mọi miners sẽ xác thực rằng Tx đã nhận và lưu vào trong Mempool. Mempool là nơi chứa những Tx đang chờ được xử lý. Các nodes này chia sẻ dữ liệu mempool với nhau bằng cách chuyển tiếp các Tx đã kí với nhau cho đến khi nó được toàn bộ mạng lưới.

### Thời gian đóng các khối

Cứ sau mỗi 10 phút, mỗi miner sẽ tạo 1 block tiềm năng từ các Tx trong mempool của nó. Các Tx được ưu tiên theo phí giao dịch. 1 miner được chọn (sau khi giải được bài toán PoW) sẽ phân tán block đó cho toàn mạng và mọi miner khác sẽ xem được block, sau đó block được đưa vào chuỗi. Phần thưởng là 6.25 BTC. Phần thưởng này được gửi cho miner thông qua coinbase Tx (là Tx đầu tiên trong block chứa địa chỉ ví của miner do ,miner thêm vào).

## Cấu trúc của một Transaction

Transaction trong Bitcoin chia làm 2 phần chính là Input và Output.

Input gồm 3 phần chính:

- Previous Tx ID: ID của Tx trước đó mà tạo ra Bitcoin ở Output để sử dụng trong giao dịch hiện tại.
- Previous Tx index: Vì một Tx có thể có nhiều Output nên index này được sử dụng để xác định output trong Tx trước đó. Danh sách các output được xem như 1 tập hợp bên trong Tx.
- ScriptSig: Script Signature. Nó mã hoá Public key và chữ kí của người sở hữu Bitcoin ở hiện tại.

<a class="post-image" >
<img itemprop="image"  src="/assets/images/post_img/post8_1.PNG"/>
</a>

Output: là những bitcoin mới được khoá với Hash của Public Key của người nhận. Bitcoin sử dụng mô hình UTXO là Bitcoin chỉ có thể sử dụng 1 lần, sau mỗi giao dịch thì bitcoin cũ sẽ bị loại bỏ và một lượng bitcoin mới được tạo ra. Output gồm 2 phần chính:

- Value: Số lượng bitcoin mà được gửi cho người nhận. (Min: 1 satoshi = 10^-8 BTC)
- ScriptPubKey: Là một dãy các chỉ dẫn (Như 1 hàm) mà nhận ScriptSig là Input và trả về True nếu người sở hữu hợp lệ mở khoá Bitcoin này. Ngược lại, nó trả về false.

Một giao dịch có thể có nhiều Input và Output. Các input có thể từ nhiều user (gọi là MultiSig Tx), các output có thể đến nhiều user.

Ngoài ra, trong Tx còn có 2 phần khác là segwit và locktime.

### Miner xác minh một Tx như thế nào

Một Tx thoả mãn khi:

- $ScriptSig \|ScriptPK$ trả về True. Tức là với điều kiện nào thì UTXO có thể được tiêu.
- $TxID\|index$ : hiện ở trong tập các UTXO
- $\sum (Input values) > \sum (Output values)$: Không thể tiêu nhiều hơn số tiền đang có.

Sau khi 1 Tx được post lên, miners sẽ xoá UTXO tương ứng của nó trong tập.

## Các loại Tx

### Pay to public key hash (P2PKH)

Trước tiên, mọi người cần hiểu về một số OP codes hoạt động trong Bitcoin Script ở [đây](https://wiki.bitcoinsv.io/index.php/Opcodes_used_in_Bitcoin_Script).
Giả sử Alice muốn gửi cho Bob 5BTC. Alice và Bob sẽ làm các bước sau:

- Bob tạo sig : gọi $Gen()$:  (pkB, skB)
- Bob tính địa chỉ bitcoin của mình bằng: $addrB = H(pkB)$
- Bob gửi $addrB$ cho Alice
- Alice tạo giao dịch Tx-fund với $Output[0]$ là: $UTXO[0] : 5, ScriptPkB$ với $ScriptPkB$ là:

	    DUP  HASH256  <<addrB>>  EQVERIFY  CHECKSIG

còn ở Input, Alice có ScriptSig để xác thực Bitcoin của Alice có chứa chữ ký của Alice lên Tx này, khiến cho không ai có quyền thay đổi ScriptPkB.

Sau đó, Bob muốn sử dụng $UTXO[0]$ thì sẽ tạo 1 Tx-spend với $ScriptSigB$ trong input, bao gồm:

- << sig >>
- << pkB >>

với $Sig = Sig(skB, Tx)$ (Tx này là Tx-spend nhưng không chứa các ScriptSig).

Miner có thể xác thực $ScriptSigB|ScriptPkB = true$ và cho phép Bob sử dụng số tiền.
Một số lưu ý như:

- Người nhận (Bob) không thể tiết lộ $PkB$ cho đến khi UTXO được dùng (vì bảo mật).
- Vì tránh việc tấn công bằng cách thay đổi Sig, Sig được chuyển vào phần witness và TxID được tính bằng:               $TxID = H(Tx$ không chứa witness $)$.


### Pay to script hash (P2SH)

Người nhận (Bob) thay vì tạo address là $H(pkB)$ thì tạo 1 **redeem script**, sau đó gán địa chỉ để người gửi (Alice) chuyển qua thành **H(redeem script)**.
Ví dụ:

- ScriptPK ở UTXO: 

		HASH160   <<H(redeem script)>>  EQUAL
- ScriptSig để tiêu: 

		{sig1 , sig2, …, sign, redeem script}

Từ đây, người gửi (Alice) có thể thêm các điều kiện phức tạp để xác định khi nào người nhận (Bob) có thể tiêu.

**Reedem Script**: là các điều kiện thoả mãn để có thể sử dụng UTXO như: số chữ kí tối thiểu, thời gian,...

Các miner xác minh bằng cách:

- $ScriptSig\|ScriptPk = true$: Bob gửi đúng script.
- $ScriptSig = true$: script là thoả mãn (tức là sẽ tồn tại điều kiện để Bob có thể dùng UTXO).

#### Ví dụ: MultiSig

Muốn dùng UTXO thì cần $t/n$ chữ kí. Ví dụ với $2 / 3$ chữ kí. Redeem Script và ScriptSig được viết như sau:

- Redeem Script:  

		2, PK1, PK2, PK3, 3, CHECKMULTISIG
- ScriptSig: 
		
		0, sig1, sig3, redeem script

## Kết luận

Ở trên là 2 dạng Tx được sử dụng nhiều trong bitcoin, cũng là cơ chế bitcoin hoạt động. Ngoài những dạng Tx ở trên, sẽ có nhiều dạng Tx khác được sử dụng ở trong nhiều trường hợp cụ thể.
