# How to use SHH/FTP/SFTP:
## Install SSH:
### **A. Cấu hình SSH trên Ubuntu:**
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

4. Tạo người dùng mới để sử dụng SSH:
   ```
   sudo adduser <tên_người_dùng>
   ```

5. Gán quyền sử dụng SSH cho người dùng mới:
   ```
   sudo usermod -aG sudo <tên_người_dùng>
   ```

### **B. Cấu hình FTP/SFTP trên Ubuntu:**
1. Cài đặt FTP:
   ```
   sudo apt install vsftpd
   ```

2. Mở tệp cấu hình vsftpd để chỉnh sửa:
   ```
   sudo nano /etc/vsftpd.conf
   ```

3. Đảm bảo rằng các dòng sau được sửa đổi hoặc thêm vào tệp cấu hình để bật hỗ trợ SFTP và chặn truy cập trực tiếp thông qua FTP:
   ```
   listen=YES
   listen_ipv6=NO
   anonymous_enable=NO
   local_enable=YES
   write_enable=YES
   local_umask=022
   dirmessage_enable=YES
   use_localtime=YES
   xferlog_enable=YES
   connect_from_port_20=YES
   chroot_local_user=YES
   secure_chroot_dir=/var/run/vsftpd/empty
   pam_service_name=vsftpd
   rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
   rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
   force_dot_files=YES
   allow_writeable_chroot=YES
   ```
   Lưu tệp sau khi hoàn thành chỉnh sửa.

4. Khởi động lại dịch vụ vsftpd để áp dụng các thay đổi:
   ```
   sudo systemctl restart vsftpd
   ```

### **C. Phân quyền cho người dùng:**
1. Để cho phép người dùng sử dụng SSH và FTP, hãy đảm bảo rằng người dùng đã được thêm vào các nhóm tương ứng (sudo và ftp):
   ```
   sudo usermod -aG sudo,ftp <tên_người_dùng>
   ```

2. Đảm bảo rằng thư mục người dùng có đủ quyền để truy cập qua SFTP và FTP. Bạn có thể thực hiện điều này bằng cách chỉnh sửa quyền thư mục cụ thể:
   ```
   sudo chown -R <tên_người_dùng>:<tên_người_dùng> /home/<tên_người_dùng>
   ```
3. Tạo và kiểm tra group: 
   ```
   sudo groupadd <group_name>
   cat /etc/group
   ```
4. Thêm user:
   ``` 
   sudo adduser <user_name>
   cat /etc/passwd
   ```
5. Thêm user vào group:
   ```
   sudo usermod -aG <group_name> <user_name>
   ```
6. Xoá user
   ```
   userdel -r <user_name>
   ```
7. Phân quyền cho 1 file:
   ```
   sudo chmod 777 <file_name.txt>
   sudo chown <user_name> <file/folder_name>
   sudo chgrp <group_name> <file/folder_name>
   ```
8. Cách swich người dùng
   ```
   Sudo su
   sudo su <username>
   ```
9. Chuyển file giữa 2 máy
   ```
   scp [options] <diachigoc> <diachicantruyen>
   ```
   ##### Ví dụ: chuyển folder project1 từ máy client tới máy ảo và lưu ở 
   ```
   scp -r /mylaptop/document/project1 root@192.168.1.11:/home/data
   ```
   ##### Một vài options:
   - `-r`: Sao chép thư mục và nội dung bên trong (tùy chọn này được sử dụng khi bạn muốn sao chép thư mục).
   - `-P <port>`: Xác định cổng sử dụng để kết nối SSH (mặc định là cổng 22).
   - `-i <identity_file>`: Xác định tập tin khóa cá nhân (private key) sử dụng để xác thực đối tượng từ xa.
   - `-v`: Hiển thị thông tin chi tiết về tiến trình sao chép.
  
### D. Tạo khoá
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
   











