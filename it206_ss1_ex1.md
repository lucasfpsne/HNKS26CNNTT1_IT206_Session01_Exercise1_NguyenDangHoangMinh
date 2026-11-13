# Lựa chọn Zero-shot và Few-shot trong phân loại phản hồi khách hàng

## 1. Mục tiêu

Thiết kế và so sánh hai cách viết prompt để phân loại phản hồi khách hàng:

- **Zero-shot:** Chỉ cung cấp hướng dẫn, không có ví dụ.
- **Few-shot:** Cung cấp hướng dẫn kèm một số ví dụ mẫu.

Các phản hồi được chia thành ba nhóm: **TÍCH CỰC**, **TIÊU CỰC** và **CẦN HỖ TRỢ**.

## 2. Thiết kế hai phương án prompt

### Phương án 1: Zero-shot

Bạn là trợ lý phân loại phản hồi khách hàng cho cửa hàng.

Hãy phân loại tin nhắn theo các quy tắc:

- TÍCH CỰC: Khách khen sản phẩm ngon, giá rẻ.
- TIÊU CỰC: Khách chê giao hàng chậm, phục vụ chậm hoặc thái độ tệ.
- CẦN HỖ TRỢ: Khách hỏi địa chỉ cửa hàng hoặc hỏi giá.

Chỉ trả về một nhãn: TÍCH CỰC, TIÊU CỰC hoặc CẦN HỖ TRỢ.

Tin nhắn cần phân loại:
```
{TIN_NHAN_KHACH_HANG}
```

### Phương án 2: Few-shot

Bạn là trợ lý phân loại phản hồi khách hàng cho cửa hàng.

Hãy phân loại tin nhắn theo các quy tắc:

- TÍCH CỰC: Khách khen sản phẩm ngon, giá rẻ.
- TIÊU CỰC: Khách chê giao hàng chậm, phục vụ chậm hoặc thái độ tệ.
- CẦN HỖ TRỢ: Khách hỏi địa chỉ cửa hàng hoặc hỏi giá.

Dưới đây là 3 ví dụ mẫu:

**Ví dụ 1:**
- Tin nhắn: "Đồ ăn ngon mà giá lại rẻ nữa!"
- Nhãn: TÍCH CỰC

**Ví dụ 2:**
- Tin nhắn: "Quán phục vụ chu đáo quá, bắt khách đợi hẳn 1 tiếng!"
- Nhãn: TIÊU CỰC

**Ví dụ 3:**
- Tin nhắn: "Cho mình hỏi cửa hàng ở đâu và món này giá bao nhiêu?"
- Nhãn: CẦN HỖ TRỢ

Hãy phân loại tin nhắn tiếp theo theo cách tương tự.
Chỉ trả về một nhãn: TÍCH CỰC, TIÊU CỰC hoặc CẦN HỖ TRỢ.

Tin nhắn cần phân loại:
```
{TIN_NHAN_KHACH_HANG}
```

## 3. Bảng so sánh Trade-off

| Tiêu chí | Giải pháp 1: Zero-shot | Giải pháp 2: Few-shot |
| --- | --- | --- |
| Độ dễ hiểu | Cao, ngắn gọn và dễ viết nhanh. | Có ví dụ dễ hình dung nhưng prompt dài hơn, cần chọn mẫu phù hợp. |
| Tốn token | Ít token hơn vì chỉ có hướng dẫn. | Nhiều token hơn vì có thêm ví dụ. |
| Xử lý câu mỉa mai | Dễ nhầm hơn nếu chỉ chú ý từ khen và bỏ qua ngữ cảnh. | Có ví dụ định hướng nên có thể nhận diện tốt hơn. |
| Công sức chuẩn bị | Thấp, chỉ cần xác định luật phân loại. | Cao hơn, cần chuẩn bị ví dụ đúng và đại diện. |
| Trường hợp phù hợp | Phản hồi đơn giản, ý nghĩa rõ ràng. | Phản hồi phức tạp, có mỉa mai hoặc cách diễn đạt dễ gây nhầm. |

**Lưu ý:** Zero-shot vẫn có thể nhận diện đúng câu mỉa mai. Few-shot không bảo đảm đúng mọi trường hợp; hiệu quả phụ thuộc vào mô hình và chất lượng ví dụ.

## 4. Thử nghiệm và đánh giá

### 4.1. Dữ liệu thử nghiệm

"Quán phục vụ chu đáo quá, bắt khách đợi hẳn 1 tiếng!"

**Nhãn đúng: TIÊU CỰC.**

Giải thích: Khách đang phàn nàn về thời gian chờ quá lâu. Cụm từ “chu đáo quá” mang ý nghĩa mỉa mai, không phải lời khen thật.

### 4.2. Kết quả phân tích minh họa

**Giới hạn:** Bảng dưới đây là đánh giá minh họa trong câu trả lời này, chưa phải kết quả của hai lượt chạy độc lập trên mô hình.

| Phương án | Nhãn khi áp dụng prompt để phân tích | Đánh giá |
| --- | --- | --- |
| Zero-shot | TIÊU CỰC | Có thể xác định đúng nhờ chi tiết “bắt khách đợi hẳn 1 tiếng”. |
| Few-shot | TIÊU CỰC | Có ví dụ trùng với câu kiểm tra và đã được gán nhãn đúng. |

### 4.3. Cách thực hiện thử nghiệm thực tế

1. Mở một cuộc trò chuyện mới, dán prompt Zero-shot và thay `{TIN_NHAN_KHACH_HANG}` bằng câu mỉa mai.
2. Mở cuộc trò chuyện mới khác trên cùng mô hình, thực hiện tương tự với prompt Few-shot.
3. Ghi lại nguyên văn hai kết quả và đối chiếu với nhãn **TIÊU CỰC**.

Nếu cả hai đều trả về **TIÊU CỰC**, hai phương án có độ chính xác bằng nhau trên câu thử nghiệm này. Nếu chỉ Few-shot trả về đúng, Few-shot chính xác hơn trong lần thử đó.

**Hạn chế của phép thử:** Câu kiểm tra đã xuất hiện trong ví dụ Few-shot, nên kết quả đúng chưa chứng minh mô hình nhận diện tốt các câu mỉa mai mới. Muốn so sánh đáng tin cậy hơn, cần thử thêm nhiều câu chưa có trong ví dụ.

## 5. Kết luận lựa chọn

Đối với bài toán có phản hồi mỉa mai, em chọn **Few-shot** vì ví dụ mẫu giúp AI hiểu rõ cách gán nhãn theo ý nghĩa thực tế. Đánh đổi là prompt dài hơn, tốn thêm token và cần công sức chuẩn bị ví dụ.

Tuy nhiên, để kết luận phương án nào chính xác hơn trong thực tế, cần chạy cả hai prompt trên cùng một bộ phản hồi mới và so sánh số câu phân loại đúng.
