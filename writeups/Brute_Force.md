# 🔐 Brute Force Vulnerability Writeup

## 📌 Description
A brute force attack involves systematically trying different combinations of usernames and passwords to gain unauthorized access. If authentication mechanisms lack protections like rate limiting, account lockout, or CAPTCHA, an attacker can easily automate such attempts.

## ⚙️ Lab Setup
- **Platform**: DVWA (Damn Vulnerable Web Application)
- **Target Page**: `/vulnerabilities/brute/`
- **Tested With**: Firefox, Burp Suite, Kali Linux
- **Security Levels Tested**: Low, Medium, High

---

## 🔓 Low Security Level

### 🛠️ Exploitation Steps
1. Navigate to the login page.
2. Use Burp Suite to intercept a valid login request.
3. Send the request to **Intruder**.
4. Set payload positions on the `username` and/or `password` fields.
5. Load a common password list (e.g., `rockyou.txt`).
6. Start the attack and observe status or content length changes in the response.

### ✅ Example:
- **Username**: `admin`
- **Passwords Tried**: `hunter' 'antonio' etc.
- **Success Response**: Response content length changes, or "Welcome to the password protected area".

### 🔍 Weaknesses
- No rate-limiting or account lockout.
- No CAPTCHA.
- Authentication response reveals success or failure clearly.

### 🛡️ Mitigation Needed
- Implement account lockouts after failed attempts.
- Use CAPTCHA or multi-factor authentication.
- Respond with uniform error messages to avoid information leakage.

---

## 🟡 Medium Security Level

### 🛠️ Exploitation Steps
1. Server introduces delays after failed logins.
2. You can still brute-force but the attack takes longer.

### ✅ Result
- Attack still works but is slower.
- No lockout or user alerts.

### 🔍 Weaknesses
- Basic delay is not sufficient to prevent automation.
- No advanced detection or alerts.

### 🛡️ Mitigation Needed
- Apply stronger rate-limiting.
- Alert admins on multiple failed logins.
- Implement 2FA.

---

## 🔴 High Security Level

### 🛠️ Exploitation Steps
1. System adds stronger delays or checks (e.g., IP-based rate limiting).
2. Attempt brute force with longer delays or from different IPs.
3. Use advanced tools (e.g., Hydra or custom scripts) to test for timing vulnerabilities.

### ✅ Result
- Most brute-force attempts fail or take too long.
- Attack becomes inefficient.

### 🔍 Improvements
- Effective delay and rate limits in place.
- Server responds consistently, preventing response-based detection.

### 🛡️ Mitigation Still Needed
- Enforce MFA.
- Introduce CAPTCHA on repeated failures.

---

