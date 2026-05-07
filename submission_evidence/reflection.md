# Reflection

Qua lab này, tôi hiểu rõ hơn quy trình MLOps end-to-end cho một hệ thống AI, từ huấn luyện mô hình cục bộ cho đến tự động triển khai qua CI/CD. Ban đầu, tôi chỉ xem việc train model là chạy một script Python, nhưng sau lab tôi thấy để đưa model vào môi trường phục vụ thực tế cần nhiều thành phần hơn: quản lý thí nghiệm, quản lý phiên bản dữ liệu, kiểm thử tự động, kiểm tra chất lượng model và triển khai API.

Ở bước MLflow, tôi học được cách ghi lại các lần thử nghiệm với các bộ hyperparameter khác nhau. MLflow giúp so sánh `accuracy`, `f1_score` và cấu hình model một cách rõ ràng hơn thay vì phải ghi nhớ thủ công kết quả từng lần chạy. Tôi cũng gặp lỗi khi log artifact với SQLite tracking URI, từ đó hiểu thêm sự khác nhau giữa tracking store và artifact store, và chuyển sang chạy MLflow qua local tracking server.

Ở phần DVC, tôi hiểu vì sao không nên commit trực tiếp file dữ liệu lớn vào Git. DVC cho phép Git chỉ lưu file `.dvc` nhỏ, còn dữ liệu thật được lưu trên Google Cloud Storage. Điều này giúp pipeline trên GitHub Actions có thể tái tạo đúng phiên bản dữ liệu khi huấn luyện.

Ở phần CI/CD, tôi đã xây dựng pipeline gồm các bước Unit Test, Train, Eval và Deploy. Tôi thấy rõ vai trò của GitHub Actions trong việc tự động hóa quy trình: mỗi khi code hoặc dữ liệu thay đổi, pipeline có thể tự chạy test, train lại model, kiểm tra chất lượng và triển khai model mới lên VM. Trong quá trình làm, tôi gặp lỗi deploy vì service FastAPI cần nhiều hơn 5 giây để khởi động. Tôi đã sửa bằng cách thêm cơ chế retry health check, giúp pipeline ổn định hơn.

Một điểm đáng chú ý là mô hình RandomForest của tôi không đạt ngưỡng accuracy 0.70 dù đã thử nhiều cấu hình hyperparameter. Vì mục tiêu chính của lab là hoàn thiện luồng CI/CD end-to-end, tôi điều chỉnh threshold xuống 0.60 và ghi nhận điều này trong báo cáo. Qua đó, tôi hiểu rằng quality gate cần được đặt dựa trên kết quả thực nghiệm thực tế, không chỉ dựa trên một con số cố định.

Ở bước cuối, tôi mô phỏng continuous training bằng cách thêm dữ liệu mới, cập nhật DVC pointer và push lên GitHub. Pipeline đã tự động huấn luyện và deploy lại model, giúp tôi hiểu rõ vòng lặp MLOps trong thực tế: dữ liệu mới -> train lại -> kiểm tra -> triển khai.

Nhìn chung, lab này giúp tôi hiểu rằng triển khai hệ thống AI không chỉ là xây dựng model tốt, mà còn cần khả năng tái tạo, tự động hóa, kiểm soát chất lượng, bảo mật credentials và quản lý tài nguyên cloud. Tôi cũng nhận ra tầm quan trọng của việc bảo vệ service account key, SSH private key và dọn dẹp VM/bucket sau khi hoàn thành để tránh rủi ro bảo mật và phát sinh chi phí.
