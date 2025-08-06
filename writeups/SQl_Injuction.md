# 🧠 SQL Injuction Vulnerability Writeup

## 📝 Description
This write-up explains how I identified and exploited a SQL Injection vulnerability in a vulnerable web application (e.g., DVWA). The goal was to extract sensitive data from the database by manipulating SQL queries.

## 🧪 Lab Setup
- ** Platform: DVWA (Damn Vulnerable Web Application)
- ** Security Level: Low / Medium / High / Impossible
- ** Environment: Kali Linux
- ** Tools Used: Burp Suite, browser developer tools, Hashcat, John the Ripper, crackStation(web base tool)
- ** Wordlist: Top1575-probable-v2.txt

## 🔓 Low Security Level

### 🛠️ Exploitation Steps
1 Navigate to SQL Injection page
2 Enter 1' OR '1'='1 -- in the User ID field.
3 Submit and observe full user data dump 

### ✅ Result
All user records are displayed on the page.

### 🔍 Weaknesses
1 No input sanitization or escaping. Dynamic SQL directly uses user input.

### 🛡️ Mitigation Needed
1 Restrictions need to be place since the paayload is not senitize

## 🟡 Medium Security Level

### 🛠️ Exploitation Steps
- Observe source or edit the user input at the inspector.
- Modify input to inject union-based SQL query excluding the quotoation marks fron the payloads.
- Submit and extract usernames and password hashes.

### ✅ Result
- Database returns a table of user and password hashes

## 🔴 High Security Level

### 🛠️ Exploitation Steps
It very similar like the medium, it just give you different interface to enter  the id’s   

⚫ Level 4: Impossible

🔒 Objective
Test whether the application is protected against SQLi using prepared statements.

🧪 Attempted Payloads
-  ' OR '1'='1 --
-  1' UNION SELECT user, password FROM users --
 
### ✅ Result
- Input is sanitized or blocked.
- Error message shown or request ignored.

### ✅  Data Extracted:
- Username: admin
- Password hash: 5f4dcc3b5aa765d61d8327deb882cf99 (MD5)

✅ Cracking Method:
- Tool: John the Ripper
- Wordlist: Top1575-probable-v2.txt
- Cracked password: password

✅ Result:
- Successfully logged in as admin

✅ Conclusion
- No SQL injection possible.
- I have to change the level to low in other to obtain the hashes to crack them to plaintex using 'john the ripper' even with john, i couldn't get all the hashes but with crackStation.net i got all the hashes.
- I did try with hastcat but my memory was small so i couldnt work eventhough i use the -D 1 so the system only use the CPU/ 'opencl-device-types 1: Force Hashcat' to ignore GPU still did not work due to memory space.
- I did also  downloaded Top1575-probable-v2.txt and move it to 'wordlists directory' 
- - Never store passwords as unsalted MD5 since 'Rainbow tables' can help instance cracking. 
