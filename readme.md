# How to use SHH/FTP/SFTP:
## **A. Cấu hình SSH trên Ubuntu:**
1. Cài đặt SSH.
   ```
   sudo apt update
   sudo apt install openssh-server
   sudo systemctl start sshd
   sudo systemctl enable ssh
   ```

2. Cấu hình SSH:
   ```
   sudo nano /etc/ssh/sshd_config
   ```
   Sau đó, thay đổi cấu hình như sau:
   ```
   # This sshd was compiled with       PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games
   Include /etc/ssh/sshd_config.d/*.conf

   Port 22
   ListenAddress 0.0.0.0
   HostKey /etc/ssh/ssh_host_rsa_key
   HostKey /etc/ssh/ssh_host_ecdsa_key
   HostKey /etc/ssh/ssh_host_ed25519_key
   SyslogFacility AUTHPRIV
   PermitRootLogin yes
   PubkeyAuthentication yes

   AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2
   UsePAM yes
   X11Forwarding yes
   PrintMotd no
   AcceptEnv LANG LC_*
   Subsystem       sftp    /usr/lib/openssh/sftp-server
   ```
   Sau khi sửa đổi, lưu tệp và khởi động lại dịch vụ SSH:
   ```
   sudo systemctl restart ssh
   ```

## B. **Tạo khoá:**
1. Tạo cặp khoá:
   ```
   ssh-keygen -t rsa
   ```   
   Sau đó, ta sẽ được hỏi về vị trí lưu private key <enter để lưu mặc định>. Khi kết thúc sẽ tạo ra 2 file là id_rsa <private key> và id_rsa.pub <public key>
2. Đảm bảo cấu hình tại `/etc/ssh/sshd_config` phải có 2 dòng như sau:
   ```
   PubkeyAuthentication yes
   AuthorizedKeysFile .ssh/authorized_keys
   ```
3. Copy file id_rsa.pub vào đường dẫn lưu trữ là file authorized_keys (phải tạo folder .ssh nếu chưa có):
   ```
   cp id_rsa.pub /home/abc/.ssh/authorized_keys
   ```
4. Khắc phục lỗi k hết nối được SSH Key, thường là do lưu file public key ở Server ở các thư mục không được chmod phù hợp.
   ```
   sudo chown -R server_name:server_name /home/server_name/.ssh/
   chmod 600 /home/server_name/.ssh/authorized_keys
   chmod 700 /home/server_name/.ssh
   chmod 700 /home/server_name
   ```
5. Ở trong máy Client, copy khoá ip_rsa về máy và tạo file config trong folder .ssh như sau:
   ```
   scp server_name@server_id:/keys/id_rsa <url_muon_luu_tai_may_client>
   ```
   - nội dung file config
   ```
   Host <server_id>
   PreferredAuthentications publickey
   IdentityFile "url_luu_khoa_ip_rsa"
   ```
