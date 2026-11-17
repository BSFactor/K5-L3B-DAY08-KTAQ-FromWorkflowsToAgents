# Team

## Thông tin nhóm

- Tên nhóm:KTAQ
- Lớp: L3B
- Repo nhóm: K5-L3B-DAY08-KTAQ-FromWorkflowsToAgents
- URL repo, nhánh nộp, commit chốt:https://github.com/BSFactor/K5-L3B-DAY08-KTAQ-FromWorkflowsToAgents, main

## Thành viên

| STT | Họ tên | MSSV | Email | GitHub | Vai trò |
| --: | ------ | ---- | ----- | ------ | ------- |
|   1 |    Nguyễn Nam Khánh    |  2A202603871    |    26ai.khanhnn3@vinuni.edu.vn   |    BSFactor    | Điều phối, rà soát và tích hợp commit/PR, hoàn tất benchmark public bốn shape (CP5), tổng hợp bằng chứng và tài liệu nhóm |
|   2 |     Nguyễn Minh Quân   |  2A202603747    |   26ai.quannm8@vinuni.edu.vn    |   minhquannm0905-cloud     |    Triển khai workflow, xử lý retry/crash, test tính xác định và báo cáo cá nhân |
|   3 |    Lương Đức Anh    |   2A202603931   |    26ai.anhld5@vinuni.edu.vn   |    ldlda    | Triển khai orchestrator, handoff chỉ đọc, xử lý retry/crash và test trace |
|   4 |    Lê Anh Tuấn    |  2A202603792    |  26ai.tuanla3@vinuni.edu.vn     |   tuancdk     | Triển khai single agent, test CP4, benchmark single–swarm (CP5) và báo cáo cá nhân |

Gợi ý chia vai:

- Nhóm 6 người: integration; workflow; single agent; orchestrator; ledger/policy/crash; eval/report.
- Nhóm 4 người: workflow + integration; single; orchestrator + handoff; ledger + evaluation.

## Bảng đóng góp

`python -m lab8.check_submission` đọc bảng này bằng máy: giữ nguyên tiêu đề `## Bảng đóng góp` và các cột. Mỗi người một dòng: commit có nội dung và ít nhất một test đã thêm hoặc sửa.

| Người | Vai | Commit | Test đã thêm hoặc sửa |
| --- | --- | --- | --- |
| Nguyễn Nam Khánh | Điều phối, rà soát và tích hợp commit/PR, hoàn tất benchmark public bốn shape (CP5), tổng hợp kết quả và báo cáo nhóm | `6cf86b2`, `5891738`, `98c97b9`, `8bf483b`, `df06fb9`, `cb48259`, `8fc7cf1`, `e1a2dc1`; benchmark mới chưa commit | Chạy host/student suite và đối chiếu 280 trial benchmark; chưa thêm hoặc sửa test riêng |
| Nguyễn Minh Quân | Workflow cố định, retry/crash, test và tài liệu đóng góp | `34900b4`, `c82846a`, `5a84fd0`, `3550b19` | Thêm `src/tests/test_workflow_student.py::test_workflow_is_deterministic_and_model_free` |
| Lương Đức Anh | Orchestrator, handoff chỉ đọc, crash/retry và tài liệu đóng góp | `a2b887d`, `6b135cb`, `3baec28` | Thêm `src/tests/test_orchestrator_handoff.py::test_orchestrator_trace_contains_only_sanitized_handoff` |
| Lê Anh Tuấn | Single agent, test CP4, crash evidence, benchmark single–swarm và báo cáo cá nhân | `a39e9bd`, `1b19db7`, `2fe76f3`, `2663961`, `caf46c7` | Thêm `src/tests/test_single_cp4.py` (approval, refund cap, injection, trace/stdout leak) |

## Cá nhân

Mỗi thành viên đồng thời tạo một báo cáo riêng trong `report/` theo dạng:

```text
report/team/MSSV_HoTen.md
```

Ví dụ: `report/team/20241234_NguyenVanAn.md`.

Sao chép mục dưới đây cho từng thành viên.

## 2A202603871_NguyenNamKhanh

### Phần việc phụ trách

- Điều phối công việc, rà soát và tích hợp commit/PR; hoàn tất benchmark public bốn shape (CP5), tổng hợp bằng chứng, đóng góp và tài liệu nhóm.

### Đóng góp cụ thể

- Rà soát triển khai single, orchestrator và workflow; chạy host/student suite khi tích hợp và phân biệt phạm vi crash suite với T12 của từng shape.
- Tích hợp workflow, xử lý xung đột TEAM.md bằng cách giữ bảng đóng góp tập trung trên `team_report`; cập nhật bản sửa thông tin liên hệ.
- Tổng hợp đóng góp theo commit, bổ sung phần cá nhân và soạn báo cáo nhóm, giữ nội dung workflow do Nguyễn Minh Quân viết.
- Chạy benchmark public đủ bốn shape trên 14 claim × 5 seed từ code `main` commit `7f07b29`, sinh `src/reports/benchmark.json`; đối chiếu metric với 280 trial và xác nhận single–swarm khớp report trước của Tuấn.
- Tích hợp bảng kết quả, run ID lỗi và phân tích chi phí vào báo cáo nhóm; phần benchmark mới chưa có commit riêng.
- Commit tài liệu: `6cf86b2`, `5891738`, `98c97b9`, `8bf483b`, `df06fb9`; commit tích hợp workflow: `cb48259`, `8fc7cf1`.

