# Clear backlog
## 1. LMS - Non STP 
- Change UI thêm phần add tài sản không chỉ add được mỗi tài sản được phê duyệt bởi SC mà được add tài sản mới
    - VD: SC phê duyệt dùng STK để làm biện pháp giảm thiểu rủi ro mà SC còn chưa có số sổ. Câu chuyện mở rộng Bussiness thiêu chỉ tiêu cần mở TD và xin bên rủi ro sử dụng TD đó 
- Tích hợp với CCM để check xem có được quyền nhận cái sổ đấy không
> 1. **RB12 phải done và mình sẽ update trên đó chứ không được quyền chỉnh sửa trước vì ảnh hưởng đến luồng tài sản thông thường đi vào thế chấp**
> 
> 2. **Đối với các phần không phải collateral thì sẽ nằm trong TSCore nhưng phần liên quan đến hoạch toán tài sản sẽ nằm trong RB12**
>
> 3. **Phần này sẽ chỉ cover những phần không được nhận làm tài sản đảm bảo nhưng vẫn store lại relationship và liability**

## 2. LMS - STP
- Update workflow to disbursment

## Pricing
- Các service ảnh hưởng tích hợp để lấy phần giá
    1. Unsecured service
    2. Secured service
    3. Disburstment
- Các phần việc của pricing
    1. Cổng thông tin để tạo chương trình khuyến mại bán hàng và các logic của chương trình khuyến mại bán hàng