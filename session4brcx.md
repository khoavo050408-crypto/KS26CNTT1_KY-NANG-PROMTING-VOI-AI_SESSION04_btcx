# BÁO CÁO THIẾT KẾ GIẢI PHÁP PROMPTING NÂNG CAO (LOGIC & CHUỖI)

## 1. Mục tiêu bài tập

Ứng dụng kiến thức Kỹ thuật Prompting Nâng cao (Logic & Chuỗi) kết hợp khung cấu trúc **CREATE** để thiết kế một giải pháp tự động hóa công việc thực tế cho cửa hàng, rèn luyện tư duy lập luận chặt chẽ trước khi triển khai lập trình (code).

## 2. Bối cảnh & Vấn đề thực tế

* **Tên giải pháp:** Hệ thống Tự động hóa Phân tích & Xử lý Khiếu nại/Phản hồi Khách hàng cho Cửa hàng Bán lẻ.
* **Vấn đề thực tế:** Cửa hàng nhận được hàng trăm đánh giá và yêu cầu hỗ trợ từ khách hàng mỗi ngày qua nhiều kênh (Fanpage, Email, Zalo). Dữ liệu đầu vào thường lộn xộn, thiếu thông tin (ví dụ: thiếu mã đơn hàng, phản hồi cảm xúc chung chung không rõ vấn đề), hoặc chứa các nội dung nhiễu.
* **Giải pháp kỹ thuật:** Xây dựng quy trình áp dụng **Chain-of-Thought (CoT)**, **Prompt Chaining (Chuỗi logic đa bước)** theo chuẩn khung **CREATE** (Context - Request - Explanation - Action - Tone - Extras) nhằm nhận diện và xử lý các trường hợp dữ liệu đứt gãy, thiếu hụt.

## 3. Bước 1: Thiết kế giải pháp (I/O)

### 3.1. Input (Dữ liệu đầu vào)

* **Thông tin giao dịch & Khách hàng:** Kênh tiếp nhận, Mã đơn hàng, Tên khách hàng.
* **Nội dung thô (Raw Text):** Đoạn chat hoặc nội dung phản hồi/khiếu nại nguyên bản từ khách hàng (có thể chứa lỗi chính tả, thông tin mâu thuẫn, thiếu hụt hoặc cảm xúc tiêu cực).

### 3.2. Output (Cấu trúc kết quả mong muốn)

Kết quả trả về tuân thủ nghiêm ngặt định dạng cấu trúc gồm 4 phần rõ ràng:

1. **Trạng thái dữ liệu:** Phân loại hợp lệ hay cảnh báo thiếu/sai lệch dữ liệu (`WARNING_INCOMPLETE_DATA`).
2. **Phân tích logic (CoT Summary):** Xác định rõ vấn đề gốc rễ (Root Cause), điểm đứt gãy dữ liệu và mức độ nghiêm trọng (Thấp / Trung bình / Cao / Khẩn cấp).
3. **Đề xuất hành động cho nhân viên:** Quy trình xử lý cụ thể cho bộ phận CSKH/Kho vận.
4. **Mẫu tin nhắn phản hồi khách hàng:** Nội dung tin nhắn gửi khách hàng lịch sự, đồng cảm, chuyên nghiệp và giải quyết đúng trọng tâm.

## 4. Bước 2: Viết Prompt theo khung CREATE & Chạy thử

### 4.1. Prompt thực thi hoàn chỉnh (Chuẩn khung CREATE)

Dưới đây là mẫu Prompt hoàn chỉnh được thiết kế theo đúng 6 bước của khung **CREATE** tích hợp tư duy **Chain-of-Thought** và các câu lệnh điều kiện bắt bẫy dữ liệu lỗi:

