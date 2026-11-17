# Research card

File này nằm cố định ở `report/team/research_card.md`; `python -m lab8.check_submission` đọc cột "Nhóm điền" bằng máy.

Phần này nối tiếp phần mini research ở buổi lý thuyết (slide 62–63). Chọn **một** câu R1–R4, điền 6 dòng, rồi biến nó thành **một ablation** chạy được trong lab: giữ nguyên model (MockLLM), tool, seed, corpus và giá, chỉ thay đổi một biến.

| Câu | Nguồn (thư viện tham khảo, slide 68–69) | Câu hỏi gợi ý cho lab |
| --- | --- | --- |
| R1 · Scaling | Google Research 2026, *Towards a Science of Scaling Agent Systems* | Task claims chủ yếu tuần tự hay song song được? Dự đoán thêm investigator sẽ làm pass rate và cost/success thay đổi thế nào. |
| R2 · Failure taxonomy | MAST, NeurIPS 2025, *Why Do Multi-Agent LLM Systems Fail?* | Các trial fail của swarm (seed 55) thuộc nhóm nào: lỗi giao việc, lệch mục tiêu, hay lỗi kiểm chứng? |
| R3 · Architecture choices | AgentArch, AACL 2026 | Nếu đổi topology mà giữ nguyên mọi thứ khác, biến nào phải cố định để kết luận còn đúng? |
| R4 · Evaluation confounders | EMNLP 2026, *Rethinking Evaluation of MAS Efficiency* | Làm sao chứng minh chênh lệch giữa A/B/C/D là do topology, không phải do prompt, tool hay seed? |

## Phiếu của nhóm

Điền vào cột cuối. Không xóa hai cột đầu.

| Field | Hướng dẫn | Nhóm điền |
| --- | --- | --- |
| CÂU | R1 / R2 / R3 / R4 | R2 · Failure taxonomy |
| SOURCE | Paper/blog, năm, URL, loại bằng chứng (benchmark, production report, blog kinh nghiệm) | Cemri et al., “Why Do Multi-Agent LLM Systems Fail?”, NeurIPS 2025 Datasets and Benchmarks Track; nghiên cứu thực nghiệm trace/taxonomy. [Bản chính thức](https://proceedings.neurips.cc/paper_files/paper/2025/hash/b1041e52d3be19f0a9bc491657488e4a-Abstract-Datasets_and_Benchmarks_Track.html). |
| CLAIM | Một kết luận gốc của nguồn. Không viết rộng hơn phạm vi paper | MAST xác định 14 dạng lỗi trong ba nhóm: thiết kế hệ thống, lệch mục tiêu giữa agent và kiểm chứng tác vụ. Taxonomy được xây dựng từ phân tích trace có người gán nhãn; nó giúp chẩn đoán lỗi thay vì mặc định thêm agent sẽ cải thiện kết quả. |
| BOUNDARY | Model / dataset / setup giới hạn kết luận đó | Paper phân tích trace từ các framework/model và tác vụ trong nghiên cứu, không đánh giá LogiX-VN hay MockLLM. Gán lỗi T02 của lab vào nhóm kiểm chứng tác vụ là diễn giải của nhóm, không phải nhãn chính thức từ tác giả. |
| HYPOTHESIS | Nếu đổi shape A→B (hoặc bật/tắt một thành phần), metric X sẽ đổi theo hướng nào, khoảng bao nhiêu | Dựa trên fixture và điểm yếu swarm đã công bố, tắt ticket-delay override sẽ sửa T02 seed 55: swarm 69/70 → 70/70, pass^5 13/14 → 14/14, variance → 0. Giữ ba model call/trial; tool call giảm một lần recompute ở trial bị sửa. Đây là kiểm chứng cơ chế lỗi đã biết, không phải dự đoán trên dữ liệu chưa xem. |
| METHOD | Biến giữ cố định: model, tools, seed, corpus, giá, evaluator. Biến duy nhất được thay đổi: … | Đối chiếu `swarm` với `swarm_order_only` trong `bonus/research_ablation.py`, sao từ reference và chỉ bỏ block ghi đè delay bằng ticket; tên shape/run đổi để tách trace. Giữ MockLLM, prompt/call sites, registry tool, 14 claim public, world, seed 11,22,33,44,55, giá, evaluator, host và DB mới mỗi trial. Lượt recompute ít đi là hệ quả của việc bỏ override, không phải thay tool hay bảng giá. |
| FAILURE / METRIC | Một lỗi dự kiến và cách đo nó: pass rate, pass^5, variance, cost/success, leak, bypass | Lỗi dự kiến: thiếu đối chiếu với order khiến ticket ghi 5 ngày thay thế order ghi 2 ngày. Đọc event `model_override` cùng verdict/ledger của T02 seed 55; đo pass rate, pass^5, seed variance, cost/success và model/tool calls. Phân loại MAST ở mức diễn giải: kiểm chứng tác vụ. |

## Kết quả ablation

- Lệnh đã chạy (copy nguyên văn): `python -m lab8.benchmark --shapes swarm,swarm_order_only --extra-shape swarm_order_only=bonus.research_ablation:run_claim --cases data/public_claims.jsonl --seeds 11,22,33,44,55 --out reports/research_ablation.json` (chạy trong `src/`, với `.venv/bin/python` là interpreter).
- Run id / file report: [src/reports/research_ablation.json](../../src/reports/research_ablation.json); `R-swarm-C-002-s55` và `R-swarm_order_only-C-002-s55`.
- Kết quả so với hypothesis (đúng / sai / không kết luận được), kèm số liệu: ủng hộ cơ chế dự đoán trên fixture đã biết; swarm 69/70, variant 70/70. T02 seed 55 từ `resolved` sai thành `denied` đúng; bốn seed còn lại giữ trace events và cost như cũ trong test tập trung.
- Giới hạn: MockLLM không phải model sản xuất, latency là `unavailable`, cost là mô phỏng. Lỗi được cài sẵn, chỉ một trial thay verdict; không xác nhận mọi lỗi multi-agent đều thuộc nhóm kiểm chứng hoặc đều sửa được bằng biện pháp này. Ablation thay nguồn dữ kiện tin cậy, không chứng minh lợi ích nhân quả của topology. Đây là thí nghiệm cho research card, không đăng ký hay tự nhận điểm bonus.

| Metric | Swarm reference | Swarm order-only |
| --- | ---: | ---: |
| Trial pass | 69/70 | 70/70 |
| Pass rate | 0,985714 | 1,000000 |
| Pass^5 | 0,928571 | 1,000000 |
| Seed min–max | 0,928571–1,000000 | 1,000000–1,000000 |
| Seed variance | 0,000816 | 0 |
| Cost/success (VND mô phỏng) | 49,57 | 48,72 |
| Tổng cost (VND mô phỏng) | 3420,12 | 3410,10 |
| Mean model calls | 3,00 | 3,00 |
| Mean tool calls | 4,657143 | 4,642857 |

Test tái lập: `python -m pytest -q tests/test_research_ablation.py` — 5 passed. Test dùng DB riêng cho mỗi nhánh, kiểm verdict và ledger qua evaluator, giữ reference failure tại seed 55, và kiểm số model call không đổi.

Muốn đi xa hơn một ablation để lấy điểm cộng thì xem [docs/bonus_topics.md](../../docs/bonus_topics.md).
