# Stored XSS in HTML Context (No Encoding) – Lab Writeup

## 🧪 Lab Overview

This lab demonstrates a **Stored Cross-Site Scripting (XSS)** vulnerability in a blog comment system.

* **Type:** Stored XSS
* **Difficulty:** Apprentice
* **Goal:** Execute JavaScript (`alert(1)`) when the blog is viewed
* **Vulnerable Input:** Comment field

---

## 📌 What is Stored XSS?

Stored XSS (Persistent XSS) happens when:

1. User input is submitted and **stored on the server (DB)**
2. The application later displays this input to users
3. The output is rendered **without sanitization**

👉 Result: Every user who views the page executes the malicious script.

---

## 🔍 Root Cause in This Lab

* Comments are stored and later displayed on the blog page
* No escaping or filtering is applied
* Input is directly inserted into HTML

Example (conceptual):

```html
<div class="comment">
  USER_COMMENT
</div>
```

If attacker submits:

```html
<script>alert(1)</script>
```

It becomes:

```html
<div class="comment">
  <script>alert(1)</script>
</div>
```

💥 Script runs for **every visitor**, not just the attacker.

---

## 🚀 Exploitation Steps

### Step 1: Open the Lab

* Go to the blog post page
* Scroll to the comment section

---

### Step 2: Inject Payload

Enter the following in the **comment box**:

```html
<script>alert(1)</script>
```

Fill other required fields:

* Name → anything
* Email → anything
* Website → optional

---

### Step 3: Submit Comment

* Click **Post Comment**

---

### Step 4: Trigger the Attack

* Reload or revisit the blog page

---

### Step 5: Observe

* Alert box pops up automatically → ✅ Lab solved

---

## ⚙️ Why This Works

* Input is **stored permanently**
* No encoding or sanitization is applied
* Browser executes `<script>` when rendering the page

---

## 🧠 Key Concepts

### 1. Stored vs Reflected XSS

| Feature  | Stored XSS     | Reflected XSS  |
| -------- | -------------- | -------------- |
| Storage  | Saved in DB    | Not stored     |
| Impact   | Multiple users | Single request |
| Severity | Higher         | Lower          |

---

### 2. Persistence = Bigger Damage

Stored XSS is dangerous because:

* Attack runs automatically
* Affects **every visitor**
* No need to trick users again

---

### 3. Payload Variations

Basic payload:

```html
<script>alert(1)</script>
```

Alternative:

```html
<img src=x onerror=alert(1)>
```

---

## 🛡️ Prevention

### ✅ Output Encoding

Escape special characters before rendering:

* `<` → `&lt;`
* `>` → `&gt;`

---

### ✅ Input Sanitization

* Remove `<script>` tags
* Use allowlists instead of blocklists

---

### ✅ Use Framework Protection

* Modern frameworks auto-escape output

---

### ✅ Content Security Policy (CSP)

* Prevent inline JavaScript execution

---

## 💡 Real-World Impact

Stored XSS is serious because attackers can:

* Steal session cookies of all users
* Hijack accounts
* Spread malware
* Perform actions as victims (CSRF-like behavior)

---

## 🧾 Key Takeaways

* Stored XSS = persistent + dangerous
* Always sanitize + encode user input
* Comment sections are common attack points
* One payload can affect thousands of users

---

## 🏁 Final Payload Used

```html
<script>alert(1)</script>
```

---

## 🔗 Tags

`Stored-XSS` `XSS` `Web-Security` `BurpSuite` `PortSwigger`

---
