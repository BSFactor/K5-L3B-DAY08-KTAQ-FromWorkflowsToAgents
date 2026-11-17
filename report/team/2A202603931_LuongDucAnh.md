# Báo cáo cá nhân — Luong Duc Anh

## 1. Thông tin thành viên

- Họ tên: Luong Duc Anh
- MSSV: 2A202603931
- Email: 26ai.anhld5@vinuni.edu.vn
- GitHub: ldlda

## 2. Phần việc phụ trách

Shape C (`orchestrator`) và ranh giới handoff giữa parent orchestrator với investigator chỉ đọc.

## 3. Đóng góp cụ thể

- Triển khai `run_claim` trong `src/lab8/shapes/orchestrator.py`: tạo handoff đã sanitize, giao các tool đọc được phép cho investigator, dùng quote từ order/policy và để host xử lý approval, trần hoàn tiền, idempotency và ghi ledger.
- Làm rõ luồng parent → investigator → parent trong code; giữ trace không chứa ticket, raw headers hoặc `parent_secret`; chuyển tiếp crash injection và cho phép resume.
- Thêm test `test_orchestrator_handoff.py::test_orchestrator_trace_contains_only_sanitized_handoff` cho allowlist handoff và trace.
- Commit kỹ thuật: `a2b887d`. Test và bằng chứng crash: `6b135cb`.

## 4. Bằng chứng

- Orchestrator public student matrix: 62 passed, gồm các trường hợp retry/crash T11–T12.
- Host suite: 54 passed; smoke: `lab8.smoke pass`.
- Crash suite: 20/20 trong `src/reports/crash.json`.
- Handoff/trace test: 1 passed. Run id mẫu: `R-orchestrator-C-001-s11`.

Các kết quả này xác nhận public checks của shape C. Benchmark bốn shape và hidden grader chưa chạy trong phần việc này.

## 5. Công cụ AI đã dùng

Đã dùng OpenAI Codex để đọc hợp đồng và mã mẫu, triển khai/refactor shape C, viết test handoff/trace, và giải thích luồng chạy. Đã kiểm tra kết quả bằng smoke, host suite, student matrix của orchestrator, test mới và crash suite; không dùng model/API thật trong shape.

## 6. Tự đánh giá

Tôi có thể giải thích luồng nhiều bước và vì sao investigator chỉ nhận handoff allowlist, vì sao trace không ghi dữ liệu thô, và vì sao shape phải gửi quote đầy đủ cho host. Bản hiện tại dùng delegation theo trình tự cố định; MockLLM không lập kế hoạch độc lập. Tôi chưa có số liệu benchmark bốn shape để kết luận orchestrator là kiến trúc nên chọn cho toàn nhóm.
