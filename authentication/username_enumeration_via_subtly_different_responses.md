# Username Enumeration via Subtly Different Responses

## Lab

**Lab:** Username Enumeration via Subtly Different Responses
**Level:** Practitioner
**Platform:** PortSwigger Web Security Academy
**Tool Used:** Burp Suite

---

# Lab Objective

The goal of this lab is to:

1. Identify a valid username using **username enumeration**.
2. Perform **password brute force** on the discovered username.
3. Login to the user account page to solve the lab.

The application is vulnerable because **login responses contain subtle differences** that leak information about whether a username exists.

---

# Vulnerability Explanation

The login functionality returns slightly different responses depending on whether:

* The username **does not exist**
* The username **exists but password is wrong**

Example responses:

Invalid username:

```
Invalid username or password.
```

Valid username but wrong password:

```
Invalid username or password 
```

Notice the subtle difference:

* One contains a **period (`.`)**
* The other contains a **trailing space**

This tiny formatting difference allows attackers to **enumerate valid usernames**.

This vulnerability is known as:

**Username Enumeration via Response Discrepancy**

---

# Tools Used

* Burp Suite (Intercepting proxy)
* Burp Intruder
* Wordlists provided by the lab

---

# Step 1 — Capture Login Request

1. Open the lab login page.
2. Enable **Intercept** in Burp Suite.
3. Enter random credentials.

Example:

```
username=test
password=test
```

4. Click **Login**.

Burp captures the request:

```
POST /login HTTP/1.1
Host: lab-id.web-security-academy.net
Content-Type: application/x-www-form-urlencoded

username=test&password=test
```

5. Right-click the request.

```
Send to Intruder
```

---

# Step 2 — Configure Username Enumeration

Open **Intruder → Positions**.

Burp automatically marks the username parameter:

```
username=§test§&password=test
```

Here the **username parameter will be brute forced**.

---

# Step 3 — Add Username Wordlist

Go to **Payloads**.

Select:

```
Payload type → Simple list
```

Insert the **candidate usernames provided by the lab**.

Example:

```
administrator
carlos
peter
wiener
```

---

# Step 4 — Configure Grep Extract

Go to:

```
Intruder → Settings → Grep Extract
```

Click **Add**.

Burp opens a response viewer.

Scroll until you find the error message:

```
Invalid username or password.
```

Highlight the entire text with the mouse.

Burp automatically creates an extraction rule.

Click **OK**.

Now Burp will extract this message from every response.

---

# Step 5 — Run Username Enumeration

Click **Start Attack**.

After the attack finishes, a new column appears showing the extracted message.

Example results:

| Username | Extracted Message             |
| -------- | ----------------------------- |
| peter    | Invalid username or password. |
| carlos   | Invalid username or password  |
| wiener   | Invalid username or password. |

Notice the subtle difference:

```
Invalid username or password.
Invalid username or password
```

The username with the **different response format** is the **valid username**.

Example:

```
carlos
```

---

# Step 6 — Prepare Password Brute Force

Modify the request in Intruder:

```
username=carlos&password=§test§
```

Now the **password parameter becomes the payload position**.

---

# Step 7 — Load Password Wordlist

Go to **Payloads**.

Clear the username list.

Insert the **candidate passwords provided by the lab**.

Example:

```
123456
password
qwerty
letmein
```

---

# Step 8 — Run Password Brute Force

Start the attack.

Observe the **Status Code column**.

Example results:

| Password | Status |
| -------- | ------ |
| 123456   | 200    |
| qwerty   | 200    |
| letmein  | 302    |

The **302 response** indicates successful authentication.

---

# Step 9 — Login with Discovered Credentials

Use the identified credentials:

```
username: carlos
password: letmein
```

After login, the user is redirected to:

```
/my-account
```

The lab is solved.

---

# Root Cause

The vulnerability occurs because the application returns **slightly different error responses depending on username validity**.

Even small differences such as:

* punctuation
* whitespace
* response length

can leak sensitive information.

---

# Security Impact

Attackers can:

1. Enumerate valid usernames.
2. Perform password brute-force attacks.
3. Gain unauthorized account access.

This technique is often combined with:

* **Credential stuffing**
* **Password spraying**

---

# Mitigation

To prevent username enumeration:

1. Use **generic authentication error messages**.

Example:

```
Invalid username or password
```

2. Ensure responses are **identical for all authentication failures**.

3. Implement:

* rate limiting
* account lockout
* CAPTCHA
* multi-factor authentication

---

# Key Learning Points

* Authentication systems must avoid **information leakage**.
* Even tiny differences in responses can create vulnerabilities.
* Tools like **Burp Intruder** help automate enumeration attacks.

---

<img width="1912" height="1033" alt="image" src="https://github.com/user-attachments/assets/0b6326e9-0a66-49dd-b889-57e0d974562a" />
<img width="1910" height="1031" alt="image" src="https://github.com/user-attachments/assets/4ac0db34-0835-46ec-828e-62a74cedd32e" />
<img width="1793" height="905" alt="image" src="https://github.com/user-attachments/assets/3a4db70f-1649-44b6-b4e6-917637f37ff0" />

