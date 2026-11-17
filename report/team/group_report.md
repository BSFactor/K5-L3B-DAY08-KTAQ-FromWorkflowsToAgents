# Báo cáo nhóm — Lab 8 LogiX-VN

## 1. Thông tin nhóm

- Tên nhóm: KTAQ
- Thành viên: xem `TEAM.md`
- Commit chốt (trùng `report/team/decision_adr.md`): chưa chốt; cập nhật sau khi tích hợp nhánh và hoàn tất ADR. Commit code dùng cho benchmark: `7f07b295b8a6dacc37f666aac2f1933a8dca6e57` trên `main`.

## 2. Mục tiêu và cách tiếp cận

Nhóm xử lý claim hoàn phí của LogiX-VN bằng bốn kiến trúc để tìm mức tự chủ thấp nhất vẫn đáp ứng nghiệp vụ và các điều kiện an toàn. Dữ liệu ticket là đầu vào không đáng tin cậy; order và policy hiện hành là nguồn quyết định entitlement. Tiền do code tính, approval và quyền ghi ledger do host kiểm soát.

Thiết kế so sánh giữ cùng corpus, seed, MockLLM, bảng giá và evaluator. Benchmark dùng SQLite mới cho mỗi trial; riêng kiểm tra retry/crash dùng lại cùng database để phát hiện refund trùng. Benchmark đủ bốn shape đã hoàn tất trên code `main`; ADR và việc tích hợp code với tài liệu vẫn cần hoàn tất.

## 3. Giải pháp

### Workflow deterministic

Workflow xử lý claim theo chuỗi bước cố định: đọc claim bằng `get_claim_view`, kiểm tra `order_id`, tra cứu order theo phạm vi principal bằng `lookup_order`, tính full entitlement bằng `compute_compensation`, rồi chuyển full quote cho `issue_refund`. Shape không dùng MockLLM để quyết định nghiệp vụ hoặc số tiền; `model_calls` bằng 0 trong lần chạy kiểm chứng.

Các nhánh kết quả tuân theo contract chung: claim không tồn tại trả `failed`; thiếu `order_id` trả `needs_info`; order không hiển thị trả `escalated`; quote bằng 0 trả `denied` mà không ghi ledger; host từ chối approval trả `denied`; amount conflict trả `failed`; refund hợp lệ trả `resolved`. `proposal_amount` luôn giữ full quote khi quote lớn hơn 0, còn host chịu trách nhiệm áp approval, giới hạn theo order và idempotency.

Trace chỉ ghi các trường đã allowlist gồm số tiền quote, reason, clause ID, refund request và quyết định cuối. Workflow không ghi ticket, claim row, order row, secret hoặc PII. Public matrix của workflow đạt 62/62; retry C-001 và crash/resume C-012 đều giữ đúng một refund và một audit `refund_committed`.

### Single agent

Shape B dùng `run_react` tối đa 8 bước. Hàm `decide()` giữ memory, chọn `lookup_order`, sau đó `compute_compensation`, rồi trả quote cho code bên ngoài vòng lặp. Trường hợp thiếu order trả `needs_info`; order không hiển thị trả `escalated`. Số ngày trễ lấy từ order, không lấy từ ticket.

Mỗi bước gọi MockLLM và ghi usage vào Budget. Trong bản triển khai hiện tại, kết quả chữ của MockLLM không điều khiển lựa chọn tool: lựa chọn vẫn là các nhánh Python cố định. Vì vậy kết quả benchmark phản ánh vòng ReAct được mô phỏng, chưa chứng minh khả năng lập kế hoạch của model thật. Sau vòng lặp, shape gửi full quote và `crash_at` cho host; crash được ghi trace rồi ném lại để test quan sát.

Trace bình thường gồm `claim` với ID và cờ có order, `react` với kết quả đã lọc, và `decision`. Shape không ghi ticket hoặc row thô. Các nhánh return dùng `finish()` để tạo kết quả và trace theo contract.

### Orchestrator và handoff

Shape C tạo handoff bằng `make_handoff`, chỉ cấp các tool `lookup_order`, `lookup_policy`, `lookup_refund_history`, `compute_compensation`. Investigator thực thi qua `_delegate_investigation` và `dispatch(worker=True)`; parent nhận dữ kiện rồi gọi host để ghi refund. Đây là delegation theo trình tự cố định bằng code, không phải nhiều model tự lập kế hoạch.

Handoff dùng allowlist, facts ban đầu rỗng và phạm vi `owner_order_only`. Investigator đọc claim view để lấy định danh, đọc order theo principal và trả kết quả có giới hạn. Trace ghi handoff đã sanitize, kết quả các tool đọc, quote và quyết định; không ghi parent context, header hoặc secret. `InjectedCrash` được truyền ra sau khi ghi trace.

