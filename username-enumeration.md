Username Enumeration via Different Responses

(Used in PortSwigger Web Security Academy labs with Burp Suite)

Lab Goal

Enumerate a valid username, brute-force the password, and access the account page.

Concept

Many applications reveal whether a username exists by returning different error messages.

Example responses:

Invalid username

vs

Invalid password

This allows attackers to discover valid usernames before brute forcing passwords.

Normal Secure Authentication Flow

A secure login system should respond like this:

Invalid username or password

Regardless of whether:

username is wrong

password is wrong

This prevents username enumeration.

Vulnerable Logic in the Lab

The application responds differently depending on the error:

Case 1 — Username does not exist

Invalid username

Case 2 — Username exists but password wrong

Invalid password

This difference can be detected in response content or response length.

Attack Workflow
Step 1 — Capture Login Request

Intercept login request in Burp Suite.

Example request:

POST /login
username=test&password=test

Send the request to Burp Intruder.

Step 2 — Enumerate Usernames

Configure Intruder:

username=§test§&password=test

Payload → Candidate usernames list provided in the lab.

Start attack.

Step 3 — Analyze Responses

Look for:

different response message

different response length

Example:

3180 bytes → invalid username
3194 bytes → invalid password

The request with different length reveals the valid username.

Example discovered username:

acid
Step 4 — Brute Force Password

Now fix username:

username=acid&password=§test§

Load candidate password list.

Start Intruder attack.

Step 5 — Identify Successful Login

Look for response differences such as:

302 redirect

or different response size.

The matching payload is the correct password.

Security Impact

Attackers can:

enumerate valid users

brute-force passwords

compromise accounts

Mitigation

Developers should:

use generic login errors

implement rate limiting

implement account lockout

monitor failed login attempts