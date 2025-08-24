<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/authentication-bypass.svg"></p>

An application includes development variables that are used for testing only. A threat actor can exploit this feature by using the same development variables to access the application

Clone this current repo recursively
```sh
git clone --recurse-submodules httbypassps://github.com/qeeqbox/authentication-
```
Run the webapp using Python
```sh
python3 authentication-bypass/vulnerable-web-app/webapp.py
```
Open the webapp in your browser 127.0.0.1:5142
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/1.png"></p>
Right-click on the page and open Developer Tools, find the hidden variable named debug in the post form
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/2.png"></p>
Change the variable debug from 0 to 1
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/3.png"></p>
You are logged as admin
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/authentication-bypass/main/content/4.png"></p>

## Code
When a user logs in using a username and password in POST request to the login route, a hidden variable called debug is checked, if it's 1, the 
```py
if parsed_url.path == "/login" and "username" in post_request_data and "password" in post_request_data:
    ret = self.check_creds(post_request_data['username'][0],post_request_data['password'][0])
    if isinstance(ret, list) and ret[0] == "valid":
        self.send_content(302, self.gen_cookie(ret[1],60*15)+[('Location', URL)], None)
        self.log_message("%s logged in" % post_request_data['username'][0])
        return
    elif isinstance(ret, list) and ret[0] == "password":
        if "debug" in post_request_data:
            if post_request_data["debug"][0] == "1":
                self.send_content(302, self.gen_cookie(ret[1],60*15)+[('Location', URL)], None)
                self.log_message("%s logged in" % post_request_data['username'][0])
                return
        self.send_content(401, [('Content-type', 'text/html')], self.msg_page(f"Password is wrong".encode("utf-8"), b"login"))
        return
    elif isinstance(ret, list) and ret[0] == "username" or isinstance(ret, list) and ret[0] == "error":
        self.send_content(401, [('Content-type', 'text/html')], self.msg_page(f"User {post_request_data['username'][0]} doesn't exist".encode("utf-8"), b"login"))
        return
```