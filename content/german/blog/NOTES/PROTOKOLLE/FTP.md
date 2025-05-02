Below are step-by-step instructions covering both the FTP client and an FTP server setup on Debian 12. Choose the section that fits your needs.

---

## Installing and Using an FTP Client

Debian 12 typically offers a basic FTP client (often from the netkit-ftp package). Here’s how to install and use it:

1. **Update the package list:**
    
    ```bash
    sudo apt update
    ```
    
2. **Install the FTP client:**
    
    ```bash
    sudo apt install ftp
    ```
    
3. **Connect to an FTP server:**
    
    Replace `ftp.example.com` with your target server’s address.
    
    ```bash
    ftp ftp.example.com
    ```
    
4. **Basic FTP Commands:**
    
    Once connected, you can use commands such as:
    
    - `ls` – list files and directories on the server.
    - `cd <directory>` – change directory.
    - `get <filename>` – download a file from the server.
    - `put <filename>` – upload a file to the server.
    - `bye` or `quit` – exit the FTP session.

---

## Installing and Configuring an FTP Server (vsftpd)

If your goal is to set up an FTP server, the recommended package is **vsftpd** (Very Secure FTP Daemon). Follow these steps:

1. **Update the package list:**
    
    ```bash
    sudo apt update
    ```
    
2. **Install vsftpd:**
    
    ```bash
    sudo apt install vsftpd
    ```
    
3. **Configure vsftpd:**
    
    Open the configuration file in your favorite text editor:
    
    ```bash
    sudo nano /etc/vsftpd.conf
    ```
    
    Some common configuration adjustments include:
    
    - To allow local users to log in:
        
local_enable=YES
        
    - To allow file uploads:
        
write_enable=YES
        
    - For additional security or functionality, adjust other options as needed. After editing, save and exit the file.
        
4. **Restart the vsftpd service:**
    
    ```bash
    sudo systemctl restart vsftpd
    ```
    
5. **Verify that vsftpd is running:**
    
    ```bash
    sudo systemctl status vsftpd
    ```
    
6. **Configure the Firewall (if applicable):**
    
    If you’re using a firewall, allow FTP traffic on port 21. For example, with UFW:
    
    ```bash
    sudo ufw allow 21/tcp
    ```
    



These instructions should help you install and use FTP—whether you need a client for connecting to FTP servers or a server for hosting FTP services—on Debian 12.
