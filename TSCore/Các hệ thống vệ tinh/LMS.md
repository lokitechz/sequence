## LMS
## Khái niệm
- **Auto và Semi - Auto**: Bản chất là có tích hợp xuống T24 khi hoạch xong T24 sẽ sync về LMS
- **Manual**: Thông tin không tích hợp gì với T24 mà sẽ là copy manual sau khi hoạch toán trên T24

- 1 luồng repayment cho ROC: sẽ không qua portal
    - LMS ở đây là 1 service chạy ngầm
    - ROC sẽ đẩy xuống lịch trả nợ xuống T24
    - T24 sẽ trả nợ
- 1 luồng là LMS là tất toán khoản vay
    - Tất toán 1 phần, giãn nợ là chưa có trên LMS
- Các nghiệp vụ liên quan đến tài sản đảm bảo sẽ call từ LMS -> CCM
- Logic liên quan đến tính giá move sang AE+ Pricing
- Với luồng repayment chưa có thì sẽ bổ sung workflow

- Mortgage on TD dùng sổ để làm biện pháp giảm thiểu rủi ro chứ không nhập kho tài sản
    + Nó là type trên CCM
    + Có step nhập kho TSĐB cho TD -> Khai báo trên T24 như bình thường

### Giải ngân hollowout khỏi T24 (Dùng TD làm tài sản thế chấp)
#### Q/A
1. Ngày trả gốc ban đầu ngày A chưa giải ngân sửa thành ngày B thì ngày B có đồng bộ xuống CCM ko?
2. Check lại xem đầu kênh gọi T24 thì T24 có validate file này ko?
4. Luồng repayment
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

### Note
- Dự kiến là LMS hoạch toán CCM và CCM đẩy vào T24
- Làm tài sản đảm bảo thông thường thì ko có bước đấy
- Với những khoản vay từ trước đến nay
    + Xử lý nợ quá hạn mà liên quan đến TD
- Với luồng sử dụng TD làm biện pháp giảm thiểu rủi ro
- Với những khoản nợ phát sinh nợ xấu
 + Xử lý tài sản của KH để làm nguồn tiền
- EA bảo R25 ko support upload file lô
- Hollow out đổ lô từ T24
    + T24 ko xử theo batch nữa
    + Luôn đẩy 1 by 1 sau khi parse file
- Hiện tại 1 phân hệ đổ lô có làm đc tất cả
- Template là các tribe tự quản lý
- T24 validate những gì -> LMS 
- CCM có màn hình edit mối quan hệ
- LMS recheck các điều kiện

# TO BE JOURNEY - Squad 2 Leding
- IDO: hệ thống dành cho nhân viên TCB của cá nhân
- BEP: hệ thống dành cho nhân viên TCB của doanh nghiệp
- SC: Smart Credit
- AA: Arrangement Architecture
- CCM: Centralized Collateral Management
- EWS (Early warning system): 
- LD (Loan Deposit): 
- OOTB -> Out of the box

## Các điểm key change của Retail Lending
### 1. Lưu trữ mỗi quan hệ không ở trong collateral status
- Tài sản bao gồm STK với nghĩa vụ trả nợ
- Hạn mức với nghĩa vụ trả nợ - Bundle Mortgage
- Nghĩa vụ trả nợ với nghĩa vụ trả nợ - Bundle Mortgage
Các
- Tách riêng nhiều loại hay 1 loại cho relationship type
### Chi tiết cần sửa
- LMS - Non STP
    - Update luông mortgage -> BA sẽ cần ghi detail chi tiết các luồng - bạn Dung
    - Sửa UI cho phép chọn 1 hoặc nhiều TSĐB - Hiện tại fix cứng dùng chính sổ mới mở để giảm thiểu rủi ro
    - Sổ để giảm thiểu rủi ro chứ kp TSĐB
    VD: Sổ 2 tỷ đảm bảo cho 1 tỷ 8
- LMS - STP: Loambard loan và My cash on TD
    - THêm 1 step để lưu mối quan hệ vào CCM
    - Thêm kết nối từ LMS STP
- Lombard loan ở quầy sẽ chuyển làm sang IDO làm hiện tại đang làm trên T24
    - Process disburment và repayment thì đã có r
    - Thêm UI mới trên IDO
    - Đặt entry point

### 2.Khi thực hiện repayment sẽ control được biz logic thì là centralize lại
- IDO repayment chung cho các sản phẩm
- New integration cho LMS - STP để biết call workflow repayment
- Revolk quan hệ với CCM khi terminate khoản vay
- Thêm workflow over due date với T24
- Repayment theo batch
- Revolk relationship sau khi repayment
- Update monitor job để check validity hằng ngày

### 3. Pricing
- Đổi lấy AE+ pricing -> Sửa đúng phần giá
- BA cần list ra bn endpoint
- AE+ Pricing
    - Thêm portal tạo campain và setup logic
    - Config rule cho pricing
