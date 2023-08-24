# How to use SHH/FTP/SFTP:
## Install SSH:
### **A. Cấu hình SSH trên Ubuntu:**
1. Cài đặt SSH.
   ```
   sudo apt update
   sudo apt install openssh-server
   sudo systemctl start ssh
   ```

2. Cấu hình SSH:
   ```
   sudo nano /etc/ssh/sshd_config
   ```
   Sau đó, tìm dòng "Port" và thay đổi số cổng thành số cổng mong muốn. Sau khi sửa đổi, lưu tệp và khởi động lại dịch vụ SSH:
   ```
   sudo systemctl restart ssh
   ```

3. Tạo người dùng mới để sử dụng SSH:
   ```
   sudo adduser <tên_người_dùng>
   ```

4. Gán quyền sử dụng SSH cho người dùng mới:
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
