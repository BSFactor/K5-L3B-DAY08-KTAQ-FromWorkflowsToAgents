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

Tôi tích hợp bảng so sánh bốn shape, run ID lỗi và phân tích chi phí vào báo cáo nhóm. Workflow là ứng viên có chi phí thấp nhất trên bộ public: pass 70/70 như single và orchestrator, nhưng cost/success thấp hơn lần lượt khoảng 4,1% và 50,0%. Code và tài liệu đã tích hợp trên `main` tại `138163c`; benchmark đã commit tại `f265ae2`, báo cáo nhóm tại `6170f77`, báo cáo cá nhân tại `a62e7ff`. ADR chọn workflow và research card R2 đã hoàn tất; commit chốt để trống theo yêu cầu hiện tại.

Tôi chạy hidden self-check (600 trial), tổng hợp kết quả và hoàn tất thí nghiệm R2: sao swarm sang variant chỉ bỏ ticket-delay override, đo lại 140 trial, thêm test đối chiếu T02 trên năm seed. Reference pass 69/70, variant pass 70/70; ba model call/trial không đổi. Test và thí nghiệm mới chờ commit.

## 4. Bằng chứng

Các lệnh kiểm tra đã được chạy với hỗ trợ Codex trong quá trình rà soát và tích hợp:

| Kiểm tra | Kết quả đã ghi nhận |
| --- | --- |
| Host suite sau khi tích hợp workflow | 56 passed |
| Toàn bộ student suite sau khi tích hợp workflow | 283 passed |
| Single matrix và test CP4, khi rà soát PR single | 97 passed |
| Orchestrator matrix, khi rà soát PR orchestrator | 62 passed |
| Crash suite chạy lại khi rà soát single | 20/20 |
| Test ablation R2, mỗi seed một ca | 5 passed |
| Host suite sau khi thêm test R2 | 61 passed |
| Student suite kiểm lại sau khi thêm test R2 | 283 passed |

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

Bằng chứng bổ sung: [hidden self-check](../../src/reports/hidden_selfcheck.json) ghi 200 trial/shape, không runtime error/trace error/leak_suspect; bộ không nhãn nên không xác nhận pass rate hoặc approval bypass chính thức. [Ablation R2](../../src/reports/research_ablation.json) ghi pass^5 tăng 0,928571 → 1, variance về 0, cost/success 49,57 → 48,72 VND. Test `python -m pytest -q tests/test_research_ablation.py` đạt 5 passed; test và variant được chuẩn bị với hỗ trợ Codex.

Tài liệu liên quan: [TEAM.md](../../TEAM.md), [báo cáo nhóm](group_report.md), [ADR](decision_adr.md), [research card](research_card.md).

## 5. Công cụ AI đã dùng

Sử dụng OpenAI Codex để giải thích checkpoint, đọc code và hợp đồng, rà soát commit/PR, chạy test, hỗ trợ xử lý merge conflict, chạy benchmark bốn shape, hidden self-check, chuẩn bị ablation/test R2 và soạn tài liệu dựa trên bằng chứng. Codex hỗ trợ kiểm tra tính nhất quán của JSON, tổng hợp metric, cập nhật bảng đóng góp và báo cáo; không dùng model thật trong các shape.

Cách đối chiếu gồm đọc diff và tác giả commit, so sánh báo cáo trên main với nhánh team_report, kiểm tra kết quả test và số liệu JSON. Khi dùng `-k single`, tên test chung `single_run` khiến pytest chọn cả shape khác; bộ lọc được sửa để loại workflow, orchestrator và swarm trước khi ghi nhận kết quả 97 passed.

## 6. Tự đánh giá

Phần điều phối và tích hợp giúp giữ nội dung của từng thành viên, liên kết đóng góp với commit/test và phân biệt bằng chứng riêng của shape với kiểm tra host. Tôi hiểu rằng báo cáo phải mô tả đúng kiến trúc đang chạy: single có vòng ReAct nhưng lựa chọn tool cố định bằng Python; orchestrator dùng delegation theo trình tự cố định. Việc hoàn tất benchmark giúp tôi so sánh bốn shape trên cùng điều kiện, phân biệt kết quả public với hidden và giải thích vì sao thêm agent chưa mang lại lợi ích trong bộ public hiện tại.
