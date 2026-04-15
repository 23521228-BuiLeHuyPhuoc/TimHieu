# Đề xuất Topology SDN phát hiện & giảm thiểu DoS/DDoS (TV1)

## 1) Mục tiêu topology
- Mô phỏng mạng SDN đủ lớn để có **traffic bình thường** và **traffic tấn công**.
- Dễ thu thập đặc trưng tại switch/controller để phục vụ module phát hiện ngưỡng.
- Cho phép đẩy luật **Drop / Rate Limit** và quan sát hiệu quả trên dashboard.

## 2) Topology đề xuất (khuyến nghị đạt điểm cao)

### Mô hình 3 lớp trong Mininet
- **Core:** 1 switch lõi `s1`
- **Aggregation:** 2 switch `s2, s3`
- **Access:** 4 switch `s4, s5, s6, s7`
- **Hosts:**
  - Nhóm client hợp lệ: `h1..h8`
  - Nhóm server dịch vụ: `srv1 (web), srv2 (api)`
  - Nhóm attacker: `atk1, atk2, atk3`
- **Controller SDN:** Ryu/ONOS đặt ngoài Mininet, kết nối OpenFlow 1.3

### Kết nối logic
- `s1` nối với `s2, s3`
- `s2` nối `s4, s5`; `s3` nối `s6, s7`
- Mỗi access switch nối 2–4 host
- `srv1, srv2` đặt khác nhánh với attacker để thấy rõ hiệu ứng nghẽn/tấn công

## 3) Phân hoạch IP/VLAN gợi ý
- VLAN10: User hợp lệ `10.10.10.0/24`
- VLAN20: Server `10.10.20.0/24`
- VLAN30: Attacker lab `10.10.30.0/24`
- VLAN99: Quản trị/telemetry `10.10.99.0/24`

> Lợi ích: tách vai trò rõ ràng, dễ viết rule chặn theo subnet/VLAN, dễ trực quan trên Grafana.

## 4) Ánh xạ đúng bảng phân công

## Thành viên 1 – Network Architect (bạn)
- Cài Mininet + Controller, tạo topology script tham số hóa:
  - số lượng user/attacker
  - băng thông link core/edge
  - delay/loss để tạo nhiều kịch bản
- Bàn giao:
  - môi trường chạy 1 lệnh lên lab
  - file topology script + tài liệu cách chạy

## Thành viên 2 – Attacker
- Script sinh baseline traffic (iperf/ping/http)
- Script tấn công (SYN flood/UDP flood/ICMP flood, nhiều nguồn)
- Có profile nhẹ/vừa/mạnh để test ngưỡng phát hiện

## Thành viên 3 – SDN Engineer
- Module lấy thống kê flow/port định kỳ (packet rate, byte rate, new flow rate)
- Module mitigation đẩy luật:
  - Drop theo src/dst/protocol
  - Rate limit theo port/queue (meter/qos)

## Thành viên 4 – Detection
- Module phát hiện ngưỡng:
  - PPS/BPS vượt ngưỡng
  - tỷ lệ SYN bất thường
  - số flow mới tăng đột biến
- Trả nhãn cảnh báo + đề xuất hành động cho TV3

## Thành viên 5 – Monitor & PM
- InfluxDB lưu metrics/events
- Grafana dashboard:
  - Throughput, packet rate, dropped packets, active flows
  - timeline: thời điểm detect và thời điểm mitigation
- Tổng hợp báo cáo + slide demo kết quả trước/sau chặn

## 5) Kịch bản demo nên có để “điểm 10”
1. **Normal traffic:** hệ thống ổn định, không false alarm.
2. **DoS đơn nguồn:** phát hiện nhanh, chặn đúng nguồn.
3. **DDoS đa nguồn:** kích hoạt rate-limit + drop theo nhóm nguồn.
4. **Sau mitigation:** dịch vụ server phục hồi rõ rệt (latency/throughput cải thiện).

## 6) KPI chấm điểm khuyến nghị
- Thời gian phát hiện (TTD) thấp.
- Tỷ lệ phát hiện cao, false positive thấp.
- Thời gian phản ứng mitigation nhanh.
- Mức phục hồi dịch vụ sau chặn có số liệu trước/sau.

## 7) Gợi ý phạm vi bàn giao tối thiểu theo sprint
- **Sprint 1:** chạy topology + baseline traffic.
- **Sprint 2:** thu thập thống kê + dashboard cơ bản.
- **Sprint 3:** detection threshold + drop rule.
- **Sprint 4:** rate-limit nâng cao + đánh giá KPI + hoàn thiện báo cáo.

---

## Kết luận
Topology trên đủ độ phức tạp để chứng minh toàn bộ chu trình **Generate traffic → Detect → Mitigate → Observe**, bám sát phân công 5 thành viên và đặc biệt giúp TV1 thể hiện vai trò kiến trúc mạng rõ ràng.