### Ranh giới tiền, approval và retry

Cả ba shape dùng `compute_compensation` và gửi quote đầy đủ đến `issue_refund`. Host áp approval đúng claim, trần thanh toán và phần entitlement đã hoàn theo order; shape không tự trừ tiền lịch sử. Refund và audit `refund_committed` được commit cùng transaction. Khóa `refund:{claim_id}` giúp retry không tạo effect mới; checkpoint chỉ ghi tiến độ, không thay ledger. Mô tả đầy đủ nằm trong [business rules](../../docs/business_rules.md) và [contracts](../../docs/contracts.md).

## 4. Kết quả

### Benchmark public đủ bốn shape

Nguồn: [src/reports/benchmark.json](../../src/reports/benchmark.json). File do `lab8.benchmark` sinh từ bản trích xuất sạch của commit `7f07b295b8a6dacc37f666aac2f1933a8dca6e57` trên `main`. Bộ public là `src/data/public_claims.jsonl` cùng world `src/data/public_world_state.json`: 14 claim × seed `11,22,33,44,55` × 4 shape = 280 trial, 70 trial mỗi shape. Các biến cố định gồm `as_of=2026-11-15`, MockLLM, bảng giá, evaluator và database mới cho từng trial. Môi trường chạy lần này: Python 3.14.4 trên Linux; cần kiểm lại trên Python 3.11–3.13 theo README trước khi nộp.

Lệnh tái lập trong `src/` của commit code trên:

```bash
python -m lab8.benchmark --shapes workflow,single,orchestrator,swarm --cases data/public_claims.jsonl --seeds 11,22,33,44,55 --out reports/benchmark.json
```

| Metric | Workflow | Single | Orchestrator | Swarm |
| --- | ---: | ---: | ---: | ---: |
| Trial pass | 70/70 | 70/70 | 70/70 | 69/70 |
| Pass rate | 1,000000 | 1,000000 | 1,000000 | 0,985714 |
| Pass^5 | 1,000000 | 1,000000 | 1,000000 | 0,928571 |
| Seed min–max | 1,000000–1,000000 | 1,000000–1,000000 | 1,000000–1,000000 | 0,928571–1,000000 |
| Seed variance | 0 | 0 | 0 | 0,000816 |
| Cost/success (VND mô phỏng) | 27,86 | 29,04 | 55,71 | 49,57 |
| Mean / p95 steps | 2,50 / 3 | 3,00 / 3 | 3,71 / 4 | 2,80 / 3 |
| Mean model calls | 0 | 2,79 | 0 | 3,00 |
| Mean tool calls | 2,79 | 2,79 | 5,57 | 4,66 |

Trial fail duy nhất là `R-swarm-C-002-s55`: swarm tin số ngày trễ trong ticket thay vì order. Ba shape nhóm viết đều pass claim này ở seed 55: `R-workflow-C-002-s55`, `R-single-C-002-s55`, `R-orchestrator-C-002-s55`. 

Trên bộ public này, workflow là ứng viên phù hợp cho nguyên tắc chọn kiến trúc ít tự chủ nhất vẫn làm được việc: đạt cùng pass rate và pass^5 với single/orchestrator, variance bằng 0, không gọi model và có cost/success thấp nhất. So với single, orchestrator và swarm, cost/success của workflow thấp hơn lần lượt khoảng 4,1%, 50,0% và 43,8%. Orchestrator không tăng số trial pass so với workflow nhưng dùng trung bình số tool call gấp đôi. Đây là kết luận giới hạn ở implementation và corpus hiện tại; quyết định nộp cuối cùng cần được ghi riêng trong ADR.

Steps là số event trong trace nên độ chi tiết của tracer ảnh hưởng chỉ số này; không dùng steps để suy ra latency. Cost là mô phỏng, latency là `unavailable`. Benchmark public này không thay thế T11/T12 hoặc chấm hidden; đặc biệt C-011/C-012 trong benchmark chỉ chạy như trial độc lập, không tạo kịch bản retry/crash.

### Retry, crash và kiểm tra an toàn

Khi tích hợp workflow, host suite đạt 56 passed và toàn bộ student suite đạt 283 passed. Ma trận mỗi shape có 62 test, gồm T11 retry và T12 crash/resume. Test bổ sung kiểm workflow không dùng model, handoff/trace của orchestrator, và 35 test CP4 của single cho approval, ticket injection và secret/PII.

Các report `src/reports/crash.json` và `src/reports/crash_ldlda.json` trên `main` ghi crash safety 20/20. Suite này kiểm host trực tiếp và một ca swarm; crash của ba shape được kiểm bằng T12. Kết quả public và các test bổ sung không thay thế phép chấm hidden hoặc chứng minh không có rò rỉ trên mọi đầu vào.

### Phần cần hoàn tất trước khi nộp

