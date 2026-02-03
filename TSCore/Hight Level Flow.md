# TO BE JOURNEY - Squad 2 Leding
- IDO: hệ thống dành cho nhân viên TCB của cá nhân
- BEP: hệ thống dành cho nhân viên TCB của doanh nghiệp
- SC: Smart Credit
- AA: Arrangement Architecture
- CCM: Centralized Collateral Management
- EWS (Early warning system): 
- LD (Loan Deposit): 
- OOTB -> Out of the box
- RCL ()

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