### Bằng chứng

- Khi tích hợp đủ ba shape: host suite 56 passed, student suite 283 passed; kiểm riêng single 97 passed, orchestrator 62 passed và crash suite 20/20 ở các lần rà soát trước.
- `src/reports/benchmark.json`: workflow, single, orchestrator đều pass 70/70; swarm 69/70. Cost/success mô phỏng lần lượt 27,86; 29,04; 55,71; 49,57 VND. Chi tiết tại mục 4 báo cáo nhóm và báo cáo cá nhân.
- Chưa thêm hoặc sửa test riêng; bằng chứng kiểm tra đến từ các suite hiện có và test của thành viên.
- Báo cáo nhóm: `report/team/group_report.md`; báo cáo cá nhân: `report/team/2A202603871_NguyenNamKhanh.md`.

## 2A202603747_NguyenMinhQuan

### Phần việc phụ trách

- Shape A (`workflow`): luồng xử lý cố định, retry/crash và trace an toàn.

### Đóng góp cụ thể

- `34900b4`: triển khai đọc claim/order đúng phạm vi, tính full quote bằng code, chuyển quote và `crash_at` cho host; không gọi model. Thêm test workflow không dùng model và cấu hình bỏ qua thư mục test tạm.
- `c82846a`: ghi chú giải pháp, lỗi pytest trên Windows và báo cáo cá nhân.
- `5a84fd0`, `3550b19`: cập nhật thông tin liên hệ trong tài liệu.

### Bằng chứng

- `src/tests/test_workflow_student.py::test_workflow_is_deterministic_and_model_free`: kiểm refund 35.000 VND, 0 model call, 3 tool call và một committed audit.
- Ma trận public của workflow: 62 test pass, gồm T11 retry và T12 crash/resume (đã kiểm khi tích hợp).
- Báo cáo: `report/team/2A202603747_NguyenMinhQuan.md` trên `main`; run mẫu `R-workflow-C-001-s11`.

## 2A202603931_LuongDucAnh

### Phần việc phụ trách

- Shape C (`orchestrator`): giao việc qua handoff đã sanitize, investigator chỉ đọc, retry/crash và trace.

### Đóng góp cụ thể

- `a2b887d`: triển khai orchestrator và investigator với tool chỉ đọc; chuyển full quote cho host xử lý approval, trần hoàn tiền và ledger.
- `6b135cb`: thêm test handoff/trace và bằng chứng crash suite của host/swarm.
- `3baec28`: ghi đóng góp và báo cáo cá nhân.

### Bằng chứng

- `src/tests/test_orchestrator_handoff.py::test_orchestrator_trace_contains_only_sanitized_handoff`: kiểm handoff, tool được phép và trace không chứa secret/header.
- Ma trận public của orchestrator: 62 test pass, gồm T11 retry và T12 crash/resume (đã kiểm khi tích hợp).
- Bằng chứng crash suite: `src/reports/crash_ldlda.json` trên `main`, 20/20; suite này kiểm host/swarm.
- Báo cáo: `report/team/2A202603931_LuongDucAnh.md` trên `main`; run mẫu `R-orchestrator-C-001-s11`.

## 2A202603792_LeAnhTuan

### Phần việc phụ trách

- Shape B (`single`): vòng ReAct tối đa 8 bước, retry/crash, kiểm tra CP4 và benchmark single so với swarm ở CP5.

### Đóng góp cụ thể

- `a39e9bd`: triển khai single với tool đọc, tính quote bằng code và gọi host sau vòng lặp; chuyển tiếp crash injection.
- `1b19db7`: thêm 35 test CP4 cho approval, trần hoàn tiền, ticket injection và rò rỉ secret/PII qua trace, kết quả, stdout/stderr.
- `2fe76f3`: thêm crash suite evidence cho host/swarm.
- `2663961`: thêm báo cáo cá nhân và benchmark single–swarm trên 14 claim × 5 seed; `caf46c7`: bổ sung email.

### Bằng chứng

- `src/tests/test_single_cp4.py`: 35 test; cộng 62 test ma trận single thành 97 test pass (đã kiểm khi tích hợp).
- `src/reports/benchmark_single_swarm.json` trên `main`: single pass 70/70, pass^5 = 1, cost/success mô phỏng 29,04 VND; swarm pass 69/70, cost/success 49,57 VND.
- Benchmark hai shape là bằng chứng bổ sung cho CP5; quyết định kiến trúc của nhóm cần benchmark đủ bốn shape.
- Báo cáo: `report/team/2A202603792_LeAnhTuan.md` trên `main`; run đối chiếu `R-single-C-002-s55` và `R-swarm-C-002-s55`.
