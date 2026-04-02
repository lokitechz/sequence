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

## 9-2-2026
- Mục tiêu của Limit
    - Direct Limit: Đã generate theo
    - InDirect Limit: Limit
- Quản lý chu trình của Limit
- Cấp chung 1 tầng product
    - Không phải chia nhỏ
    - Nếu muốn chia nhỏ riêng thì tạo riêng 1 Limit riêng
## 9-2-2026
- Mục tiêu của Limit
    - Direct Limit: Đã generate theo
    - InDirect Limit: Limit
- Quản lý chu trình của Limit
- Cấp chung 1 tầng product
    - Không phải chia nhỏ
    - Nếu muốn chia nhỏ riêng thì tạo riêng 1 Limit riêng

## 10-02-2026
- Có 2 điểm trạm
    - 1. Khi KH kí kết hợp đồng tín dụng với TCB sẽ thực hiện lấy giá và kí kết trên hợp đồng đó
        - IDO, ROC, LMS sẽ gọi đến RBG Pricing
        - Gồm 3 cấu phần
            + Lãi suất cơ sở
            + Biên độ của khách hàng: do RM cân đối -> là 1 số cụ thể 3%
            + Sale Campain: Personalize -> là 1 số cụ thể 3%
        - Sẽ sync up key và con số để KH view được con số từ T24
        - Hiện tại CCM đã có expose 1 API
        - Info hợp đồng đó lãi suất bao nhiêu: key ls là bn, -> Store tại AA
    - 2. Period review
        - Trigger đẩy đến Lake để cung cấp ngược về cho Pricing -> Pricing review và tính toán đẩy ngược về T24
- AE+ Pricing
    1. Phát sinh 1 portal mới để tạo sale campain mới
        - Đặt sale campain code, thời gian + add điều kiện và logic của sale campain
        - 2 luồng duyệt khác nhau
    2. Chia làm 2 phần
        - Pricing Engine: là excute rule
            - Excute Rule
            - Enrich chỉ dành cho AEP
        - Portal: BPA
            - Campaingn Code thuộc Bussiness Domain nào
    3. Cách sử dụng AE+ Pricing
        - Tạo Business Domain
        - Indicator là các data feature của khách hàng
        - Tạo Target Audience gom những indicator vào với nhau
        - KQ tạo rule lưu bảng pp_dmn_rule
        - Pricing Engine sẽ thụ hưởng

    4. Rule được lấy từ các hệ thống khác nhau
        - 30 product toàn bộ lending ko bao gồm thẻ
        - Rule bao gồm mở mới và review
            + Gồm giá cơ sở, giá fix
            + Key LS cơ sở đồng bộ từ T24
        - Setup rule có bao gồm rủi ro tín dụng LTV 
        - Ondemand lấy ở DAH
        - Review lấy dữ liệu từ lake đẩy param
## 12-02-2026
- Trao đổi với LMS
    - 1. Manual từ đầu không sinh bút toán gì hết
    - 2. Khi hoạch toán lỗi thì sẽ lên T24 sẽ phải sang T24 hoạch toán tay
    - Q: LMS có phải xây dựng các màn hình tương ứng hollowout từ T24 ra không
    - IDO serve đc các TH adhoc
    - Lock TD không nằm trên LMS và nếu hệ thống chết không cần màn hình trên LMS
    - Onboard trên CCM
## NOte
- Làm song song technical upgrade và functional upgrade do muốn đóng hợp đồn tháng 7

## LMS giỏ
- Đã thỏa mãn
- Hệ thống 
- IDO đẩy sang mặc định là auto
- Disbursment - Thông tin đẩy từ SC sang -> Hiện tại đang may đo theo từng sản phẩm

- R18 T24 giải ngân 
- LMS đang vận hành như nào trên -> Ping dungnn3
- Trước khi giải ngân check

1. XĐ thằng TK đa
2. Khi xđ thỏa mãn điều kiện r thì thông tin CCM sẽ quản lý , đến ngày đến 
-> API lấy ra thông tin 
3. Phong tỏa giải tỏa
4. Mortgage on TD
- Lấy thông tin cho

### Mortgage on TD
#### Luồng khởi tạo hồ sơ vay và giải ngân
1. SC input thông tin sau đó sẽ đẩy hồ sơ sang LMS
2. Khi có hồ sơ trên LMS sẽ soạn hồ sơ bằng file excel
3. KH kí giấy tờ thế chấp
4. Nhập kho tài sản làm trên T24
5. Có 2 cách để giải ngân
- Hạch toán trực tiếp trên T24 (Không dùng mấy trử trường hợp bất khả kháng)
- Hoạch toán đổ lô bằng file excel (Cách hay được dùng)
    - Sau khi hoạc toán thành công trên T24 -> T24 trả ra bút toán -> Người dùng sẽ nhập bút toán này vào hồ sơ trên LMS

