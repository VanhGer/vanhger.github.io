---
layout: post
title: Mình đã tạo một game bằng C++ và SDL2.0
category: [software]
postnum: 4
---


Xin chào mọi người. Ở kì học thứ 2, mình được học môn Lập trình nâng cao, là môn thứ 2 để hướng dẫn học sinh về lập trình sau môn "Nhập môn lập trình" thuộc kì học 1. Tại môn này, mình đã được hướng dẫn làm một project game 2D bằng ngôn ngữ C++ và sử dụng SDL 2.0 để hỗ trợ. Đây là project đầu tiên của mình, và hôm nay mình sẽ viết về cách mà mình tạo ra nó.

## 1. Cài đặt môi trường và sử dụng SDL2.0

Về môi trường cài đặt, mình đã được giảng viên dành 1 buổi trên lớp để hướng dẫn cụ thể các bước về tải thư viện SDL2.0, chỉnh sửa các câu lệnh và chạy thử. Mình code game này ở trên <b> CodeBlock </b>, và có dựa theo hướng dẫn sau để cài đặt môi trường: <b>[Youtube](https://www.youtube.com/watch?v=Gj3iXwvKmUY) </b>và <b>[Lazyfoo](https://lazyfoo.net/tutorials/SDL/01_hello_SDL/index.php)</b>.
Về việc sử dụng SDL2.0, mình tham khảo trên website <b>[Lazyfoo](https://lazyfoo.net/tutorials/SDL/index.php)</b>, ở đây có những hướng dẫn rất chi tiết theo từng bước, rất phù hợp cho việc tìm hiểu và học tập.

Bài viết này, mình sẽ không đi sâu về mặt kĩ thuật cài đặt và xử lý (vì trong tài liệu trên website Lazyfoo ở trên đã nói rất kĩ từng phần) mà chủ yếu là hành trình mình tạo ra game này.

## 2. Ý tưởng về game này

Mình là một người rất đam mê tìm hiểu về các bí ẩn và đặc biệt là các truyền thuyết về truy tìm kho báu nên ý tưởng đầu tiên nảy ra trong đầu mình sẽ là làm game về đề tài này. Tuy nhiên, để cụ thể hoá ý tưởng thì là điều không hề dễ dàng. Lý do là vì trong những tài liệu hay bộ phim về truy tìm kho báu, nhân vật chính thường phải trải qua rất nhiều thử thách và cạm bẫy thì mới có thể tiến đến kho báu. Kết quả là sau một khoảng thời gian suy nghĩ, mình quyết định tạo ra các mini-game trong game này, với mỗi mini-game tượng trưng cho mỗi thử thách cần vượt qua với độ khó tăng dần, cùng với đó là việc xây dựng nội dung từng thử thách sao cho có tính logic và cốt truyện. Mình nghĩ rằng nếu như chỉ code một game đơn giản thì điểm cho project này sẽ không cao, nhưng nếu là tổng hợp của nhiều game như thế thì có thể sẽ khác. Tiếp theo, mình sẽ phát triển loại game là dạng game đồ hoạ Pixel, như một số game nổi tiếng: <b> Stardrew Valley </b>,<b> Harvest Town </b>,... Cuối cùng, khi ý tưởng gốc đã xong xuôi, mình sẽ đi vào phần chi tiết cho từng thử thách.

## 3. Thử thách thứ nhất

Khi bắt tay vào làm, mình vẫn chưa nghĩ ra được mini-game đầu tiên sẽ là gì cho phù hợp. Cho đến buổi thực hành thứ 2 ở môn này, giảng viên đã cho mình làm 1 bài tập nhỏ là sử dụng hàm Random trong C++ để tạo ra các số từ 1 đến 3, tương ứng với Kéo, Búa, Bao trong trò chơi <b> One - Two - Three </b> (Oẳn tù tì) huyền thoại. Từ đây, mình đã quyết định chưa Oẳn tù tì vào làm một mini-game đầu tiên, với độ khó là dễ, nhưng cũng dựa vào may mắn là chính. Và để thêm phần thú vị cho minigame này, mình đã xây dựng một cốt truyện nhỏ và đồ hoạ phù hợp để tăng tính hình thức và bắt mắt. Một bật mí nhỏ là khi chấm game này cho mình, thầy giảng viên phải chơi mini game này 3 lần mới có thể dành được chiến thắng.

## 4. Thử thách thứ hai

Thử thách này mình lên ý tưởng từ nhân vật tượng nhân sư Sphinx trong Ai Cập cổ đại. Nhân sư sẽ canh gác lăng mộ và chỉ cho phép người đi qua nếu họ trả lời đúng được câu hỏi của nó. Mình quyết định sẽ đưa nhân vật chính đi qua lăng mộ và phải trả lời câu hỏi của Nhân sư này. Tuy nhiên, để đơn giản (vì đây mới là thử thách số 2) nên nhân vật sẽ có 7 lần đoán số tuổi của Sphinx, với điều kiện là tuổi của Nhân sư là nhỏ hơn 100. Nếu đoán sai thì tất nhiên, nhân vật chính sẽ dừng chân tại đây. Đến đây, nhiều bạn sẽ hiểu được tại sao mình để là 7 lần đoán. Đó là dựa theo thuật toán tìm kiếm nhị phân mà mình đã được học từ môn Nhập môn lập trình.

## 5. Thử thách thứ ba

Sau khi làm xong 2 thử thách trên, mình nghĩ mình cần nâng cấp độ khó cho game lên một chút, và đòi hỏi thao tác của người chơi nhiều hơn. Trong nhiều bộ phim truy tìm kho báu kinh điển, nhân vật chính sẽ đi qua một đường hầm với nhiều bẫy ở xung quanh, đòi hỏi sự khéo léo và dẻo dai của nhân vật. Với ý tưởng này, mình cũng tạo ra một đường hầm để đi tới chìa khoá của kho báu, với rất nhiều bẫy như rắn độc, hố sâu dung nham, vòng lửa,... đòi hỏi người chơi phải nhanh nhẹn điều khiển để tránh mất máu đi đến đích để nhận chìa khoá.

Phần này đòi hỏi mình làm tỉ mỉ, từ việc điều hướng các vòng lửa, năng lượng cho tới việc xử lý va chạm và cân bằng lượng máu để có được trải nghiệm game tốt nhất.

## 6. Thử thách cuối cùng

Việc mình kết thúc hành trình ở thử thách thứ 4 vì mình lười :v, nhưng thật ra mình nghĩ việc dừng ở minigame số 4 là đủ để mọi người có thể trải nghiệm toàn bộ game, và có thêm thời gian cho mình hoàn thiện phần code.

Ở phần này, nội dung thử thách là việc trốn chạy khỏi đám zombie bảo vệ kho báu trong một khoảng thời gian nhất định. Đây là thử thách mình tốn nhiều thời gian làm nhất (cũng là thử thách khó nhất) vì phải code rất nhiều phần như tự động bắn, tự động nâng cấp sức mạnh, di chuyển bản đồ khi nhân vật chuyển động... Đây là minigame mình lấy ý tưởng từ game <b>[Vampire Survivors](https://store.steampowered.com/app/1794680/Vampire_Survivors/)</b>, nhưng mình làm đơn giản hơn rất nhiều. Ngoài ra, việc tự tạo các hình ảnh vũ khí, zombie,... cũng khiến mình cảm thấy khá là vất vả. Nhưng sau cùng, mình cũng tạo được 1 bản gọi là tạm được của tựa game này.

## 7. Những phần phụ khác

Tất nhiên, để tạo được game Truy tìm kho báu này, không thể không kể đến những bản đồ, nhân vật, quái vật, vũ khí,... Về phần nhân vật, quái vật,... mình lấy từ trang <b>[OpenGameArt](https://opengameart.org)</b>. Đây là trang web chia sẻ miễn phí các spritesheet nhân vật, vũ khí,... thuộc dạng pixel 2D. Ngoài ra, mình cũng sử dụng phần mềm <b> PhotoshopCS6</b> để chỉnh sửa, cắt ghép các phần sao cho phù hợp, tạo map, menu, bản đồ kho báu cho từng màn.

Bên cạnh đó, mình cũng tạo các cốt truyện nhỏ trong mỗi thử thách, từ việc đi qua kim tự tháp để nhận chỉ dẫn cho đến vượt qua những cạm bẫy để lấy được chìa khoá kho báu, qua đó giúp đảm bảo tính hợp lý về cốt truyện tổng thể cho game.

## Tổng kết

Toàn bộ ở trên là những chia sẻ về quá trình làm game <b>Truy Tìm Kho Báu</b> của mình. Mọi người có thể tải mã nguồn của game tại <b>[đây](https://github.com/VanhGer/Game_Treasure_Hunt)</b>. Hy vọng bài viết này sẽ giúp mọi người có một góc nhìn khác về việc lập trình một project game, một góc nhìn hứng thú và sáng tạo hơn.
