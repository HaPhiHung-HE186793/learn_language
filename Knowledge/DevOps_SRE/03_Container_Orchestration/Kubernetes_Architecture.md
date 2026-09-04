# Container Orchestration & Kubernetes Architecture

> **Tài liệu Kubernetes Senior**: Pod Auto-scaling (HPA/VPA), Zero-Downtime Rolling Updates, và Ingress Routing.

Kiến trúc Container và Kubernetes (K8s)Containers: Là công nghệ ảo hóa cô lập bộ nhớ, tiến trình và mạng ở cấp độ không gian người dùng (user space) của hệ điều hành. Chúng giúp ứng dụng chạy nhất quán từ môi trường phát triển cục bộ lên hệ thống máy chủ sản xuất.Kubernetes là gì: Là một hệ thống điều phối container mã nguồn mở, giúp tự động hóa việc triển khai, mở rộng quy mô, và phục hồi các ứng dụng container.  Kiến trúc K8s: Cụm Kubernetes được chia thành hai phần chính:Control Plane (Node quản lý): Bộ não của cụm, nơi duy trì và quản lý trạng thái của hệ thống thông qua API server và kho lưu trữ etcd.  Worker Nodes: Các máy chủ thực tế chịu trách nhiệm chạy các container ứng dụng của bạn.  Các Đối tượng (Objects) Cốt lõi:Pod: Đơn vị nhỏ nhất và cơ bản nhất. Nó bọc một hoặc nhiều container có cùng chung địa chỉ IP, cổng mạng và bộ nhớ.  Deployment: Một bộ điều khiển quản lý số lượng các bản sao (replicas) của Pod luôn hoạt động và giúp thực hiện các bản cập nhật mà không gây gián đoạn (rolling updates).  Service: Tạo ra một IP ảo và định tuyến DNS ổn định để cân bằng tải và kết nối người dùng tới một nhóm các Pods (thường xuyên bị xóa đi hoặc tạo lại).  Namespace: Đường ranh giới logic giúp chia nhỏ và cô lập tài nguyên cho nhiều nhóm (teams) hoặc môi trường khác nhau trên cùng một cụm K8s.  

## 1. ZERO-DOWNTIME ROLLING UPDATES
- **Readiness & Liveness Probes**: Cấu hình Readiness Probe để K8s chỉ gửi traffic khi App đã sẵn sàng nhận kết nối DB.
- **Pod Disruption Budget (PDB)**: Đảm bảo tối thiểu 50% Pods luôn online khi Node bảo trì.
- **Resource Requests & Limits**: Bắt buộc đặt `requests.memory` và `limits.memory` để tránh OOMKilled lây lan.