#### Luồng khởi tạo hồ sơ sau vay (thu nợ) và hoạch toán thu nơ
##### Thu nợ đến hạn
1. T24 tự chạy job khi đến kì thu nợ (làm rõ logic chạy)

##### Thu nợ 1 phần
1. LMS tạo hồ sơ thu nợ (Mang ý nghĩa lưu vết và không đẩy thông tin đi đâu)
2. T24 thu nợ bằng tay
3. Sau khi thu nợ trên T24 thành công sẽ quay lại LMS đánh dấu case là "Hồ sơ hoàn thành"
--> End luồng

### 27-02-2026
1. TCB làm gì toàn bộ phần liên quan đến vệ tinh và delta gap
2. AE+ Pricing
    1. Bussiness nhìn lại xem cái portal đã đủ đáp ứng nhu cầu tạo sale campain chưa?
    - Lúc nào lake có data feature mới thì portal có cần dev lại không?
    2. Setup 1 cái rule
    

- Liên hệ với các BA của 

- Mortgage with TD đem đi thế chấp và nhập kho

- Mortgage on TD dùng sổ để làm biện pháp giảm thiểu rủi ro chứ không nhập kho tài sản
    + **Có step nhập kho TSĐB cho TD -> Khai báo trên T24 như bình thường

- Sử dụng nguồn tiền của TD làm biện pháp thanh toán trong hạn, trước hạn hoặc tại 1 thời điểm nào đó

### 11-03-2026
#### Backlog LMS
- DMS có năng lực xử lý tất cả các các khoản nợ trên hệ thống chứ ko phải mỗi thẻ
- DMS sẽ trả ra danh sách khách hàng và min hay max
- LMS sẽ tính toán đc số tiền cần phong tỏa là bn dựa trên thông tin khách hàng gửi sang
- Lake hoặc T24
- Mong muốn realtime
- Hiện tại: near realtime chậm tầm 30 phút
- Chạy job vào 9h sáng cần biết nợ là bn
- Cơ chế phong tỏa đang làm thủ công
- Hiện tại trên T24 thu nợ số tối thiểu
- Job thu nợ quá hạn
+ Thu cả phần chưa quá hạn
+ Dưới 60 ngày chỉ thu nợ quá hạn
- DMS đẩy thông tin sang LMS lấy thông tin từ T24 vs Card xong tổng hợp dữ liệu
- DMS có ngày trả ra data sớm có ngày muộn
- DMS có realtime đc không hay LMS sẽ phải tự đi lấy

1. Màn hình thu nợ
- Màn hình FT

Clear lại các hạng mục bên dưới

2. Validate info
    + Check xem có nợ quá hạn trên T24
    + Check phân luồng nợ gọi đến DMS

#### Backlog ROC
- LMS tự động fill tài khoản giải ngân nếu ko truyền thông tin người thụ hưởng
    + LMS chỉ đẩy thông tin -> ROC không cần sửa gì
    + Luồng của PIL đang qua LMS
- MyCash on TD và Lombard TD
    + Giải ngân tại quầy move sang IDO
    + Add thêm step kết nối sang CCM

1. Hollowout ra cho đẹp hơn
2. Nếu ko move thì bị charge nhiều tiền do đi kèm AA
- Dòng 67, 68 đã estimate đúng chưa
- Đã làm trên IDO cho Mycash on TD
- Vendor khi sửa API có sửa endpoint
- BIL mới có của unsecured chưa có secured
3. Mule sửa vì T24 đổi endpoint còn hệ thống vệ tinh gọi Mule thì enpoint Mule giữ nguyên

### 13-03-2026
#### As is
- Hiện tại có 1 file MD_Interest theo template
    + Chưa phải file chuẩn nếu RBG đưa ra lãi suất mới
    + Có chức năng upload file lãi xuất
    + Ko cover được trong file thì sẽ phải tính bằng cơm và điền lại lên form của LMS
    + Chỉ dành cho khoản vay mới
    + Sửa giá trị trên file excel xong lại upload lại lên LMS
- Điều chỉnh lãi xuất cho khoản vay hiện hữu
    + Luồng ngoại lệ đang manual bên ngoài
    + Hiện tại ngoại lệ đang tự nhập
- Hiện tại không gọi qua bên nào
- 1 sản phẩm có thể đi qua nhiều file excel khác nhau
- Mong đợi
    + Lãi suất
    + Phí áp dụng cho từng sản phẩm
    + Lịch trả nợ ko áp dụng cho AE+ Pricing
- Đọc trc BRD Pricing
- Tiêu chí nào, tính toán ra sao, expect mong muốn

### Meeting
- Review
    + Có 2 loại API
    + Technical API
        - Review request response

### Meeting 31-03-2026
- Việc sắp tới review FSD và IDD
    + Check list 3 round review
