## Limit (A Dũng)
### Mục tiêu
- **Technical Upgrade** R18 lên R25 chức năng của Limit Module giữ nguyên
- **Functional Upgrade**
    + Đánh giá khi lên AA sẽ chỉ ảnh hưởng đến trong T24
    + Hiện tại đang dùng chung segment không y/c tạo bảng mã limit mới
    + FIN muốn theo dõi available limit -> Nếu bật tính năng này tạo ra khoảng 3tr tk kế toán nội bộ

### Nghiệp vụ và ý nghĩa
- QUẢN LÝ GIỚI HẠN KH ĐƯỢC CHI TIÊU
- Limit offer là số mà bank offer cho KH không quản lý trên module Limit
- Khi KH thực hiện kí hợp đồng để nhận hạn mức thì sẽ ghi nhận số này trên module Limit trên Core
    - VD: Offer 1 tỷ nhưng KH chỉ nhận HM là 350tr thì sẽ lưu 350tr này trên core
- Khi giải ngân gọi trực tiếp vào T24
#### Vòng đời
1. Limit offer: Lời mời mà bank offer cho KH có thể sử dụng cho nhiều mục đích
- SC tạo request offer
- Offer sẽ được ghi vào trong Limit Proxy

2. Contracted Limit: Là số limit KH kí kết với bank
- Đối với KH cá nhân thì dùng đến đâu kí đến đấy
- Khi KH đã kí thì sẽ được hoạch toán vào module Limit trong core còn với thẻ sẽ được hoạch toán sang hệ thống thể
- ROC và LMS sẽ đẩy request vào module Limit trong core

3. Sử dụng vốn
- Khi sử dụng vốn sẽ sinh ra số dư

4. Quản lý tình trạng tài chính của KH

### Cần làm
- Technical upgrade sẽ đảm bảo test đúng các cấu phần của Limit trên T24
- Review FSD
- Danh sách API trong FSD
    1. MB.PROCESS.MAX.AC.TCB