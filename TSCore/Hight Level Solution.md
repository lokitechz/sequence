## High level solution and key change
- Đến từ 2 thứ
    + LD -> AA: Bị GAP nên nếu làm trên core là cần customize -> Vendor charge tiền khá nhiều
    + IT của Bussiness y/c hệ thống core phải đáp ứng tuy nhiên khi đánh giá với vendor thì core không đáp ứng được. Chỉ đáp ứng 1 phần và 1 phần hollow out ra ngoài
### 1. Enhancement for pricing
- **Hiện trạng**: T24 không có module đáp ứng việc tùy chỉnh lãi suất hiện tại đang phải customize với nhiều hạn chế và ảnh hưởng đến hiệu suất của LD
    - Hiện tại giá đang được tính toán trên T24
    - ROC đang handle 1 phần
    - Nếu giữ nguyên cách hiện tại sử dụng cơ chế key lãi suất tạo ra các bảng key lãi suất mapping và dựa vào customer info để biết map với key nào và bộ key bị limit về mặt số lượng. Vendor tư vấn không nên sử dụng theo cách này nữa và sẽ có 1 lúc bị limit không sinh ra thêm được -> Move ra ngoài core
    - Theo thông tin mới nhất
        + MFT Unsecured a Tuấn khối RBG sẽ handle toàn bọ RBG pricing -> Sẽ handle cả phần này
- **Giải pháp**: Tách riêng module cá nhân hóa giá cả ra 1 Platform riêng (Chưa chắc phải làm)
- **Chi tiết**: Chưa chốt có làm hay không
### 2. Accouting for Collateral
- **Hiện trạng**: Hiện tại đang quản lý TSĐB trên T24
- **Giải pháp**: Chuyển sang quản lý TSĐB trên CCM (Centralize Collateral Management) -> Phụ thuộc vào dự án RB12 
    + Kể cả thông tin collateral được ghi và T24 thì source of trust của TSĐB vẫn là CCM
- **Giả định**: Dự án RB12 xong là sẽ có toàn bộ kết nối từ LMS sang CCM
### 3. Centralize the risk mitigation method of lending
- **Hiện trạng**: Hiện tại đang quản lý mối quan hệ giữa tài sản và nợ phải trả trên T24
    + TSĐB là 1 risk mitigation method có thể có hình thức khác như kí quỹ
    + Không đăng kí gd đảm bảo thì không phải TSĐB
    + 1 sổ được thế chấp cho 1 khoản vay sẽ khác với việc 1 sổ được block để làm biện pháp giảm thiểu rủi ro cho khoản vay
    + Khi ghi nhận trên sổ tài chính của bank thì giao dịch đảm bảo sẽ được coi như 1 tài sản của bank. Nếu như chỉ block thì sẽ ko được coi là tài sản của bank
    + Chuyển từ LD sang AA thì vendor sẽ phải customize -> Charge tiền move ra CCM
- **Giải pháp**: Chuyển sang quản lý trên CCM
### 4. Hollow out the disbursement routine for flexibility
- **Hiện trạng**: Hiện tại phần giải ngân đang làm trên T24
    + Khi muốn vay 1 khoản A chu trình giải ngân đã move
    + TD loan vay cầm cố sổ TK - mỗi SP n ày đang làm trên T24
    + Logic parsing file
    + Bị charge tiền cao
- **Giải pháp**: Chuyển sang xử lý trên LMS để tăng tính linh hoạt và không ảnh hưởng đến perfomance của T24 - Luồng STP
    + Hiện tại có rồi (làm cho sản phẩm Lombard Loan)
    + Màn hình giải ngân
### 5. Repyament centralization
- **Hiện trạng**: Quy trình trả nợ khoản vay TD theo yêu cầu đang làm trên T24
    + Có 4 loại và logic tính ra số tiền khác nhau
        - Trả nợ trước hạn
        - Trả nợ đến hạn
        - Trả nợ quá hạn
        - Trả nợ để đóng hẳn khoản vay
- **Giải pháp**: Chuyển sang LMS tái sử dụng quy trình hiện tại đã có cho việc trả nợ khoản vay TD
    + Nơi monitor hết hạn là CCM
    + Trigger từ IDO, ROC, CCM
### 6. Optimize repayment process for Debt Collection
- **Hiện trạng**: Chức năng mặc định của T24 liên quan đến quy trình thu hồi nợ không đáp ứng tất cả các y/c kinh doanh. Nếu thực hiện trên T24 sẽ có GAP size XXL
    - T24 có chức năng ACLK số tiền tăng dư nợ lên hằng ngày và đang k có
    - Job phải thu phải trả
- **Giải pháp**:
    - Ngày hôm trước bị block từng này và ngày hôm sau từng này
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
    - TCB k trực tiếp cho vay mà đi donate cho vay
    - File nhận đc từ entrusted loan từ Home Credit
- **Giải pháp**: Thiết lập sản phẩm cho vay AA cho các khoản vay ủy thác
    - Parse file bên ngoài đẩy vào T24 one by one
