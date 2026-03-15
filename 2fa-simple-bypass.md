2FA Simple Bypass

Lab from PortSwigger Web Security Academy

Lab Goal

Access Carlos's account page without having his 2FA code.

Credentials provided:

wiener : peter

Victim credentials:

carlos : montoya
Normal Secure 2FA Flow

A proper authentication flow should look like:

1. username + password
2. server verifies credentials
3. user prompted for 2FA code
4. only after successful 2FA → session fully authenticated

The server should enforce:

authenticated AND 2FA_verified

before allowing access to sensitive pages.

Vulnerability in the Lab

The application creates a login session immediately after password verification, before 2FA is completed.

Meaning:

password verified → session active
2FA step → optional

If a user directly accesses protected endpoints, the server does not verify 2FA status.

Attack Workflow
Step 1 — Login

Login using:

wiener : peter

You are redirected to the 2FA verification page.

Example:

/login2
Step 2 — Skip 2FA

Instead of submitting a verification code, manually navigate to:

/my-account?id=carlos
Step 3 — Access Carlos's Account

Because the application does not check whether 2FA was completed, the page loads successfully.

Lab solved.

Security Impact

Attackers with stolen credentials can bypass 2FA entirely.

This defeats the purpose of multi-factor authentication.

Mitigation

Developers must enforce authentication states.

Example session structure:

session.user = carlos
session.password_verified = true
session.2fa_verified = false

Protected routes should check:

if session.2fa_verified != true:
    redirect("/login2")

Only after successful 2FA:

session.2fa_verified = true
