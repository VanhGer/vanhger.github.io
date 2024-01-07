---
layout: post
title: Đồng thuận trên mạng
# feature-img: "assets/img/blur/bandb.png"
# img_title: "assets/images/title_img/post1.png"
# tags: [Instruction]
# author: vanhg
# permalink: /blog/2023/07/22/dong-thuan-tren-mang

---
Ở bài trước, ta đã biết rằng các node sẽ tham gia vào giao thức có thể là Adversary và có thể gây hại cho blockchain. Vậy làm cách nào để chọn các node tham gia vào consensus.

## Bitcoin Mining
Bitcoin chọn các nodes tham gia vào giao thức bằng các sử dụng POW, giải bài toán tìm số nonce (xem chi tiết [tại](http://127.0.0.1:4000/blog/2023/07/20/cau-tao-block-va-pow)). 
Bitcoin sử dụng Nakamoto consensus: <br> 
<strong> Quy tắc đề xuất: </strong> ở thời điểm nào, mỗi miner trung thực mở rộng chuỗi nặng nhất (với độ khó cao nhất).
<strong> Quy tắc confirmation: </strong> Miner confirm 1 block (với tiền tố của nó) mà ở độ sâu k trong chuỗi nó nhìn được.
- Ví dụ trong thực tế: k = 6.
- Miner và client chấp nhận các Tx ở block cuối cùng được confirm và tiền tố của nó như là log. 
- Leader được chọn bởi POW.

So sánh giữa Bitcoin và Streamlet /// ảnh <br>
- Streamlet thì không dynamic available: Nó sẽ không đảm bảo liveness nếu n/3 hoặc hơn các nodes offline.
- Bitcoin thì dynamic available: tiếp tục confirm Tx khi miningpower offline. <br>

## Consensus ở Internet
Các nodes tham gia mở, tức là các adversary có thể tạo nhiều node và người tham gia trung thực có thể online hoặc offline và đi tuỳ ý. Do đó, mục tiêu đặt ra là phải hạn chế sự tham gia của Adversary, đồng thời duy trì dynamic available của giao thức.

## Security
Bitcoin đảm bảo an toàn dưới mạng đồng bộ, với tỉ lệ mining power do Adversary kiểm soát 𝜷 < 1/2

### Nakamoto's Private Attack: 𝜷 ≥ 1/2
Khi adversay đào block mới, nhưng không công bố. Các miner trung thực tiếp tục đào mà không biết về các block ẩn này. Khi adversary công bố chuỗi đó (đã tạo đủ block), chuỗi đó trở thành chuỗi dài hơn chuỗi mà miners trung thực đào được (vì 𝜷 ≥ 1/2), các block đào bởi các miner trung thực sẽ bị bỏ.
### Forking
Vì Network delay, các honest blocks ở cùng 1 độ cao. Do đó, tỉ lệ phát triển các khối honest sẽ chậm hơn so với 1 - 𝜷. Do đó, Adversary sẽ thành công nếu 𝜷 ≥ (1 - 𝜷) / 2 (Trường hợp chỉ có 2 chuỗi fork).
### Security
Nếu 𝜷 < 1/2, tồn tại tỉ lệ Mining đủ nhỏ 𝜆(Δ,𝛽) = 𝜆A + 𝜆H mà Bitcoin thoả mãn security với tỉ lệ lỗi chấp nhận là 𝑒^(−Ω(𝑘)) dưới mạng đồng bộ. Đây là xác xuất lỗi với comfirmation. Đây chính là lý do sau 10 phút mới tạo ra khối mới chứ không phải là 1 giây.

#### Chứng minh
Nhận thấy, với các khối trung thực được tạo, vẫn có thể tạo ra fork vì khối sau được tạo ra quá gần khối trước (< Δ). Lúc này, khối tạo sau được gọi là Tailgater. Ngược lại, nếu khối được tạo ở thời điểm t, và không tồn tại khối nào được tạo trong khoảng thời gian từ t - Δ đến t, thì khối đó được gọi là Non-Tailgater. Các khối Non-Tailgater thì có độ cao riêng biệt nhau. 

Giờ, ta cần tìm tỉ lệ các khối trung thực là Tailgater hoặc Non-Tailgater. Xem thời gian tạo các khối là biến ngẫu nhiên Poisson, thì khoảng thời gian T giữa 2 khối tuân theo phân bố mũ. <br> 
Gọi mining rate là λ (1/ minutes). Với Bitcoin λ = 1/10.<br>
Theo phân bố mũ, xác suất để T lớn hơn s bất kì là: P[T > s] = 𝑒^(-λs). Suy ra, nếu s = Δ thì tỉ lệ honest block tạo ra là Non-Tailgater là g = P[T > Δ] =  𝑒^(-λΔ), Tailgater là 1 - g.

Ta thấy, các Non-tailgater block tăng với tỉ lệ: gλ, các block này có height khác nhau, vì thế chuỗi dài nhất cũng tăng ít nhất với tỉ lệ này. => liveness.

Với Safety, ta cần thêm các khái niệm sau: 
- Block B được đào ở thời gian t, nếu không có honest block nào khác được đào từ t−Δ đến t+Δ thì B gọi là loner. Do đó, loner là block duy nhất ở độ cao đó. <br>
Để vi phạm safety, cần phải có hai chuỗi khác nhau hơn k khối, cả hai đều được thông qua bởi các node trung thực. Điều này tương đương với các Adversary phải khai thác các khối nhanh hơn các khối loner được tạo ra. Gọi tỉ lệ mine của Adversary là 𝜷. Tỉ lệ mine loner là g * g * λ. <br> Do đó, Nakamoto đảm bảo security nếu g * g * λ > 𝜷. 

## Kêt luận
Nếu mọi người muốn xem chi tiết về cách chứng minh, hãy truy cập vào [đây](https://eprint.iacr.org/2019/943.pdf).