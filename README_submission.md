# Báo cáo nộp bài Lab 17

**Học viên:** Nguyễn Đỗ Khải Hoàn

**Mã học viên:** 2A202601379

**Kết quả practice:** 11/11 PASS (100%); no-memory: 2/11 PASS (18,2%).

## Câu hỏi cốt lõi

**Layer quan trọng nhất trong bộ test:** Long-term memory đóng góp nhiều điểm nhất (20/56), bao phủ E02, E03, E08 và E09. Nó giữ preference và open loop qua nhiều thread, xử lý cập nhật theo recency, đồng thời cách ly dữ liệu giữa Minh và Lan.

**Zep Context Block so với Redis + Qdrant:** Zep tự trích xuất fact, dựng graph, chọn context liên quan và hỗ trợ user scope, nhưng phụ thuộc dịch vụ cloud, có độ trễ và ít kiểm soát pipeline. Redis + Qdrant cho độ trễ thấp và quyền kiểm soát dữ liệu tốt hơn, nhưng phải tự xây extraction, conflict resolution, compaction, retrieval và deletion.

**Guardrail chống memory poisoning:** Chỉ ghi durable memory khi người dùng opt-in; kiểm tra và làm sạch nội dung trước ingestion; lưu source, timestamp, confidence và scope; ưu tiên fact mới khi xung đột nhưng vẫn giữ provenance; không cho heartbeat tự cấp quyền hoặc ghi instruction mới; luôn kiểm tra user ID để tránh rò rỉ chéo.

## Phân tích benchmark

1. Không layer nào bị fail trong student benchmark: mọi layer đạt 100%. Ở baseline, long-term, episodic và semantic đều 0%.
2. E03 retrieve nhiều nhất với 1.390 token do Context Block và fact edges chứa open loop cùng provenance.
3. E07 cần long-term (`Python`) kết hợp semantic (`Idempotency-Key`); thiếu một trong hai sẽ không đủ evidence.
4. Student giảm trung bình 14,2% token và đạt 100% hit rate. No-memory giảm 81,8% nhưng chỉ đạt 18,2% vì không retrieve durable context; giảm token không đồng nghĩa retrieval tốt.

E08 cho thấy recency phải gắn với scope: BLUEBIRD-42 dùng TypeScript/NestJS, còn Python vẫn áp dụng cho ORCHID-27. E10 compact raw turns nhưng giữ `REVIEW-DEADLINE-1600`, Friday và 16:00 trong durable notes.
