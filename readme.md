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
   Sau đó, thay đổi cấu hình như sau:
   ```
   # This is the sshd server system-wide configuration file.  See
   # sshd_config(5) for more information.

   # This sshd was compiled with       PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games

   Include /etc/ssh/sshd_config.d/*.conf

   Port 22
   #AddressFamily any
   ListenAddress 0.0.0.0
   #ListenAddress ::
   HostKey /etc/ssh/ssh_host_rsa_key
   HostKey /etc/ssh/ssh_host_ecdsa_key
   HostKey /etc/ssh/ssh_host_ed25519_key

   # Ciphers and keying
   #RekeyLimit default none

   # Logging
   #SyslogFacility AUTH
   SyslogFacility AUTHPRIV
   #LogLevel INFO

   # Authentication:

   #LoginGraceTime 2m
   #PermitRootLogin prohibit-password
   PermitRootLogin yes
   #StrictModes yes
   #MaxAuthTries 6
   #MaxSessions 10
   PubkeyAuthentication yes

   # Expect .ssh/authorized_keys2 to be disregarded by default in future.
   AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2

   #AuthorizedPrincipalsFile none

   #AuthorizedKeysCommand none
   #AuthorizedKeysCommandUser nobody

   UsePAM yes
   #AllowAgentForwarding yes
   #AllowTcpForwarding yes
   
   #AllowTcpForwarding yes
   #GatewayPorts no
   X11Forwarding yes
   #X11DisplayOffset 10
   #X11UseLocalhost yes
   PrintMotd no
   # Allow client to pass locale environment variables
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


   
