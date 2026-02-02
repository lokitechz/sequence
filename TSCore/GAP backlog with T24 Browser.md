## Các điểm LMS cần sửa
### LMS - Non STP
- Update luồng mortgage cho phép add nhiều tài sản và query danh sách tài sản từ CCM
- Tích hợp với CCM để check thông tin tài sản đảm bảo và lưu mối quan hệ giữa TSĐB với nghĩa vụ trả nợ

### LMS - STP
- Update luồng giải ngân
    - Thêm step đẩy dữ liệu mối quan hệ lên CCM
- Update cho 2 luồng repayment process
    - Thêm step revoke TSĐB trên CCM
    - Tái sử dụng API của ROC để trigger run process trên CCM
    - Tái sử dụng API

- New workflow for Repayment over due date
- Integration with AE+ Pricing for get final interest
- 