# Hoàn thành Lab 9 M1–M7 — Evaluation and Failure Analysis

Bài cá nhân của Đào Văn Hiệp. Eval set v1 có 14 item, v2 có 28 item, mỗi item chạy trên 5 seed. Đã triển khai code graders, judge calibration, paired bootstrap, slice gate, pass@k/pass^k và integrity/ratchet.

Baseline v1 `E-swarm-20261118-6a48`, SHA `6a48d2e5a5ff`. Baseline v2 `E-swarm-20261118-bb70`, SHA `bb70e0e9813d`.

Core cho fixed qua gate và chặn seeded_regression.

```text
fixed: exit 0 — merge
seeded_regression: Δ pass_rate -0.271 CI [-0.500, -0.057]
exit 1 — blocked: refund_amount_correct, pass_rate, reply_matches_ledger
```

Scorecard v2, chạy bằng `python -m lab9.mutation --spec config/metric_spec_v2.yaml --baseline reports/baseline_v2.json`.

```text
fixed                expect merge got merge ok
honest_refactor      expect merge got merge ok
seeded_regression    expect block got block ok
slice_regression     expect block got block ok
flaky_regression     expect block got block ok
mutation score 3/3 · false alarms 0
```

A/A chạy bằng `python -m lab9.mutation --spec config/metric_spec_v2.yaml --aa 66,77,88,99,110`. Mọi metric có delta 0. pass_hat_5 của cả hai bộ seed bằng 0.8929, chọn sàn blocking 0.80. flaky_regression có pass_hat_5 bằng 0.5714.

```text
metric       spec seeds  alt seeds  delta
pass_rate    0.9786      0.9786     +0.0000
pass_at_3    1.0000      1.0000     +0.0000
pass_hat_3   0.9357      0.9357     +0.0000
pass_at_5    1.0000      1.0000     +0.0000
pass_hat_5   0.8929      0.8929     +0.0000
```

Bốn trap chạy qua gate, output đầy đủ ở `src/reports/traps/gate_output.txt`.

```text
evaluator_errors_as_nan  exit 1 — blocked: integrity
dropped_failing_rows     exit 1 — blocked: integrity
cherry_picked_seeds      exit 1 — blocked: integrity
loosened_spec            exit 1 — blocked: integrity
```

Giới hạn gồm MockLLM, judge replay công khai, cỡ mẫu 5 seed và baseline/base-spec cần nguồn tin cậy. Chưa đo người dùng thật. Với 5 seed, lỗi hiếm chưa được phát hiện ổn định. Eval card có giải thích ngưỡng, slice và giới hạn integrity.

CI GitHub và merge vào main đang chờ sau khi commit/push và mở PR trong fork. Link repo nộp LMS lấy từ fork của người học.

Bài nộp M1–M7 đã được push lên main tại commit 3d0e412. PR submission/ci-review cập nhật trạng thái này và chạy CI trên toàn bộ bài nộp. Kiểm tra local đạt 35 test Lab 9, eval set v1/v2 có 0 lỗi. Trạng thái CI GitHub được theo dõi tại PR.
