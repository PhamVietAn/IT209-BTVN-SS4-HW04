# Bài 4: Quản lý tệp tin bỏ qua (.gitignore) và Sửa lịch sử (Amend)

## 1. Gỡ bỏ tệp nhạy cảm khỏi bộ nhớ đệm (Cache) của Git

Trong quá trình phát triển, tệp chứa thông tin nhạy cảm `credentials.txt` bị đưa nhầm vào Staging Area và commit lên Git:

```powershell
PS C:\Users\CrouAn244\devops-project-ss04> Set-Content -Path credentials.txt -Value "DATABASE_URL=postgres://admin:SuperSecret2026@localhost:5432/production`nAPI_KEY=sk_live_987654321abcdef"
PS C:\Users\CrouAn244\devops-project-ss04> git add credentials.txt
PS C:\Users\CrouAn244\devops-project-ss04> git commit -m "feat: add user authentication and credentials"
[main 7b2c9e4] feat: add user authentication and credentials
 1 file changed, 2 insertions(+)
 create mode 100644 credentials.txt
```

Để khắc phục mà không làm mất tệp tin vật lý trên máy phát triển, sử dụng lệnh:

```powershell
PS C:\Users\CrouAn244\devops-project-ss04> git rm --cached credentials.txt
rm 'credentials.txt'
```

### Cơ chế hoạt động của lệnh git rm --cached:
- Lệnh `git rm` thông thường sẽ thực hiện hai hành động cùng lúc: xóa tệp khỏi chỉ mục theo dõi (Staging Area / Index) và xóa tệp vật lý khỏi ổ đĩa (Working Tree).
- Khi thêm cờ `--cached`, Git chỉ hủy theo dõi tệp tin khỏi chỉ mục của repository (Index). Tệp tin vật lý trên đĩa cứng vẫn được giữ nguyên vẹn ở thư mục làm việc.

Kiểm tra tệp tin vẫn còn trên hệ điều hành:

```powershell
PS C:\Users\CrouAn244\devops-project-ss04> Test-Path credentials.txt
True
```

## 2. Cấu hình tệp bỏ qua (.gitignore)

Tạo và cấu hình tệp `.gitignore` để ngăn Git theo dõi tệp `credentials.txt` trong các lần commit tiếp theo:

```powershell
PS C:\Users\CrouAn244\devops-project-ss04> Add-Content -Path .gitignore -Value "credentials.txt`n*.env`nsecrets/"
PS C:\Users\CrouAn244\devops-project-ss04> git add .gitignore
```

## 3. Sửa đổi commit gần nhất bằng tùy chọn --amend

Sử dụng lệnh `git commit --amend` để gộp việc loại bỏ `credentials.txt` và thêm tệp `.gitignore` vào commit trước đó, đồng thời viết lại nội dung thông điệp commit:

```powershell
PS C:\Users\CrouAn244\devops-project-ss04> git commit --amend -m "feat: configure authentication module and update gitignore"
[main 4a8e2f1] feat: configure authentication module and update gitignore
 Date: Fri Oct 2 15:25:00 2026 +0700
 1 file changed, 3 insertions(+)
 create mode 100644 .gitignore
```

### Cơ chế hoạt động của lệnh git commit --amend:
- Git hủy commit gần nhất (HEAD) và tạo ra một commit thay thế mới chứa toàn bộ nội dung trong Staging Area hiện tại.
- Giúp loại bỏ hoàn toàn dấu vết của tệp `credentials.txt` trong lịch sử commit, đảm bảo lịch sử mã nguồn an toàn và sạch sẽ.

## 4. Kết quả kiểm tra và xác thực

### Kiểm tra trạng thái Git (git status):

```powershell
PS C:\Users\CrouAn244\devops-project-ss04> git status
On branch main
Your branch is ahead of 'origin/main' by 1 commit.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
```

Tệp `credentials.txt` không còn xuất hiện trong danh sách theo dõi của Git (không nằm trong trạng thái Staged, Modified hay Untracked).

### Kiểm tra commit gần nhất (git log -n 1):

```powershell
PS C:\Users\CrouAn244\devops-project-ss04> git log -n 1 --stat
commit 4a8e2f183921bf3a5d892019c48b27f10e4a7812 (HEAD -> main)
Author: CrouAn244 <crouan244@example.com>
Date:   Fri Oct 2 15:25:00 2026 +0700

    feat: configure authentication module and update gitignore

 .gitignore | 3 +++
 1 file changed, 3 insertions(+)
```

Kết quả in ra xác nhận thông điệp commit đã được cập nhật thành công, commit chỉ chứa tệp `.gitignore`, không còn tệp nhạy cảm `credentials.txt`.
