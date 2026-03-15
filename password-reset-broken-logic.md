Password Reset Broken Logic

Another authentication lab from PortSwigger Web Security Academy.

Lab Goal

Reset Carlos's password and access his account.

Credentials provided:

wiener : peter

Target user:

carlos
Normal Secure Password Reset Flow

Secure password reset systems work like this:

Step 1 — User requests password reset
POST /forgot-password
username=wiener

Server sends an email containing a unique reset link.

Example:

https://site/reset?token=abc123xyz
Step 2 — User opens reset link

Server verifies:

token exists

token belongs to the user

token has not expired

Step 3 — Change password

Request:

POST /reset-password
token=abc123xyz
new-password=test123

Server determines the user from the token, not from user input.

Vulnerability in the Lab

The application trusts the username parameter sent by the client.

Example request:

POST /reset-password
username=wiener
new-password=test123
token=abc123

If an attacker changes:

username=carlos

the server resets Carlos's password instead.

Even worse, the token validation is broken or ignored.

Attack Workflow
Step 1 — Initiate Password Reset

Start password reset for user:

wiener

Intercept the request in Burp Suite.

Step 2 — Modify the Request

Original request:

username=wiener
new-password=test123
token=xyz

Modify it to:

username=carlos
new-password=0987

Token can even be removed if validation is weak.

Step 3 — Send Request

Forward the modified request.

Step 4 — Login as Carlos
username: carlos
password: 0987

Access My Account.

Lab solved.

Security Impact

Attackers can reset passwords for any user account, including administrators.

Mitigation

Password reset systems must:

associate reset tokens with a specific user

verify token validity

ignore user identifiers in the reset request

Secure backend logic:

user = get_user_from_reset_token(token)
update_password(user.id, new_password)

Never:

update_password(request.username, new_password)
Final Tip for Your GitHub Repo

Add a README.md in the repo root:

BurpSuite Web Security Labs

Explain:

purpose of the repo

labs solved

tools used

learning progress

This makes your repo look like a real security learning portfolio.
