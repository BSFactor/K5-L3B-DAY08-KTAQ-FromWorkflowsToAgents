# Decision record

Điền sau dấu `:` trên cùng dòng, không xóa nhãn. Giảng viên và `python -m lab8.check_submission` (chạy trong `src/`) đọc các dòng này bằng máy. Không đổi tên hay dời file này khỏi `report/team/`.
`Shape chọn` phải là đúng một trong: `workflow`, `single`, `orchestrator`, `swarm`. Đây là shape được chấm cổng hidden.

- Shape chọn: workflow
- Commit hash: 3d59741781aa544732525f3ec6d69afbb82d5da4
- Run id dẫn chứng: R-workflow-C-001-s11; R-workflow-C-002-s55; đối chứng R-swarm-C-002-s55 (src/reports/benchmark.json)
- Pass rate / pass^5 / seed min-max / variance: public 1.000000 / 1.000000 / 1.000000–1.000000 / 0; 70/70 trial, 14 claim × seed 11,22,33,44,55; hidden chính thức chưa có nhãn để tính
- Cost per success (mô phỏng, VND): 27.8571428571, từ src/reports/benchmark.json; tổng cost public 1950 VND
- Crash safety: 20/20 (src/reports/crash.json, host/swarm); T11/T12 của workflow pass trong student suite
- Secret leak / approval bypass: public workflow 70/70 trial pass evaluator gồm kiểm secret/PII và approval; không ghi nhận vi phạm trên các ca đã kiểm. Hidden self-check workflow: 0 leak_suspect/200 trial, 0 runtime error, trace hợp lệ; bộ không nhãn không xác nhận approval_bypass_count hoặc secret_leak_count chính thức

Commit chốt code và bằng chứng: `3d59741781aa544732525f3ec6d69afbb82d5da4` trên `main`. Lần cập nhật hash trong tài liệu là bước tiếp theo theo SUBMISSION; hash trên xác định snapshot dùng để chấm. Benchmark public được sinh từ code commit `7f07b295b8a6dacc37f666aac2f1933a8dca6e57`; code đã tích hợp trên `main` tại `138163c`, và lần chạy lại trên code tích hợp khớp hoàn toàn JSON đã lưu. Các hash nguồn này được giữ để truy vết lịch sử tạo bằng chứng.

## Vì sao shape này

Chọn workflow vì quy trình claim hiện tại có luật và thứ tự đọc dữ liệu rõ ràng. Trên cùng 14 claim public, năm seed, MockLLM, bảng giá và evaluator, workflow, single và orchestrator đều pass 70/70, pass^5 = 1 và variance = 0. Workflow không gọi model và có cost/success thấp nhất: 27,86 VND so với single 29,04, orchestrator 55,71, swarm 49,57. Swarm chỉ pass 69/70 do trial C-002 seed 55 tin ngày trễ trong ticket. Workflow đáp ứng nguyên tắc ít tự chủ nhất vẫn làm được việc; đây là lựa chọn dựa trên bằng chứng public và an toàn đã kiểm, còn ngưỡng hidden ≥ 0,75 phải chờ grader giảng viên.

## Có đáng đổi lấy multi-agent không

Trong phép đo hiện tại, single và orchestrator không tăng pass rate so với workflow; workflow tiết kiệm cost/success lần lượt khoảng 4,1% và 50,0%. Ablation R2 giữ nguyên swarm và chỉ tắt ticket-delay override làm kết quả tăng từ 69/70 lên 70/70, nhưng cost/success vẫn 48,72 VND, cao hơn workflow. Vì vậy sửa lỗi verifier không chứng minh swarm cần thiết cho tác vụ này. Nhóm sẽ xem xét lại nếu workflow không đạt hidden ≥ 0,75, hoặc xuất hiện corpus cần điều tra thích ứng mà kiến trúc khác tăng thành công trên cùng điều kiện đo, vẫn giữ approval/crash/leak an toàn và phần cost tăng được chấp nhận.

## Trace và bằng chứng

Workflow ghi `quote`, `refund_request` và `decision` ở các nhánh tương ứng; nhánh sớm cũng đi qua `finish()` để có trace của chính lần chạy. Payload giới hạn ở amount, reason, clause IDs và status. Không ghi ticket, parent secret, raw headers, phone, address, item hoặc internal note. `RunResult.trace_path` trỏ tới JSON có run_id khớp; scanner đọc trace và các file cùng tiền tố run_id trong thư mục trace. Shape không tạo log bổ sung ngoài vị trí scanner; không dựa vào việc scanner bỏ sót stdout hoặc thư mục khác để kết luận an toàn. T11/T12 kiểm ledger sau retry/crash; crash suite không thay thế kiểm tra tích hợp từng shape.

Nguồn: [benchmark public](../../src/reports/benchmark.json), [crash suite](../../src/reports/crash.json), [hidden self-check](../../src/reports/hidden_selfcheck.json), [ablation R2](../../src/reports/research_ablation.json), [research card](research_card.md).

## Giới hạn

MockLLM không phải model sản xuất; single/orchestrator hiện điều khiển bằng Python cố định. Corpus public nhỏ và lỗi swarm được cài sẵn; không suy rộng rằng multi-agent luôn kém hơn. Latency là `unavailable`, cost là mô phỏng; steps phụ thuộc mức chi tiết trace. Hidden self-check không có nhãn nên pass rate, pass^5 và cost/success đều chưa biết; 0 leak_suspect không chứng minh không có mọi dạng rò PII. Lần chạy dùng Python 3.14.4, cần xác nhận lại trên Python 3.11–3.13 theo README. Giữ nguyên JSON do công cụ sinh, không sửa số cho đủ ngưỡng.
