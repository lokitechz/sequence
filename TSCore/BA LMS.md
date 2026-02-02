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

## LMS
- Các nghiệp vụ liên quan đến tài sản đảm bảo sẽ call từ LMS -> CCM
- Khi 
- Logic liên quan đến tính giá move sang AE+ Pricing
- Với luồng repayment chưa có thì sẽ bổ sung workflow