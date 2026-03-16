## LMS
- **Auto và Semi - Auto**: Bản chất là có tích hợp xuống T24 khi hoạch xong T24 sẽ sync về LMS
- **Manual**: Thông tin không tích hợp gì với T24 mà sẽ là copy manual sau khi hoạch toán trên T24

- 1 luồng repayment cho ROC: sẽ không qua portal
    - LMS ở đây là 1 service chạy ngầm
    - ROC sẽ đẩy xuống lịch trả nợ xuống T24
    - T24 sẽ trả nợ
- 1 luồng là LMS là tất toán khoản vay
    - Tất toán 1 phần, giãn nợ là chưa có trên LMS
- Check LMS đã connect vs DMS chưa

- Các nghiệp vụ liên quan đến tài sản đảm bảo sẽ call từ LMS -> CCM
- Logic liên quan đến tính giá move sang AE+ Pricing
- Với luồng repayment chưa có thì sẽ bổ sung workflow

### Giải ngân hollowout khỏi T24 (Dùng TD làm tài sản thế chấp)
#### Q/A
1. Ngày trả gốc ban đầu ngày A chưa giải ngân sửa thành ngày B thì ngày B có đồng bộ xuống CCM ko?
2. Check lại xem đầu kênh gọi T24 thì T24 có validate file này ko?
3. 
#### Giải ngân liên quan đến TD
- SP1: Vay thế chấp (sử dụng TD như 1 tài sản thế chấp thông thường)

- SP2: Dùng TD làm biện pháp giảm thiểu rủi ro
    - Gọi sang CCM làm 2 việc
    - LMS gọi sang CCM
        + TD nào sẽ đảm bảo cho khoản vay nào
        + CCM trigger -> LMS -> T24
    - Chi nhánh tự phong tỏa (trước đây)
    - Đến ngày trả nợ đầu tiên hệ thống nào trigger để gọi sang LMS -> Đi hoạch toán
- Ngày trả gốc ban đầu ngày A chưa giải ngân sửa thành ngày B

- Lombard TD: gồm 3 SP
Personal loan by TD branch -> làm trên IDO
Personal loan by TD online -> giải ngân trên kênh
Overdraft by TD -> Có phải Mycash on TD
    - GAP 97
- Để định nghĩa ra vin đặc thù hiện tại bây giờ SC có đẩy gì sang ko
#### Giải ngân theo lô
- Hollow ra LMS
- T24 hiện đang validate thông tin bút toán nào đúng thì trả đúng bút toán nào sai sẽ trả sai
- Check lại xem đầu kênh gọi T24 thì T24 có validate file này ko
- Xử lý tiếp các dòng đi tiếp từng bút toán