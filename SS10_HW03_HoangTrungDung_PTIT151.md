# BÀI 3: ĐỌC HIỂU CODE & CẬP NHẬT ĐẶC TẢ HỆ THỐNG

## 1. Prompt thiết kế cho Antigravity

```text
Role:
Bạn là Senior System Analyst và Software Architect có kinh nghiệm phân tích mã nguồn Java Spring Boot và viết tài liệu SRS.

Objective:
Phân tích module CartService đã được index trong hệ thống và thực hiện đồng thời:
1. Phát hiện lỗ hổng logic hoặc thiếu sót nghiệp vụ.
2. Sinh danh sách Business Rules theo chuẩn SRS để nhóm phát triển thực hiện bản vá.

Context:
Hệ thống thương mại điện tử Guai-api có chức năng thêm sản phẩm vào giỏ hàng thông qua phương thức addToCart(userId, productId, quantity).

Đoạn code đã được index trong workspace.

Constraints:
- Phân tích theo góc nhìn nghiệp vụ thay vì sửa code.
- Xác định các trường hợp dữ liệu bất thường.
- Kiểm tra tính hợp lệ của quantity.
- Kiểm tra giới hạn tồn kho Inventory.
- Kiểm tra trường hợp số lượng âm hoặc bằng 0.
- Kiểm tra trường hợp vượt quá số lượng sản phẩm còn lại.
- Mô tả hậu quả nếu không xử lý.
- Đề xuất Business Rules theo chuẩn SRS.
- Không viết code Java.

Output Format:
1. Logic Issues Found
2. Risk Analysis
3. Business Rules
4. Expected System Behavior
```

---

# 2. Business Rules do AI sinh ra

```text
BUSINESS RULES – CART MODULE

BR-01: Sản phẩm phải tồn tại
Hệ thống chỉ cho phép thêm sản phẩm đã tồn tại trong cơ sở dữ liệu.

BR-02: Số lượng thêm vào phải lớn hơn 0
Giá trị quantity phải lớn hơn 0.
Nếu quantity <= 0, hệ thống từ chối yêu cầu.

BR-03: Không cho phép số lượng âm
Người dùng không được gửi quantity âm.
Hệ thống trả về lỗi dữ liệu không hợp lệ.

BR-04: Số lượng thêm vào không được vượt quá tồn kho
Quantity yêu cầu phải nhỏ hơn hoặc bằng số lượng tồn kho hiện tại.

BR-05: Tổng số lượng trong giỏ không được vượt tồn kho
Nếu sản phẩm đã tồn tại trong giỏ:
CurrentQuantity + NewQuantity ≤ Inventory.

BR-06: Hệ thống phải kiểm tra tồn kho trước khi lưu
Việc kiểm tra Inventory phải được thực hiện trước thao tác save.

BR-07: Không cho phép sản phẩm hết hàng được thêm vào giỏ
Nếu Inventory = 0, hệ thống từ chối yêu cầu.

BR-08: Hệ thống phải trả về thông báo lỗi rõ ràng
- INVALID_QUANTITY
- OUT_OF_STOCK
- INSUFFICIENT_INVENTORY

BR-09: Hệ thống phải ghi nhận các yêu cầu bất thường
Các trường hợp quantity âm hoặc vượt tồn kho phải được ghi log.

BR-10: Dữ liệu giỏ hàng phải đảm bảo tính nhất quán
Sau khi cập nhật giỏ hàng, tổng số lượng sản phẩm trong giỏ không được lớn hơn số lượng tồn kho thực tế.
```

---

# 3. Lỗ hổng logic được phát hiện

### Lỗi 1: Không kiểm tra quantity <= 0

Ví dụ:

```java
addToCart(1, 10, -5);
```

Kết quả:

* Giỏ hàng có thể có số lượng âm.
* Dữ liệu không hợp lệ.

---

### Lỗi 2: Không kiểm tra tồn kho

Ví dụ:

* Tồn kho: 5 sản phẩm.
* Người dùng thêm 20 sản phẩm.

Hệ thống vẫn lưu dữ liệu.

---

### Lỗi 3: Không kiểm tra tổng số lượng trong giỏ

Ví dụ:

* Đã có 4 sản phẩm trong giỏ.
* Kho còn 5 sản phẩm.
* Người dùng thêm 3 sản phẩm.

Kết quả:

* Giỏ hàng chứa 7 sản phẩm.
* Vượt quá tồn kho.

---

# Kết luận

Module Cart hiện tại thiếu các quy tắc nghiệp vụ quan trọng về:

* Kiểm tra dữ liệu đầu vào.
* Kiểm tra tồn kho.
* Kiểm tra giới hạn số lượng.
* Đảm bảo tính nhất quán dữ liệu.

Các Business Rules trên sẽ là căn cứ để nhóm phát triển triển khai bản vá và cập nhật tài liệu SRS.
