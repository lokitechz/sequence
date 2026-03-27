## Bussiness flow
1. RM lên SC đẩy thông tin cấp duyệt hạn mức
2. RM lên LMS đề nghị giải ngân
3. LMS thực hiện process để giải ngân xuống T24 qua API
4. Sau khi giải ngân xuống T24 xong data sẽ đi Data Lake hoặc OGG -> Push data lên Kafka (topic chưa biết)
5. LP consume data sẽ thực hiện tính toán ra hạn mức còn lại của khách hàng