```
### C - CONTEXT (Bối cảnh & Vai trò)
Bạn là Trưởng nhóm Tự động hóa Vận hành và Chăm sóc Khách hàng (CSKH) tại hệ thống cửa hàng bán lẻ thời trang. Hàng ngày, cửa hàng tiếp nhận lượng lớn tin nhắn khiếu nại qua nhiều kênh với dữ liệu đầu vào thường xuyên bị thiếu, sai lệch hoặc chứa cảm xúc tiêu cực từ khách hàng.

### R - REQUEST (Yêu cầu cốt lõi)
Hãy phân tích chuỗi dữ liệu phản hồi của khách hàng dưới đây thông qua tư duy lập luận từng bước (Chain-of-Thought), phát hiện các điểm đứt gãy dữ liệu, đề xuất hướng xử lý nội bộ và soạn thảo tin nhắn phản hồi khách hàng chuẩn xác.

# DỮ LIỆU ĐẦU VÀO (INPUT):
- Kênh tiếp nhận: [Zalo Official Account]
- Mã đơn hàng: [Đang trống / Không rõ]
- Tên khách hàng: [Chị Lan]
- Nội dung thô từ khách: "Shop làm ăn kiểu gì thế giao cái áo rách chỉ mà đổi trả khó khăn quá gọi hoài không ai bắt máy bực mình thật đấy hoàn tiền gấp cho tôi nhé đơn hàng hình như là DH98... gì đó không nhớ rõ."

### E - EXPLANATION (Giải thích & Ràng buộc)
Để đảm bảo hệ thống vận hành an toàn và không gây khủng hoảng truyền thông, bạn phải tuân thủ nghiêm ngặt các ranh giới sau:
- TUYỆT ĐỐI KHÔNG tự suy diễn hoặc bịa đặt mã đơn hàng đầy đủ nếu dữ liệu đầu vào bị khuyết thiếu hoặc mập mờ.
- TUYỆT ĐỐI KHÔNG đổ lỗi cho khách hàng hoặc đơn vị vận chuyển.
- Nếu phát hiện dữ liệu bị thiếu hoặc sai lệch (ví dụ: mã đơn hàng không đầy đủ "DH98..."), bắt buộc gắn cờ trạng thái `WARNING_INCOMPLETE_DATA` và yêu cầu xin lại thông tin từ khách trong phần phản hồi.

### A - ACTION (Các bước hành động - Chain-of-Thought)
Hãy thực hiện tuần tự 4 bước lập luận sau trước khi xuất ra kết quả:
1. Kiểm tra tính toàn vẹn dữ liệu (Data Validation): Đối chiếu các trường Mã đơn hàng, Tên khách và Nội dung để tìm điểm thiếu hụt hoặc đứt gãy.
2. Phân tích vấn đề cốt lõi (Root Cause Analysis): Tách bạch các nguyên nhân gây bức xúc (lỗi chất lượng sản phẩm, quy trình đổi trả, tốc độ phản hồi tổng đài).
3. Đánh giá mức độ ưu tiên: Phân loại mức độ nghiêm trọng (Thấp / Trung bình / Cao / Khẩn cấp) và đề xuất hành động cụ thể cho nhân viên CSKH.
4. Soạn thảo phản hồi: Viết tin nhắn gửi khách hàng bao gồm: Lời xin lỗi chân thành -> Xác nhận phương án hỗ trợ -> Xin lại thông tin bị thiếu một cách khéo léo.

### T - TONE (Giọng điệu văn phong)
Văn phong chân thành, đồng cảm, cầu thị, chuyên nghiệp và xoa dịu cảm xúc. Sử dụng xưng hô "em" - "chị/anh" lịch sự, gần gũi.

### E - EXTRAS (Định dạng đầu ra & Bổ sung)
Xuất kết quả dưới dạng báo cáo cấu trúc rõ ràng gồm đúng 4 mục sau (không viết thêm lời dẫn thừa thãi):
1. **[TRẠNG THÁI DỮ LIỆU]:** (Hợp lệ / Cảnh báo thiếu dữ liệu kèm mã lỗi)
2. **[PHÂN TÍCH LOGIC (CoT Summary)]:**
   - Vấn đề cốt lõi: ...
   - Điểm đứt gãy dữ liệu: ...
   - Mức độ nghiêm trọng: ...
3. **[ĐỀ XUẤT HÀNH ĐỘNG CHO NHÂN VIÊN]:** ...
4. **[MẪU TIN NHẮN PHẢN HỒI KHÁCH HÀNG]:** ...
```

### 4.2. Kết quả chạy thử (AI Response)
1. giả sử yêu cầu khách hàng
 
 Shop ơi, mình thật sự quá thất vọng. Mình đặt 2 chiếc áo sơ mi màu trắng size M mà shop giao có 1 chiếc, chiếc còn lại thì lại là màu đen size S. Mình đã nhắn tin cho shop từ hôm qua nhưng không thấy ai phản hồi. Gọi hotline cũng không được. Đây không phải lần đầu shop giao sai hàng cho mình đâu nhé. Lần trước mình cũng gặp tình trạng tương tự nhưng bỏ qua rồi. Bây giờ mình muốn shop hoàn tiền chiếc áo giao sai và có phương án xử lý rõ ràng, chứ đừng bắt mình chờ thêm nữa. Mã đơn hàng hình như DH125 hay DH152 gì đó, mình không nhớ chính xác. 

 2. kết quả AI

 [TRẠNG THÁI DỮ LIỆU]: Cảnh báo thiếu dữ liệu (WARNING_INCOMPLETE_DATA - Mã đơn hàng mập mờ giữa hai giá trị DH125 và DH152, thiếu số điện thoại đặt hàng và hình ảnh đối chiếu sản phẩm giao sai).