- Tích hợp code `main` và tài liệu `team_report` vào nhánh nộp; hiện JSON benchmark được sinh từ code `main`, còn code shape trên `team_report` vẫn là stub. Chạy lại benchmark khi thay đổi code sau tích hợp.
- Chạy hidden self-check khi có `student_drop/`; report này là tùy chọn theo SUBMISSION, và chưa có kết quả hidden trong bằng chứng đang dùng.
- Hoàn tất [decision_adr.md](decision_adr.md), [research_card.md](research_card.md), ghi run ID và commit chốt nhất quán. Chưa đưa ra lựa chọn kiến trúc cuối cùng trong bản thảo này.

## 5. Khó khăn và cách xử lý

- Trên Windows, pytest ban đầu báo `PermissionError: [WinError 5]` tại thư mục `%TEMP%\\pytest-of-minhq`, khiến 62 case workflow lỗi ở bước setup trước khi code được chạy. Nhóm chuyển `basetemp` vào repository bằng `--basetemp .pytest-tmp\\workflow` và thêm `.pytest-tmp/` vào `.gitignore`. Sau đó ma trận workflow chạy thành công với 62 passed.

- `run_react` chỉ giữ kết quả tool gần nhất. Single dùng closure memory để mang order qua các bước; quote vẫn đến từ code.
- Crash có thể xảy ra sau khi ghi refund nhưng trước checkpoint. Các shape chuyển tiếp `crash_at`; T12 kiểm crash thật và resume trên cùng DB để xác nhận không trả hai lần.
- Approval hợp lệ nhưng thuộc claim khác vẫn phải bị từ chối. Test single bổ sung kiểm đúng audit `approval_claim_mismatch`, bên cạnh approval hết hạn, bị thu hồi hoặc thiếu.

## 6. Tổng hợp đóng góp

| Thành viên | Đóng góp có bằng chứng |
| --- | --- |
| Nguyễn Nam Khánh | Điều phối, rà soát và tích hợp commit/PR; hoàn tất benchmark public bốn shape (280 trial), đối chiếu số liệu và report single–swarm trước đó, tích hợp bảng so sánh và phân tích chi phí vào báo cáo nhóm. |
| Nguyễn Minh Quân | Workflow cố định, full quote và crash/retry, test không dùng model, ghi chú Windows và báo cáo cá nhân. |
| Lương Đức Anh | Orchestrator, investigator chỉ đọc, handoff đã sanitize, test trace, bằng chứng crash suite và báo cáo cá nhân. |
| Lê Anh Tuấn | Single, 35 test CP4, bằng chứng crash suite, benchmark single–swarm và báo cáo cá nhân. |

Chi tiết commit, test và phần cá nhân nằm trong [TEAM.md](../../TEAM.md). Báo cáo cá nhân của ba thành viên đã có trên `main` theo mẫu `report/team/MSSV_HoTen.md`.

## 7. Hạn chế và hướng cải thiện

MockLLM không phải model sản xuất; cost là mô phỏng, latency là `unavailable`. Single và orchestrator hiện dùng điều khiển cố định bằng code, nên số liệu không đo đầy đủ tính tự chủ hay lỗi lập kế hoạch của LLM. Bộ public nhỏ, swarm có điểm yếu được cài sẵn; chưa đủ để kết luận kiến trúc nào tốt nhất ngoài bài lab.

Cần chốt ADR dựa trên benchmark bốn shape đã có và bổ sung hidden self-check nếu được cung cấp `student_drop/`. Chưa có bằng chứng để xác nhận ngưỡng hidden ≥ 0,75. Nếu một kiến trúc có mức tự chủ cao hơn xử lý tốt hơn các claim cần điều tra thích ứng, đồng thời giữ approval/crash/leak an toàn và chi phí chấp nhận được, nhóm sẽ xem xét lại lựa chọn. Muốn đánh giá model thật cần một thí nghiệm riêng với kiểm soát prompt, tool, ngân sách và corpus phù hợp.

## 8. Tài liệu tham khảo

- [README và mục tiêu lab](../../README.md), [checkpoints](../../CHECKPOINTS.md).
- [Business rules](../../docs/business_rules.md), [output/trace contracts](../../docs/contracts.md).
- [Architecture](../../docs/architecture.md), [định nghĩa metric](../../docs/metrics.md).
- Code và test của ba shape tại commit `7f07b295b8a6dacc37f666aac2f1933a8dca6e57` trên `main`; [benchmark bốn shape](../../src/reports/benchmark.json). Benchmark single–swarm và hai crash report trong `src/reports/` trên `main` là bằng chứng bổ sung.
- Báo cáo cá nhân: `2A202603747_NguyenMinhQuan.md`, `2A202603931_LuongDucAnh.md`, `2A202603792_LeAnhTuan.md` trên `main`.
