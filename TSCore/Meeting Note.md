## Note 26-01-2026
- Đỏ là mới
- Đen là hiện tại
- Đang write tài sản đảm bảo sang T24 -> Link thông tin tài sản đảm bảo sang CCM
- Move sang IDO
- TSĐB đi theo món
- Đến hạn thì vẫn T24
- Mycash on TD sửa lại luồng write vào CCM
- CCM lưu theo likange
- AA lending open xong return 1 số thông tin validity của AA về
- CCM trigger hết
- RBG Pricing có sẵn đã tích hợp vs ROC -> Đang chạy cho TD với AE+ pricing
- Ko đụng vào sale tool
- Pricing thuần túy về ngoại lệ về giá
- Sửa thông tin LD trên LMS
- Margin fix trong hợp đồng
- Margin tính từ lúc giải ngân, review lãi suất định kì là T24
- Lưu tất cả info và DB

## Note 27-01-2026
- Làm phong tỏa trước đổ tiền vào TK thanh toán
- Hàng tháng nếu quá hạn là tất toán sổ luôn (cuối kì sử sổ tiết kiệm)
- Sau 10 ngày tất toán
- Ko customize gì ở T24
- My cash on TD - CCM will handle and LMS

- Sửa chính xác những gì màn hình nào
- Lấy Epic trong delta log những yêu cầu nào của cần move ra
- Có nhiều epic đi theo high level

1. Book DL final GAP
2. Ngồi với từng BA check từng GAP 1

## Note 29-01-2026
094: Đang thảo luận
- AC.LOCKEVENTS
- Số tiền kia là số lock
- API đầu account number, lock amount and lock type
- min/max 
- customer type: MIN/MAX


## Note 2-2-2026
Brief: 
- Sẽ chỉ handle phần key change và hollow out những gì T24 handle
- Sẽ handle những gì T24 ko cover được

- T24 đang store toàn bộ mối quan hệ -> Move ra store trên CCM

1. Update mortgage journey
- Sửa UI cho phép chọn nhiều tài sản đảm bảo
- Lấy danh sách TSĐB từ CCM
- Tích hợp với CCM để kiểm tra chập nhận TSĐB và lưu trữ mối quan hệ giữa STK/Tài sản với nghĩa vụ trả nợ
- Không được nhận là tài sản đảm bảo nhưng vẫn cần store info (làm biện pháp giảm thiểu rủi ro) chỉ dừng lại ở bước store thông tin trên CCM
- Chỉ có TD

2. Update workflow giải ngân

3. ROC
- Có bảng local trên T24 để lưu
- CCM sẽ lưu MyCash c9 bảo bên dưới

4. Màn hình vay TD
- New UI for IDO thuần túy query infomation
- Thấu chi cầm cố STK và vay cầm cố STK sẽ cùng trên 1 màn của TD

5. Repayment
- Sẽ làm hết trên IDO
- Chỉ kho lock sổ thành công thì mới disburment
- Revoke relationship
- Gồm cả KH trả nợ và trigger từ CCM
- Repayment over due date mới từ đầu cover trigger đên
    - KH có nhu cầu trả nợ quá hạn
    - T24 đã có job chạy
- Vendor phải expose API tiêu chuẩn phải trả ra 

6. Terminate khoản vay chỉ cho Lombard
- 

7. Revoke relationship -> Không lưu mối quan hệ đó nữa nếu không khi check sẽ vẫn còn

8. Entrusted loan

Repayment over due date - LMS chưa có
- DMS đổ data về cho LMS -> Tự động
- Làm rõ thêm
- Đang có 2 màn hình trên T24 (huong)
    - Thu nợ theo ID 
- Đang có API nợ quá hạn

Response time của các màn hình T24

## Note 4-2-2026
### DMS
- Change Management(CM)
    - Định hướng đào tạo
    - 
- Lock TD là bắt buộc trước khi đẩy xuống LMS để giải ngân
- IDO -> Secure Lending service -> T24 Lock TD
- IDO đã có
- Repayment sẽ call LMS hết -> UI làm mới là màn hình riêng
- Call để query info là ROC làm API mới lấy repayment amount cho từng khoản vay
- Thay đổi cơ chế lấy lãi (xóa code cũ + đổi sang lấy mới)
- Lãi suất áp dụng tại luồng giải ngân
- PIL 
- ROC check tài khoản

## 