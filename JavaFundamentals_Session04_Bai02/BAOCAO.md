# Báo cáo Bài 2: Quản lý nhánh và Giải quyết xung đột (Merge Conflict)

- **Môn học:** Java Fundamental - Session 04
- **Đường dẫn nộp bài dự kiến:** `homework/session_04/ex2/`

---

## 1. Mục tiêu bài tập
- Nắm vững thao tác tạo và chuyển đổi giữa các nhánh cục bộ (`git branch`, `git checkout` / `git switch`).
- Tự tạo xung đột gộp nhánh (Merge Conflict) có chủ đích trên cùng một file (`README.md`).
- Hiểu cấu trúc các thẻ đánh dấu xung đột (`<<<<<<< HEAD`, `=======`, `>>>>>>> feature-update`) và xử lý thủ công.
- Hiểu nguyên lý hoạt động của cơ chế **3-Way Merge** (gộp 3 vùng) trong Git.
- Hoàn thành commit gộp nhánh và quan sát đồ thị phân nhánh bằng `git log --graph --oneline`.

---

## 2. Các bước thực hiện chi tiết

### Bước 1: Khởi tạo Repository và commit ban đầu trên nhánh `main`
```bash
git init -b main
# Tạo file README.md ban đầu
git add README.md
git commit -m "Initial commit: Tạo file README.md ban đầu"
```

### Bước 2: Tạo và chuyển sang nhánh `feature-update`
```bash
git checkout -b feature-update
```
- Chỉnh sửa nội dung file `README.md` tại các dòng mô tả phiên bản và tính năng:
  ```markdown
  Phiên bản: 1.1.0-feature
  Trạng thái: Nhánh feature-update bổ sung tính năng quản lý người dùng và sản phẩm mới.
  ```
- Tiến hành commit thay đổi trên nhánh phụ:
  ```bash
  git commit -am "feat: Cập nhật tính năng trên nhánh feature-update"
  ```

### Bước 3: Quay trở lại nhánh `main` và tạo thay đổi xung đột
```bash
git checkout main
```
- Chỉnh sửa cùng các dòng đó trong file `README.md` với nội dung khác:
  ```markdown
  Phiên bản: 1.1.0-hotfix
  Trạng thái: Nhánh main cập nhật vá lỗi bảo mật và tối ưu hiệu năng hệ thống.
  ```
- Tiến hành commit trên nhánh chính:
  ```bash
  git commit -am "fix: Cập nhật bản vá bảo mật trực tiếp trên main"
  ```

### Bước 4: Thực hiện gộp nhánh (Merge) và kích hoạt Conflict
Đang đứng ở nhánh `main`, thực hiện gộp nhánh `feature-update`:
```bash
git merge feature-update
```
**Kết quả hiển thị trên Terminal:**
```text
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```
> Git thông báo quá trình tự động gộp thất bại do cùng một vị trí trong file `README.md` bị sửa đổi khác nhau ở cả 2 nhánh kể từ commit gốc chung.

### Bước 5: Phân tích và xử lý xung đột thủ công

Mở file `README.md`, Git đã chèn các ký hiệu đánh dấu vùng xung đột:
```markdown
<<<<<<< HEAD
Phiên bản: 1.1.0-hotfix
Trạng thái: Nhánh main cập nhật vá lỗi bảo mật và tối ưu hiệu năng hệ thống.
=======
Phiên bản: 1.1.0-feature
Trạng thái: Nhánh feature-update bổ sung tính năng quản lý người dùng và sản phẩm mới.
>>>>>>> feature-update
```

**Ý nghĩa các thẻ:**
- `<<<<<<< HEAD`: Bắt đầu vùng code hiện tại của nhánh đang đứng (`main`).
- `=======`: Ranh giới ngăn cách giữa thay đổi của 2 nhánh.
- `>>>>>>> feature-update`: Kết thúc vùng code đến từ nhánh được gộp vào (`feature-update`).

**Cách xử lý thủ công:**
Xóa các dòng đánh dấu `<<<<<<< HEAD`, `=======`, `>>>>>>> feature-update`, sau đó tổng hợp nội dung hợp lý từ cả hai nhánh:
```markdown
# Dự án Demo Git - Session 04

## Giới thiệu
Phiên bản: 1.1.0 (Tích hợp tính năng & Vá lỗi bảo mật)
Trạng thái: Đã gộp thành công tính năng quản lý người dùng, sản phẩm từ nhánh feature-update và bản vá bảo mật, tối ưu hiệu năng từ nhánh main.
```

### Bước 6: Đánh dấu đã giải quyết và hoàn tất Merge Commit
```bash
git add README.md
git commit -m "Merge branch 'feature-update': Gộp nhánh và giải quyết xung đột thủ công trong README.md"
```

---

## 3. Tìm hiểu Cơ chế gộp 3 vùng (3-Way Merge)
Khi thực hiện `git merge`, Git không chỉ so sánh commit hiện tại trên `main` và commit mới nhất trên `feature-update`, mà thực hiện thuật toán **3-Way Merge** dựa trên 3 điểm:
1. **Common Ancestor (Base commit):** Điểm rẽ nhánh chung gần nhất của cả 2 nhánh (Commit ban đầu `2b7882c`).
2. **Current Branch (Ours / HEAD):** Phiên bản hiện tại trên `main` (`f7b01e9`).
3. **Incoming Branch (Theirs):** Phiên bản mới từ nhánh `feature-update` (`f840a08`).

- Nếu một dòng chỉ thay đổi ở một nhánh so với Base commit, Git sẽ tự động áp dụng thay đổi đó.
- Nếu cả hai nhánh đều sửa đổi cùng một dòng so với Base commit nhưng theo hai cách khác nhau, Git không thể tự đoán biết nên giữ phần nào => Kích hoạt **Merge Conflict** để lập trình viên xử lý thủ công.

---

## 4. Kết quả kiểm tra lịch sử commit đồ thị

Chạy lệnh kiểm tra:
```bash
git log --graph --oneline
```

**Kết quả đạt được:**
```text
*   2182966 Merge branch 'feature-update': Gộp nhánh và giải quyết xung đột thủ công trong README.md
|\  
| * f840a08 feat: Cập nhật tính năng trên nhánh feature-update
* | f7b01e9 fix: Cập nhật bản vá bảo mật trực tiếp trên main
|/  
* 2b7882c Initial commit: Tạo file README.md ban đầu
```

**Nhận xét:**
- Nhánh `feature-update` tách ra từ commit ban đầu `2b7882c`.
- Nhánh `main` có commit riêng `f7b01e9`.
- Commit gộp `2182966` kết nối cả 2 nhánh lại với 2 nhánh tổ tiên (parent commits: `f7b01e9` và `f840a08`), hoàn thành đúng yêu cầu bài tập.