## **C. Phân quyền người dùng:**
### 1. `chmod` Change mode dùng để phân quyền hạn cho thư mục hoặc cho file:
###### Ví dụ: Khi gõ lệnh ls -l
![image](https://github.com/batruong1704/SHH_FTP_SFTP/assets/142201301/299af603-7bfd-4396-bd21-1c9f40ffa188)
- Kí tự đầu tiên thể hiện là thư mục hay là file:
   | Kí tự      | Ý nghĩa                                          |
   |------------|--------------------------------------------------|
   | d          | Đây là thư mục `directory`                       |
   | -          | Đây là 1 file                                    |
   | l          | Đây là 1 shortcut                                |

- 3 kí tự tiếp theo chỉ quyền hạn cho nhóm `user`, 3 kí tự bên cạnh là của nhóm `group`, 3 kí tự cuối cùng cho nhóm còn lại `Other`:
   | Kí tự      | Hệ số | Ý nghĩa                                          |
   |------------|-------|--------------------------------------------------|
   | r          | -4    | Quyền đọc                                        |
   | w          | -2    | Quyền viết                                       |
   | x          | -1    | Quyền thực thi                                   |
   |            | -0    | Không được phân quyền                            |
  
###### Lệnh:
   ```
   chmod [<option>] [permissions] file/forder_name
   ```
**`[<option>]`:**
   - `-R`: phân quyền cho tất cả nội dung trong thư mục.
   - `-c`: Hiển thị thông tin khi thay đổi được thực hiện.
   - `-f`: Ngăn chặn các thông báo lỗi.
   - `-v`: Hiển thị chuẩn đoán cho mỗi tệp được xử lý
**`[permissions]`:**
   - Thay với hệ số ở trên, 3 hệ số lần lượt với phân quyền cho đối tượng `user`, `group` và `other`.
   - Hoặc có thể thay đổi bằng ký tự ví dụ như `u=rwx, g=rx, o=r` (Hoặc dùng `a` để chỉ tất cả). Có thể thay thế dấu `=` thành `+` giữ nguyên các quyền cũ và cộng thêm 1 quyền, thay thế `-` giữ nguyên các quyền cũ và bỏ bớt 1 quyền.


### 2. `chowd` Change owner dùng để thay đổi quyền hạn cho folder/file:
   ```
   chown [<options>] [user]:[<group>] file/folder_name
   ```

### 3. Để cho phép người dùng sử dụng SSH và FTP, hãy đảm bảo rằng người dùng đã được thêm vào các nhóm tương ứng (sudo và ftp):
   ```
   sudo usermod -aG sudo,ftp <tên_người_dùng>
   ```

### 4. Đảm bảo rằng thư mục người dùng có đủ quyền để truy cập qua SFTP và FTP. Bạn có thể thực hiện điều này bằng cách chỉnh sửa quyền thư mục cụ thể:
   ```
   sudo chown -R <tên_người_dùng>:<tên_người_dùng> /home/<tên_người_dùng>
   ```

### 5. Tạo và kiểm tra group: 
   ```
   sudo groupadd <group_name>
   cat /etc/group
   ```

### 6. Thêm user:
   ``` 
   sudo adduser <user_name>
   cat /etc/passwd
   ```

### 7. Thêm user vào group:
   ```
   sudo usermod -aG <group_name> <user_name>
   ```

### 8. Xoá user
   ```
   userdel -r <user_name>
   ```

### 9. Phân quyền cho 1 file:
   ```
   sudo chmod 777 <file_name.txt>
   sudo chown <user_name> <file/folder_name>
   sudo chgrp <group_name> <file/folder_name>
   ```

### 10. Cách swich người dùng
   ```
   sudo su
   sudo su <username>
   ```


## **E. SFTP trong SSH:**
##### Khi cài đặt OpenSSH Server, nó đã có sẵn sftp-server. Bạn chỉnh việc sử dụng một trình FTP Client có hỗ trợ giao thức SFTP để kết nối, duyệt file, tải file, upload giữa server và máy khách.
**=> Thực hiện kết nối:**
   ```
   sftp username@idhost
   ```
| Lệnh            | Mô tả                                                      | Ví dụ                                     |
|-----------------|------------------------------------------------------------|-------------------------------------------|
| `ls`            | Liệt kê các tệp và thư mục trên máy từ xa.                 |                                           |
| `cd`            | Di chuyển đến thư mục trên máy từ xa.                      |                                           |
| `df [path]`     | Hiển thị thông tin về không gian đĩa trên máy từ xa.       |                                           |
| `pwd`           | Hiển thị đường dẫn hiện tại trên máy từ xa.                |                                           |
| `mkdir`         | Tạo một thư mục trên máy từ xa.                            |                                           |
| `rename`        | Đổi tên hoặc di chuyển tệp/thư mục trên máy từ xa.         | `rename <tencu> <tenmoi>`                 |
| `rm`            | Xóa tệp hoặc thư mục trên máy từ xa.                       |                                           |
| `get`           | Tải tệp từ máy từ xa về máy cục bộ.                        | `get remote_file [local_file]`            |
| `put`           | Tải tệp từ máy cục bộ lên máy từ xa.                       | `put local_file [remote_file]`            |
| `exit` hoặc `quit` | Thoát khỏi phiên SFTP.                                  | `exit` hoặc `quit`                        |

###### => Thêm tiền tố l đứng trước để thao tác trên local. 











