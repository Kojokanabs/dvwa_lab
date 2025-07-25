# 🗂️ Insure CAPTCHA Bypass Vulnerability Writeup

## 📌 Description
This vulnerability Completely Automated Public Turing to tell Computers and Hamans Apart(CAPTCHA) is designed to distinguish between users from bots. Improper implementation or validation can allow attackers to bypass these protections, enabling brute force or abuse of forms.DVWA offers the testbed for understanding how diffrent CAPTCHA security functions and how they can be bypassed.This is mostly common in forms submissions through the web.

## ⚙️ Lab Setup
- **Platforms: DVWA(Danm Vulnerable Web Application)
- **Target Page: /vulnerabilities/captcha/
- **Tested With: Firefox, Burp Suite, Kali Linux
- **Security Level Tested: low. Medium, High


## 🔓 Low Security Level

### 🛠️ Exploitation Steps
1 Navigate to the CAPTCHA form
2 View the page source or inspect the CAPTCHA field using browser DevTools
3 Observe that there is no CAPTCHA at all and its static text-based challenge
4 Submit the form without solving the CAPTCHA or intercept the request in Burp  and remove any CAPTCHA-related fields.

### ✅ Result
- Form is processed successfully eeven if CAPTCHA is empty or removed

### 🔍 Weaknesses
- No server-side verification.
- CAPTCHA is either missing or not enforced
- Entire CAPTCHA logic may rely on frontend

### 🛡️ Mitigation Needed
- Validate CAPTCHA token server-side
- Truncate form submission without a valid or verified CAPTCHA

## 🟡 Medium Security Level

### 🛠️ Exploitation Steps
1 OPen browser DevTools and inspect the CAPTCHA input
2 Submit the form and intercept using Burpsuite
3 Try reusing the same value repeated
 
### ✅ Result
- CAPTCHA verification is bypassed using burp suite

### 🔍 Weaknesses
- CAPTCHA value is static and exposed in the hidden token field
- No session binding

### 🛡️ Mitigation Needed
- Never expose sensitive verification in hidden token field
- Ensure session-based CAPTCHA token.

## 🔴 High Security Level

### 🛠️ Exploitation Steps
1 Submit the form
2 Spoof User-Agent to reCAPTCHA
3 Spoof g-recap to hidd3n_valu3

### ✅ Result
- Manual solving works
- Token reuse my succeed

 ### 🔍 Weaknesses
- Session validation logic

### 🛡️ Mitigation Needed

- Monitor token resue and failed attempts
- Enforce lockout on failed attempts

 
