# Báo cáo cá nhân — 2A202603871_NguyenNamKhanh

## 1. Thông tin thành viên

- Họ tên: Nguyễn Nam Khánh
- MSSV: 2A202603871
- Email: 26ai.khanhnn3@vinuni.edu.vn
- GitHub: BSFactor

## 2. Phần việc phụ trách

Phụ trách điều phối công việc, rà soát và tích hợp commit/PR của các thành viên, tổng hợp bằng chứng và tài liệu nhóm. Tôi hoàn tất benchmark public đủ bốn shape của CP5 và tổng hợp kết quả vào báo cáo chung trên nhánh `team_report`.

## 3. Đóng góp cụ thể

| Commit | Nội dung |
| --- | --- |
| `4dfb144` | Khởi tạo tài liệu nhóm. |
| `6cf86b2`, `5891738`, `98c97b9` | Ghi nhận đóng góp của theo commit và test. |
| `8bf483b`, `df06fb9` | Cập nhật tài liệu và thông tin đóng góp của thành viên. |
| `cb48259` | Tích hợp workflow, giữ TEAM.md của main để giải quyết xung đột bảng đóng góp. |
| `8fc7cf1` | Tích hợp bản sửa thông tin liên hệ của thành viên, tiếp tục giữ TEAM.md tập trung trên nhánh báo cáo. |

Rà soát code, test và báo cáo của thành viên trước khi tổng hợp đóng góp. Khi tích hợp workflow, tôi giữ các thay đổi triển khai, test, báo cáo cá nhân và mô tả workflow của Quân; chuyển phần ghi nhận đóng góp sang `team_report` để tránh các thành viên cùng sửa một bảng trên main.

Bản thảo báo cáo nhóm bổ sung luồng của ba shape, ranh giới code/model, handoff, retry/crash và các giới hạn. Nội dung workflow và lỗi pytest trên Windows do Quân viết được giữ lại.

Tôi hoàn tất benchmark public cho `workflow`, `single`, `orchestrator`, `swarm` trên cùng 14 claim và năm seed `11,22,33,44,55` (280 trial). Benchmark chạy với hỗ trợ Codex trên bản trích xuất sạch của code `main`, commit `7f07b295b8a6dacc37f666aac2f1933a8dca6e57`, và sinh `src/reports/benchmark.json` trên nhánh `team_report`. Tôi đối chiếu số trial, số lần pass và cost/success với dữ liệu từng trial; xác nhận phần single–swarm khớp hoàn toàn với report trước của Tuấn.

Tôi tích hợp bảng so sánh bốn shape, run ID lỗi và phân tích chi phí vào báo cáo nhóm. Workflow là ứng viên có chi phí thấp nhất trên bộ public: pass 70/70 như single và orchestrator, nhưng cost/success thấp hơn lần lượt khoảng 4,1% và 50,0%. ADR, research card, tích hợp nhánh nộp và commit chốt vẫn chưa hoàn tất. Phần benchmark mới và cập nhật tài liệu này chưa có commit riêng tại thời điểm ghi báo cáo.

## 4. Bằng chứng

Các lệnh kiểm tra đã được chạy với hỗ trợ Codex trong quá trình rà soát và tích hợp:

| Kiểm tra | Kết quả đã ghi nhận |
| --- | --- |
| Host suite sau khi tích hợp workflow | 56 passed |
| Toàn bộ student suite sau khi tích hợp workflow | 283 passed |
| Single matrix và test CP4, khi rà soát PR single | 97 passed |
| Orchestrator matrix, khi rà soát PR orchestrator | 62 passed |
| Crash suite chạy lại khi rà soát single | 20/20 |

Crash suite chủ yếu kiểm host và một ca swarm; T12 kiểm crash/resume của từng shape sinh viên. Các số test trên thuộc những lần tích hợp cụ thể.

Bằng chứng CP5: [benchmark.json](../../src/reports/benchmark.json), với lệnh tái lập trong `src/` của commit code nêu trên:

```bash
python -m lab8.benchmark --shapes workflow,single,orchestrator,swarm --cases data/public_claims.jsonl --seeds 11,22,33,44,55 --out reports/benchmark.json
```

| Shape | Trial pass | Pass^5 | Cost/success (VND mô phỏng) |
| --- | ---: | ---: | ---: |
| Workflow | 70/70 | 1,000000 | 27,86 |
| Single | 70/70 | 1,000000 | 29,04 |
| Orchestrator | 70/70 | 1,000000 | 55,71 |
| Swarm | 69/70 | 0,928571 | 49,57 |

Trial fail duy nhất: `R-swarm-C-002-s55`. Ba shape nhóm viết đều pass C-002 tại seed 55. Các chỉ số variance, seed min–max, mean/p95 steps và so sánh được tổng hợp ở mục 4 báo cáo nhóm. Kết quả là benchmark public, chưa xác nhận ngưỡng hidden; latency là `unavailable`. Lần chạy dùng Python 3.14.4 trên Linux, cần kiểm lại trên Python 3.11–3.13 theo README.

Tài liệu liên quan: [TEAM.md](../../TEAM.md), [báo cáo nhóm](group_report.md).

## 5. Công cụ AI đã dùng

Sử dụng OpenAI Codex để giải thích checkpoint, đọc code và hợp đồng, rà soát commit/PR, chạy test, hỗ trợ xử lý merge conflict, chạy benchmark bốn shape và soạn tài liệu dựa trên bằng chứng. Codex hỗ trợ kiểm tra tính nhất quán của JSON, tổng hợp metric, cập nhật bảng đóng góp và báo cáo; không dùng model thật trong các shape.

Cách đối chiếu gồm đọc diff và tác giả commit, so sánh báo cáo trên main với nhánh team_report, kiểm tra kết quả test và số liệu JSON. Khi dùng `-k single`, tên test chung `single_run` khiến pytest chọn cả shape khác; bộ lọc được sửa để loại workflow, orchestrator và swarm trước khi ghi nhận kết quả 97 passed.

## 6. Tự đánh giá

Phần điều phối và tích hợp giúp giữ nội dung của từng thành viên, liên kết đóng góp với commit/test và phân biệt bằng chứng riêng của shape với kiểm tra host. Tôi hiểu rằng báo cáo phải mô tả đúng kiến trúc đang chạy: single có vòng ReAct nhưng lựa chọn tool cố định bằng Python; orchestrator dùng delegation theo trình tự cố định. Việc hoàn tất benchmark giúp tôi so sánh bốn shape trên cùng điều kiện, phân biệt kết quả public với hidden và giải thích vì sao thêm agent chưa mang lại lợi ích trong bộ public hiện tại.