[PHÂN TÍCH LOGIC (CoT Summary)]:

Vấn đề cốt lõi: Khách hàng gặp sự cố nghiêm trọng và lặp lại: (1) Đơn hàng hiện tại giao sai quy cách sản phẩm (đặt 2 áo sơ mi trắng size M nhưng nhận 1 áo trắng size M và 1 áo đen size S); (2) Chậm trễ hỗ trợ trên cả hai kênh tin nhắn và hotline từ hôm qua; (3) Đây là lần thứ hai khách gặp lỗi giao sai hàng tại cửa hàng, dẫn đến tâm lý mất niềm tin và yêu cầu hoàn tiền dứt khoát.

Điểm đứt gãy dữ liệu: Khách không nhớ chính xác mã đơn hàng (DH125 hay DH152), không có tên khách hàng/số điện thoại đặt hàng kèm theo và chưa có ảnh chụp thực tế chiếc áo giao sai để kho xác minh.

Mức độ nghiêm trọng: Khẩn cấp / Rất cao (Khách hàng cũ bị lặp lại lỗi vận hành lần thứ 2, đã chờ đợi hơn 24 giờ không được phản hồi và đang có mức độ thất vọng cực lớn, nguy cơ rời bỏ thương hiệu hoặc khiếu nại công khai rất cao).

[ĐỀ XUẤT HÀNH ĐỘNG CHO NHÂN VIÊN]:

Bước 1 (Gắn cờ & Không đoán mã): Gắn cờ WARNING_INCOMPLETE_DATA và gắn nhãn ưu tiên VIP/Khẩn cấp cho hội thoại; tuyệt đối không tự ý tra cứu hoặc thao tác hoàn tiền trên một trong hai mã DH125 hay DH152 khi chưa xác thực.

Bước 2 (Phản hồi hỏa tốc & Xin lỗi chân thành): Phản hồi ngay lập tức, nhận hoàn toàn trách nhiệm về lỗi đóng gói lặp lại và sự chậm trễ của bộ phận CSKH/hotline.

Bước 3 (Xác thực dữ liệu tối giản): Xin khách số điện thoại đặt hàng (để hệ thống tự lọc ra đúng đơn giữa DH125 và DH152) kèm 1 ảnh chụp chiếc áo đen size S giao nhầm nhằm hoàn tất thủ tục nhanh nhất, tránh hỏi dồn nhiều thông tin gây phiền.

Bước 4 (Kích hoạt quy trình hoàn tiền ưu tiên & Đền bù thiện chí): Ngay khi khớp được đơn hàng, tiến hành hoàn tiền ngay lập tức cho chiếc áo giao sai mà không bắt khách chờ đợi lâu, đồng thời đề xuất tặng kèm một voucher/mã giảm giá đặc biệt cho lần mua sau để bù đắp trải nghiệm không tốt ở cả 2 lần mua hàng.

[MẪU TIN NHẮN PHẢN HỒI KHÁCH HÀNG]:
Dạ em chào anh/chị ạ. Trước hết, em xin gửi lời xin lỗi chân thành và sâu sắc nhất đến anh/chị vì sự cố lần này ạ. Em thật sự rất tiếc khi bên em lại để xảy ra sai sót trong khâu đóng gói sản phẩm đến lần thứ hai, đồng thời kênh tin nhắn và hotline lại phản hồi chậm trễ khiến anh/chị phải chờ đợi từ hôm qua đến nay.

Bên em xin nhận hoàn toàn trách nhiệm về sự bất tiện này và sẽ ưu tiên xử lý hoàn tiền ngay cho chiếc áo giao sai theo đúng mong muốn của anh/chị, tuyệt đối không để mình phải chờ đợi thêm nữa ạ.

Để em có thể xác định chính xác đơn hàng của mình giữa hai mã DH125 và DH152 và làm lệnh hoàn tiền liền cho anh/chị, anh/chị cho em xin số điện thoại đặt hàng cùng một tấm ảnh chụp chiếc áo màu đen size S giúp em nhé ạ. Ngay khi nhận được thông tin, em sẽ trực tiếp xử lý và cập nhật kết quả hoàn tiền cho anh/chị ngay lập tức ạ!