- Portal set parameter trên databrick
- Data bricks sẽ lấy động theo param

### 4.Collection enhancement
- Đã đóng thì sẽ đi theo bước 3

## Term Deposit and Overdraft by Term Deposit (Vay cầm cố sổ tiết kiệm và vay thấu chi cầm cố sổ tiết kiệm - Cầm cố 100% STK)
1. Tạo yêu cầu vay cầm cố sổ TD hoặc mở thấu chi cầm cố sổ TD(trên IDO hoặc TCB Mobile)

2. ROC(secured-lending service) gửi yêu cầu approve limit lên Smart Credit -> Cấp hạn mức

3. Khách hàng lấy danh sách sổ TD được phép cầm cố

4. Khách hàng xác nhận khoản vay

5. ROC(secured-lending service) đẩy yêu cầu lên LMS để xử lý giải ngân khoản vay

6. LMS sẽ xử lý như sau
- LMS gọi xuống core khóa TD -> Core sẽ không cho phép khóa nếu đang trong trạng thái khóa
- LMS gọi xuống core cấp hạn mức và write in Secured value
- LMS gọi xuống CCM tạo collateral ID (Tài sản thế chấp)
- Liên kết collateral với hạn mức
- Giải ngân cho khách hàng
- Liên kết hạn mức với các trách nghiệm pháp lý

#### Q/A
- **Check LTV là gì?** -> LTV = Loan to value
- **Đang thấy có 2 step là ROC gọi CCM và SC gọi CCM?**
- **Làm rõ write in secured value là gì?** Write secure value = Ghi nhận giá trị của tài sản đảm bảo
- **Cần làm rõ trách nghiệm pháp lý là những gì?**

# Term Deposit - Mortgage (Dùng STK để tăng tỷ lệ cho vay trên giá trị tài sản)
1. Phê duyệt case Mortgage Smart Credit
- Làm rõ step Quy trình phê duyệt bao gồm việc sử dụng TD để giảm thiểu rủi ro?
- Sync up xuống CCM -> Đã có
- Request lên LMS để tạo case

2. LMS
- Tạo case
- Lập kế hoạch thế chấp với chi tiết số tiền được đảm bảo bằng TD
- Lưu thông tin làm biện pháp đảm bảo

3. CCM
- Chấp nhận tài sản đảm bảo và nhập kho

4. LMS thực hiện kí hợp đồng và giải ngân
- LMS sẽ đẩy thông tin xuống CCM để lưu thông tin quan hệ đặc biệt giữa TD và LD và thời gian hiệu lực của mối quan hệ
- Gọi xuống core để khóa TD
- Mở AA Lending
- Type linh động
- Bóc riêng likage đúng bằng giá trị bằng số tài tiền vay
- **LMS sẽ phải tính vadility của mối quan hệ trc khi lưu vào CCM**

5. Trigger đẩy sang LMS đúng số tiền phải trả và lãi

# Bundle
- Xóa linkage giữa liability với Collateral
- Secure tất toán xong thì in active limit
- Ở quầy

# Settlement My Cash by TD (Thanh toán thấu chi bằng tài khoản tiền gửi có kì hạn)

1. User chọn **My Cash**

2. Lấy danh sách TD từ Redis Cache
- unsecured-leding service gọi Redis để lấy dữ liệu cache
- **Cache Hit**: có giá trị -> Chuyển sang step 3
- **Cache Miss**: không có giá trị chuyển xuống step 2.1
    - 2.1. Lấy danh sách TD trong database của service
        - **Có dữ liệu**: trả ra danh sách TD
        - **Ko có dữ liệu**: gọi xuống T24 để lấy danh sách TD

3. Lặp danh sách TD gọi xuống T24 để lấy thông tin chi tiết của từng TD

4. Validate tổng số tiền các TD cộng lại so với số tiền cần phải tất toán

5. Trả về danh sách từng TD với kết quả validate

6. Review và confirm giao dịch

7. Request thanh toán lên LMS

8. lms-repayment service trên LMS nhận request và validate sau đó lưu vào DB với status = INIT

9. Gọi xuống T24 để check COB sau đó authen vào Camuda engine

10. Camuda trigger task mở khóa TD
- **Thành công**: call back về lms-repayment service để thực thi chuyển sang Step 11
- **Thất bại**: thực hiện retry

11. Camuda trigger task tất toán TD
- **Thành công**: end luồng
- **Thất bại**: trả ra mã lỗi và thực hiện rollback

2-4-2026
- LMS 7 sang 8 là call API
- Bổ sung thông tin về mối quan hệ ts xd xem ở trên SC hay LMS
- Bước 7 sang bước 8 clear lại API Spec với RB12?
- Luồng auto có thể đi như này, còn luồng manual sẽ hoạch tài sản như nào? Chắc chắn k làm trên T24
- Hoạch toán TSĐB?

