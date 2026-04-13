# Kế hoạch đầy đủ để tìm hiểu lý thuyết các dịch vụ kết nối mạng Azure

## 1) Mục tiêu học tập tổng thể

Sau khi hoàn thành kế hoạch này, bạn cần đạt được:

1. Hiểu đúng vai trò, cơ chế hoạt động, giới hạn và mô hình triển khai của:
   - Virtual Network (VNet) Peering
   - Private Link
   - VPN Gateway (Site-to-Site, Point-to-Site)
   - Azure ExpressRoute
   - Azure Virtual WAN
2. So sánh chính xác **VNet Peering** với **Azure Virtual WAN** theo:
   - Chức năng
   - Ưu / nhược điểm
   - Trường hợp sử dụng thực tế
3. Có năng lực chọn giải pháp kết nối phù hợp theo bối cảnh doanh nghiệp (quy mô nhỏ, vừa, lớn, đa vùng, đa chi nhánh, hybrid cloud).

---

## 2) Kiến thức nền tảng bắt buộc (học trước)

Trước khi vào từng dịch vụ, cần nắm vững:

- Cấu trúc mạng trong Azure:
  - Region, Availability Zone, Subscription, Resource Group
  - VNet, Subnet, IP Address Space
  - NIC, NSG, UDR (User Defined Route), Route Table
- Luồng mạng và điều hướng:
  - System routes vs custom routes
  - East-West traffic vs North-South traffic
- Mô hình kiến trúc:
  - Flat network
  - Hub-and-Spoke
  - Transit routing cơ bản
- Bảo mật mạng:
  - NSG vs Azure Firewall (khái niệm)
  - Zero Trust (nguyên tắc)
- Kết nối hybrid:
  - On-premises ↔ Azure
  - Internet public path vs private dedicated path

> Nếu chưa chắc phần nền, hãy dành 1–2 buổi đầu chỉ để chuẩn hóa kiến thức nền rồi mới đi tiếp.

---

## 3) Lộ trình học chi tiết theo giai đoạn

## Giai đoạn A — Học từng dịch vụ riêng lẻ

### A1. Virtual Network Peering

**Mục tiêu hiểu sâu:**
- Peering là gì, hoạt động ở lớp nào, luồng đi qua đâu
- Same-region peering và Global VNet Peering
- Cách route hoạt động khi peering
- Giới hạn và lưu ý thiết kế (khả năng mở rộng, quản trị nhiều kết nối)

**Nội dung cần nắm:**
- Khái niệm non-transitive trong kết nối VNet (ý nghĩa thực tế khi có nhiều VNet)
- Dùng peering trong kiến trúc hub-spoke
- Các chi phí liên quan traffic đi qua peering

**Đầu ra cần đạt:**
- Viết được 1 trang tóm tắt: “Khi nào dùng peering, khi nào không”.

---

### A2. Azure Private Link

**Mục tiêu hiểu sâu:**
- Private Endpoint là gì
- Cách truy cập dịch vụ PaaS qua private IP
- Sự khác nhau giữa Private Link và Service Endpoint (ở mức khái niệm sử dụng)

**Nội dung cần nắm:**
- Kịch bản “đóng public access”
- DNS trong Private Link (private DNS zone, name resolution)
- Rủi ro cấu hình sai DNS dẫn đến truy cập lỗi

**Đầu ra cần đạt:**
- Lập bảng “Private Link phù hợp với dịch vụ nào và vì sao”.

---

### A3. VPN Gateway (Site-to-Site, Point-to-Site)

**Mục tiêu hiểu sâu:**
- Site-to-Site VPN: kết nối mạng doanh nghiệp (on-prem) với Azure
- Point-to-Site VPN: kết nối người dùng cá nhân/thiết bị client vào Azure
- Khái niệm cơ bản về IPsec/IKE, BGP (mức kiến trúc)

**Nội dung cần nắm:**
- Khi nào dùng S2S, khi nào dùng P2S, khi nào kết hợp cả hai
- Ảnh hưởng của SKU/throughput/tunnel limit đến thiết kế
- Tính ổn định và các yếu tố ảnh hưởng độ trễ

**Đầu ra cần đạt:**
- Vẽ sơ đồ 2 mô hình:
  - Chi nhánh ↔ Azure (S2S)
  - Người dùng từ xa ↔ Azure (P2S)

---

### A4. Azure ExpressRoute

