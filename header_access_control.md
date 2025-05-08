## 🧩 Solution to the Challenge – Hidden URL & Header Validation

### 🔍 Discovery

While analyzing the home page of the site:

```
http://192.168.1.56/
```

At the bottom, there was a **clickable copyright link**:

```html
<a href="?page=b7e44c7a40c5f80139f0a50f3650fb2bd8d00b0d24667c4c2ca32c88e13b758f">
  <li>&copy; BornToSec</li>
</a>
```

![Capture d’écran 2025-05-09 à 00 14 12](https://github.com/user-attachments/assets/ba035e5b-8e9d-41f6-849e-0c0e763e6cc8)


When clicked, this took me to a new page. Inside the **source code**, I found two crucial hidden comments:

```html
<!-- Let's use this browser: "ft_bornToSec". It will help you a lot. -->
<!-- You must come from: "https://www.nsa.gov/". -->
```

![Capture d’écran 2025-05-09 à 00 14 21](https://github.com/user-attachments/assets/37106dc7-7f4e-4eb1-887d-70cc36ca151d)
![Capture d’écran 2025-05-09 à 00 14 40](https://github.com/user-attachments/assets/c09f89ce-2176-49c9-821d-c8aac5833d71)


These comments gave me important clues — the **User-Agent** and **Referer** headers needed to be **manipulated** to access the hidden content on this page.

---

### 🧪 Exploitation

To exploit this, I sent a request using **Burp Suite** or **curl**, modifying the HTTP headers as follows:

```http
GET / HTTP/1.1
Host: 192.168.1.56
User-Agent: ft_bornToSec
Referer: https://www.nsa.gov/
```

This request led the server to respond differently, revealing the hidden **flag**.
![Capture d’écran 2025-05-08 à 23 50 14](https://github.com/user-attachments/assets/b0906464-7cdf-4934-8e9c-ebe316805f4e)

```f2a29020ef3132e01dd61df97fd33ec8d7fcd1388cc9601e7db691d17d4d6188```

---

### 🛠️ Technical Insight

The challenge relied on **header-based access control**. The server was **checking both the `User-Agent` and `Referer` headers** for a specific value:

* `User-Agent: ft_bornToSec`
* `Referer: https://www.nsa.gov/`

Since both of these headers can be easily **spoofed** or manipulated by a client (such as using Burp Suite or curl), it’s clear that the application didn’t perform sufficient server-side validation. This presents a **security vulnerability** — relying on client-controlled headers for sensitive actions is risky and should be avoided.

---

### ✅ Conclusion

> 🧠 **Takeaway**: Always ensure that any critical decision-making logic on the server is **not influenced by user-controlled input**, including headers like `User-Agent` and `Referer`.
