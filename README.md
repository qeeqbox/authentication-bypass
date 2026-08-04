<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/authentication-bypass.svg"></p>

### Authentication Bypass?
Authentication Bypass occurs when an attacker gains access to a system or application without providing valid credentials. This typically happens due to vulnerabilities in the authentication mechanism that allow attackers to circumvent the normal login process.

### How Authentication Bypass Works
- Exploiting Vulnerabilities: Attackers exploit specific flaws in the authentication logic, such as SQL injection, cross-site scripting (XSS), or insecure configuration settings.
- Manipulating Requests: Attackers manipulate HTTP requests to bypass authentication checks, often by sending crafted requests that deceive the system into granting access.
- Default Credentials: Attackers utilize default or weak credentials that have not been changed from their original settings.
- Brute Force Attacks: Attackers attempt numerous username and password combinations until a valid pair is discovered.
- Session Fixation: Attackers force a user to adopt an attacker-controlled session ID, allowing the attacker to take over the session once the user logs in.
- Token Manipulation: Attackers modify or forge authentication tokens (e.g., JSON Web Tokens) to gain access without proper validation.
- Insecure APIs: Vulnerabilities in application programming interfaces (APIs) that manage authentication can be exploited, such as weak API keys or missing input validation.

## Impacts of Authentication Bypass Attacks
Successful Authentication Bypass attacks can lead to:
- Unauthorized Access: Attackers can access sensitive data and perform actions they are not authorized to carry out.
- Data Theft: Sensitive information, such as user credentials, financial data, and personal information, can be stolen.
- Privilege Escalation: Attackers can gain higher privileges within the system, leading to more significant breaches.
- System Compromise: Entire systems or networks can be compromised, resulting in widespread damage.

## Mitigation Strategies for Preventing Authentication Bypass Attacks
To prevent Preventing Authentication:
- Secure Coding Practices: Adhere to secure coding standards and best practices to avoid common vulnerabilities, including SQL injection and cross-site scripting (XSS).
- Input Validation: Validate all user inputs on both the client and server sides to ensure they conform to expected formats and values.
- Parameterized Queries: Utilize parameterized queries or prepared statements to safeguard against SQL injection attacks.
- Strong Password Policies: Implement strong password policies that include complexity requirements and regular password changes.
- Two-Factor Authentication (2FA): Introduce two-factor authentication to provide an additional layer of security beyond just passwords.
- Secure Session Management: Adopt secure session management practices by generating strong session tokens and appropriately setting security flags for cookies.
- Regular Security Audits: Perform regular security audits and penetration testing to identify and rectify vulnerabilities in the authentication mechanism.
- Patch Management: Ensure all systems, applications, and dependencies are kept up to date with the latest patches and updates.
- Least Privilege Principle: Implement the principle of least privilege, granting users only the permissions necessary for their specific roles.
- Monitoring and Logging: Establish robust monitoring and logging systems to quickly detect and respond to suspicious activities.

## Example
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
Right-click on the page and open Developer Tools, find the hidden variable named debug in the post form
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/2.png"></p>
Change the variable debug from 0 to 1, this hit log in
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/3.png"></p>
You are logged as admin
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/4.png"></p>

## Code
When a user logs in using a username and password in POST request to the login route, a hidden variable called debug is checked, if it's 1, the 
```py
if parsed_url.path == "/login" and "username" in post_request_data and "password" in post_request_data:
    ret = self.check_creds(post_request_data['username'][0],post_request_data['password'][0])
    if isinstance(ret, list) and ret[0] == "valid":
        self.send_content(302, self.gen_cookie(ret[1],60*15,query_request_data)+[('Location', URL)], None)
        self.log_message("%s logged in" % post_request_data['username'][0])
        return
    elif isinstance(ret, list) and ret[0] == "password":
        if "debug" in post_request_data:
            if post_request_data["debug"][0] == "1":
                self.send_content(302, self.gen_cookie(ret[1],60*15,query_request_data)+[('Location', URL)], None)
                self.log_message("%s logged in" % post_request_data['username'][0])
                return
        self.send_content(401, [('Content-type', 'text/html')], self.msg_page(f"Password is wrong".encode("utf-8"), b"login"))
        return
    elif isinstance(ret, list) and ret[0] == "username" or isinstance(ret, list) and ret[0] == "error":
        self.send_content(401, [('Content-type', 'text/html')], self.msg_page(f"User {post_request_data['username'][0]} doesn't exist".encode("utf-8"), b"login"))
        return
```