**Mục tiêu hiểu sâu:**
- Kết nối private dedicated giữa doanh nghiệp và Microsoft cloud
- Sự khác biệt cốt lõi giữa VPN qua Internet và ExpressRoute
- Giá trị của SLA/độ ổn định/dự đoán hiệu năng

**Nội dung cần nắm:**
- Mô hình nhà cung cấp kết nối (connectivity provider)
- Các phiên bản/tuỳ chọn cơ bản liên quan băng thông, mở rộng
- Kịch bản workload yêu cầu tính ổn định cao

**Đầu ra cần đạt:**
- Bảng so sánh VPN vs ExpressRoute theo độ tin cậy, chi phí, vận hành.

---

### A5. Azure Virtual WAN

**Mục tiêu hiểu sâu:**
- Virtual WAN là nền tảng managed transit network toàn cục
- Vai trò của Virtual Hub trong kết nối VNet, branch, VPN users, ExpressRoute
- Cách Virtual WAN giúp quản trị tập trung ở quy mô lớn

**Nội dung cần nắm:**
- Kiến trúc global/multi-region
- Tích hợp nhiều dạng kết nối trong một mô hình quản trị
- Ưu thế vận hành so với tự ghép nhiều peering thủ công

**Đầu ra cần đạt:**
- Viết 1 ghi chú kiến trúc: “Khi nào tổ chức nên chuyển từ peering thuần sang Virtual WAN”.

---

## Giai đoạn B — So sánh tổng hợp & ra quyết định

Tạo ma trận so sánh theo 9 tiêu chí:

1. Mục tiêu kết nối chính
2. Phạm vi triển khai (nội bộ Azure, hybrid, global)
3. Khả năng mở rộng
4. Độ phức tạp quản trị
5. Hiệu năng/độ trễ
6. Tính sẵn sàng và độ ổn định
7. Mức độ bảo mật và kiểm soát truy cập
8. Chi phí triển khai ban đầu
9. Chi phí vận hành dài hạn

Kết quả cuối giai đoạn:
- Có “decision matrix” dùng để chọn giải pháp phù hợp từng bài toán.

---

## Giai đoạn C — Củng cố bằng case study

Thực hiện tối thiểu 5 case:

1. Startup nhỏ chỉ có 2 VNet trong 1 region
2. Doanh nghiệp có 5–10 VNet, bắt đầu có nhu cầu tách môi trường
3. Công ty có nhiều chi nhánh cần kết nối về Azure
4. Doanh nghiệp hybrid cần kết nối ổn định cao đến data center
5. Tổ chức đa quốc gia cần quản trị mạng tập trung đa vùng

Mỗi case trả lời 4 câu:
- Chọn dịch vụ nào?
- Vì sao dịch vụ đó tối ưu?
- Rủi ro chính là gì?
- Nếu scale gấp 5 lần thì kiến trúc có còn phù hợp không?

---

## 4) Phân tích & so sánh: Virtual Network Peering vs Azure Virtual WAN

## 4.1 Chức năng

### Virtual Network Peering
- Kết nối trực tiếp 2 VNet (hoặc nhiều cặp VNet) qua backbone Azure.
- Dùng để các tài nguyên ở các VNet giao tiếp với nhau như trong mạng riêng.
- Thích hợp cho kết nối điểm-điểm hoặc số lượng kết nối vừa phải.

### Azure Virtual WAN
- Dịch vụ mạng managed cho kết nối tập trung quy mô lớn.
- Dùng Virtual Hub để liên kết:
  - Nhiều VNet
  - Nhiều chi nhánh (S2S VPN)
  - Người dùng từ xa (P2S VPN)
  - Kết nối ExpressRoute
- Phù hợp bài toán transit toàn cục và vận hành tập trung.

---

## 4.2 Ưu / nhược điểm

### Virtual Network Peering

**Ưu điểm:**
- Cấu hình tương đối đơn giản khi số VNet ít.
- Độ trễ thấp, đi trên backbone Azure.
- Hiệu quả cho bài toán nội bộ Azure quy mô nhỏ đến trung bình.

**Nhược điểm:**
- Khi số lượng VNet lớn, quản lý peering phức tạp (nhiều cặp kết nối).
- Khó mở rộng thành mô hình global transit nếu hệ thống tăng nhanh.
- Quản trị tập trung và policy đồng nhất ở quy mô lớn không thuận lợi bằng Virtual WAN.

### Azure Virtual WAN

