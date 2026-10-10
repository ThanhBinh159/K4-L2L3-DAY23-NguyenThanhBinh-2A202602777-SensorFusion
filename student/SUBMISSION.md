# Báo cáo bài nộp — Day 23 Sensor Fusion Lab

## Thông tin học viên

- Họ tên: Nguyen Thanh Binh
- MSSV: 2A202602777
- Email: binhnt1509204@gmail.com
- Link repo (fork): https://github.com/ThanhBinh159/K4-L2L3-DAY23-NguyenThanhBinh-2A202602777-SensorFusion
- Commit hash nộp (git rev-parse HEAD): Lấy từ commit cuối và nộp trên LMS sau khi push.

## Tóm tắt kết quả

- fusion_mode: compare; frames: [0, 198]; segment: training_segment-1005081002024129653_5313_150_5333_150_with_camera_labels.tfrecord; seed: 0
- detection.precision: 0.9700934579439252; detection.recall: 0.7004048582995951; tp/fp/fn: 519/16/222
- tracking.lidar: rmse=0.15032270744854423 m; matches=502; sum_sq_err=11.343652020079627 m²; ghost_track_frames=0; missed_gt_frames=239; mean_confirmed_tracks=2.522613065326633
- tracking.fused: rmse=0.13586677737057276 m; matches=502; sum_sq_err=9.266810158918522 m²; ghost_track_frames=0; missed_gt_frames=239; mean_confirmed_tracks=2.522613065326633
- precision_track: LiDAR 1.0000, fused 1.0000; coverage: LiDAR 0.9672, fused 0.9672.
- So sánh: RMSE fused trừ LiDAR = -0.0145 m; matches thay đổi +0; ghost thay đổi +0; miss thay đổi +0. RMSE chỉ tính trên các cặp ghép nên cần đọc cùng matches và ghost/miss.
- Tiêu chí RMSE fused không kém LiDAR quá 0.05 m: đạt.
- Giới hạn: camera dùng tâm hộp 2D nhãn FRONT thêm nhiễu theo seed; không có detector ảnh độc lập.
- Môi trường: notebook chuyển model và BEV tensor của Part C sang Kaggle T4 trong bộ nhớ; file Part C nộp giữ nguyên nên khi chạy lại trên CPU có thể khác nhẹ do số học.

## Giải thích ngắn (Parts E–H)

1. LiDAR đo z=(x,y,z) theo mét với R 3×3; camera đo z=(u,v) theo pixel với R 2×2. EKF dùng h(x) và H riêng cho từng sensor để tính innovation và update.
2. Mahalanobis so innovation với S=HPHᵀ+R; cổng χ² theo số chiều đo loại cặp khó tin trước gán greedy. Khoảng cách này có xét độ bất định, còn Euclidean thì không.
3. Lab là track-then-fuse: một danh sách track, predict một lần/frame, AssocL update LiDAR và quản lý track, rồi AssocC update camera. Thứ tự trong platform/fusion_lab/scripts/run_lab.py; grade_run.log ghi record lidar/fused theo frame.
4. Calibration lệch làm pixel dự báo h(x) lệch và innovation tăng. Gating có thể loại đo hoặc update sai nếu độ lệch lọt cổng. Đây là hệ quả mô hình, chưa phải kết quả bonus calibration.
5. Sensor cần truyền tường minh vì frame không có measurement không thể suy ra modality. LiDAR quyết định score/init/delete; camera chỉ update EKF.
6. Xác nhận khi score > 0.8; miss không hạ confirmed. Xóa khi Pxx hoặc Pyy > 9, confirmed có score < 0.6, hoặc track chưa confirmed có score ≤ 0.

## Bonus

Không.

## Khai báo sử dụng AI

- Công cụ đã dùng (ChatGPT, Copilot, Claude, …): ChatGPT/Codex.
- Dùng cho phần nào (hàm, câu hỏi, debug): Part E–H, notebook Kaggle, bản nháp giải thích và báo cáo số liệu.
- Cách bạn đã kiểm tra lại (pytest, chạy Waymo, đối chiếu công thức): Notebook đã chạy pytest student/tests -q, chạy compare seed 0 trên segment mặc định và runner đã đối chiếu metrics.json với grade_run.log. Học viên cần đọc lại code và báo cáo trước khi nộp.

## Checklist nộp

- [x] Part E–H đã implement và test trong repo pass khi chạy notebook
- [x] Compare seed 0, frame 0–198 và sáu artifacts do runner sinh
- [x] ZIP không chứa Waymo, weights, paths.yaml hay .env
- [ ] Đưa nội dung ZIP lên fork GitHub và chạy tools/check_submission.py
- [ ] Nộp link repo và commit hash trên LMS
