## High level solution and key change
### 1. Enhancement for pricing
- **Hiện trạng**: T24 không có module đáp ứng việc tùy chỉnh lãi suất hiện tại đang phải customize với nhiều hạn chế và ảnh hưởng đến hiệu suất của LD
- **Giải pháp**: Tách riêng module cá nhân hóa giá cả ra 1 Platform riêng
- **Chi tiết**: Chưa chốt có làm hay không
### 2. Accouting for Collateral
- **Hiện trạng**: Hiện tại đang quản lý TSĐB trên T24
- **Giải pháp**: Chuyển sang quản lý TSĐB trên CCM (Centralize Collateral Management) -> Phụ thuộc vào dự án RB12
- **Giả định**: Dự án RB12 xong là sẽ có toàn bộ kết nối từ LMS sang CCM
### 3. Centralize the risk mitigation method of lending
- **Hiện trạng**: Hiện tại đang quản lý mối quan hệ giữa tài sản và nợ phải trả trên T24
- **Giải pháp**: Chuyển sang quản lý trên CCM
### 4. Hollow out the disbursement routine for flexibility
- **Hiện trạng**: Hiện tại phần giải ngân đang làm trên T24
- **Giải pháp**: Chuyển sang xử lý trên LMS để tăng tính linh hoạt và không ảnh hưởng đến perfomance của T24
### 5. Repyament centralization
- **Hiện trạng**: Quy trình trả nợ khoản vay TD theo yêu cầu đang làm trên T24
- **Giải pháp**: Chuyển sang LMS tái sử dụng quy trình hiện tại đã có cho việc trả nợ khoản vay TD
### 6. Optimize repayment process for Debt Collection
- **Hiện trạng**: Chức năng mặc định của T24 liên quan đến quy trình thu hồi nợ không đáp ứng tất cả các y/c kinh doanh. Nếu thực hiện trên T24 sẽ có GAP size XXL
- **Giải pháp**:
    - Chuyển phạm vi mà T24 ko xử lý được ra LMS
    - Tận dụng chức năng có sẵn của T24 liên quan đến việc khóa tài khoản + quy trình xử lý trả nợ quá hạn trên T24
### 7. Ommi channel
- **Hiện trạng**: Chưa tập trung
- **Giải pháp**:
    - Thu hẹp phạm vi hỗ trợ của T24 browser cho chi nhánh -> IDO
    - Thu hẹp phạm vi hỗ trợ của T24 browser cho OPS -> LMS
    - Tất cả logic quy về 1 nguồn
        + Application process sẽ trên ROC
        + Giải ngân và trả nợ trên LMS
        + Phần còn lại sẽ trên T24
### 8. Optimize home credit
- **Hiện trạng**: T24 chưa hỗ trợ giám sát khoản vay ủy thác nên T24 đang phải custom sử dụng lại LD truyền thống cho LD ủy thác
- **Giải pháp**: Thiết lập sản phẩm cho vay AA cho các khoản vay ủy thác