**Ưu điểm:**
- Quản trị tập trung, phù hợp hệ thống lớn/đa vùng/đa chi nhánh.
- Tích hợp nhiều phương thức kết nối trong cùng kiến trúc.
- Mở rộng tốt theo nhu cầu toàn cầu và vận hành doanh nghiệp.

**Nhược điểm:**
- Chi phí và độ phức tạp thiết kế ban đầu cao hơn peering đơn giản.
- Có thể overkill cho hệ thống nhỏ, ít VNet, ít biến động.
- Đòi hỏi tư duy kiến trúc tổng thể (network governance) rõ ràng.

---

## 4.3 Trường hợp nên dùng mỗi loại

### Nên dùng Virtual Network Peering khi:
- Hệ thống có ít VNet, phạm vi không quá rộng.
- Chủ yếu cần kết nối nội bộ Azure giữa các môi trường/workload.
- Ưu tiên triển khai nhanh, đơn giản, tối ưu chi phí giai đoạn đầu.

### Nên dùng Azure Virtual WAN khi:
- Có nhiều VNet, nhiều region, nhiều chi nhánh hoặc người dùng từ xa.
- Cần quản trị tập trung và tiêu chuẩn hóa kết nối toàn doanh nghiệp.
- Có nhu cầu kết hợp đa dạng kết nối (S2S, P2S, ExpressRoute, VNet) trong cùng kiến trúc.
- Dự báo tăng trưởng mạnh, cần khả năng scale và vận hành lâu dài.

---

## 4.4 Kết luận ngắn gọn để ra quyết định

- **Nếu bài toán nhỏ/đơn giản:** ưu tiên **VNet Peering**.
- **Nếu bài toán doanh nghiệp lớn/cần quản trị tập trung quy mô cao:** ưu tiên **Azure Virtual WAN**.
- **Nếu hiện tại nhỏ nhưng có lộ trình mở rộng nhanh:** có thể bắt đầu bằng peering, nhưng cần thiết kế lộ trình chuyển dần sang mô hình transit tập trung khi đạt ngưỡng phức tạp.

---

## 5) Kế hoạch học theo checklist (thực thi)

- [ ] Hoàn thành ôn nền tảng mạng Azure (VNet, subnet, route, NSG, hub-spoke)
- [ ] Tóm tắt lý thuyết VNet Peering (khái niệm, giới hạn, use case)
- [ ] Tóm tắt lý thuyết Private Link (private endpoint, DNS, use case)
- [ ] Tóm tắt lý thuyết VPN Gateway (S2S/P2S, điểm khác nhau, use case)
- [ ] Tóm tắt lý thuyết ExpressRoute (mục tiêu, lợi ích, điều kiện áp dụng)
- [ ] Tóm tắt lý thuyết Virtual WAN (kiến trúc, thành phần, use case)
- [ ] Hoàn thiện ma trận so sánh tất cả dịch vụ theo 9 tiêu chí
- [ ] Hoàn thành chuyên đề so sánh sâu VNet Peering vs Virtual WAN
- [ ] Thực hiện 5 case study và tự giải thích lựa chọn kiến trúc
- [ ] Viết bản kết luận cuối cùng: “Khung chọn giải pháp kết nối mạng Azure”

---

## 6) Bộ câu hỏi tự đánh giá cuối kỳ

Tự trả lời đầy đủ các câu sau (không xem tài liệu):

1. Vì sao peering không phải luôn là đáp án tốt nhất khi số lượng VNet tăng mạnh?
2. Private Link giải quyết vấn đề gì mà peering/VPN không giải quyết trực tiếp?
3. Khi nào S2S VPN đủ dùng, khi nào cần nâng lên ExpressRoute?
4. Vì sao Virtual WAN phù hợp hơn cho mô hình đa chi nhánh toàn quốc/toàn cầu?
5. Nếu công ty tăng từ 3 lên 30 VNet trong 1 năm, kiến trúc nào giảm rủi ro vận hành nhất?
6. Với yêu cầu bảo mật cao (hạn chế public exposure), dịch vụ nào cần ưu tiên?
7. Nếu ngân sách giới hạn nhưng cần mở rộng trong tương lai, chiến lược chuyển đổi kiến trúc nên như thế nào?

> Nếu bạn trả lời mạch lạc, có lập luận trade-off và nêu rõ điều kiện áp dụng, nghĩa là bạn đã nắm lý thuyết ở mức tốt.

