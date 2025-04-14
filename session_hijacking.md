## 🧩 Solution to the Challenge

I started by inspecting the web application and its different visible elements. I explored the various available pages and attempted to interact with all accessible features, particularly the **login page** and the **file upload section**.

### 🔍 Initial Attempts:

- I tried to exploit a potential **upload vulnerability** by sending a **shell file**, but it didn't work (no processing or execution of the file).
- Next, I looked at the **source code** of the page and the linked JavaScript files, but I didn’t find any sensitive or exploitable information.
- I then focused on the **login form**.

### 🧪 Injection Tests:

- I attempted **XSS vulnerabilities** in the form fields, but without success.
- Then, I launched a **SQLi (SQL injection)** attack using Burp Suite, employing the **Sniper** technique in Intruder, but no variations or error messages confirmed a vulnerability.

### 🕵️ Traffic Analysis with Burp Suite:

While inspecting the login requests in Burp Suite, I noticed something interesting in the headers:

```
Cookie: I_am_admin=68934a3e9455fa72420237eb05902327
```

This seemed like a **session cookie** used to determine whether a user is an admin or not.

### 🔐 Exploitation:

I copied the cookie value (`68934a3e9455fa72420237eb05902327`) and submitted it to the site [https://md5decrypt.net](https://md5decrypt.net). I discovered that it was the **MD5 hash** of the string `"false"`.

I figured that if `"false"` corresponds to a non-admin user, then `"true"` might be the key to gaining admin access.

So, I asked ChatGPT for the **MD5 hash of `"true"`**, and it returned the following value:

```
b326b5062b2f0e69046810717534cb09
```

### 🎯 Injection of the Modified Cookie:

In Burp Suite, I modified the cookie in the **Repeater** tab:

```
Cookie: I_am_admin=b326b5062b2f0e69046810717534cb09
```

I then sent the request and observed the response. The server returned a **script containing the flag**:

```html
<script>alert('Good job! Flag : df2eb4ba34ed059a1e3e89ff4dfc13445f104a1a52295214def1c4fb1693a5c3'); </script>
```

---

## 🏁 Conclusion:

The challenge relied on a **poor management of the authentication logic** via an insecure cookie. It was possible to **completely bypass the login form** by providing a properly forged client-side cookie.

---
![Capture d’écran 2025-04-11 à 22 45 17](https://github.com/user-attachments/assets/216c5ae9-2976-4f8f-aa24-d84ae0dfff36)
![Capture d’écran 2025-04-11 à 23 12 45](https://github.com/user-attachments/assets/d9b1fd98-6e3b-4d30-b57c-0adb62be0fb6)


