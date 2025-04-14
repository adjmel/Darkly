## 🧩 Resolution of the Challenge

I started on the **login page** and clicked on the **"I forgot my password"** link to test if there was a potential vulnerability in the password reset process. However, I encountered a validation error, which caught my attention.

### 🔍 Analysis with Burp Suite:

- I used **Burp Suite** to intercept and analyze the HTTP request sent when clicking the password reset link.
- In the request, I noticed an **email address** was being sent to the webmaster’s email (`webmaster@borntosec.com`) in the following format:
  ```
  mail=webmaster%40borntosec.com&Submit=Submit
  ```

### 🧪 Modifying the Request:

- I copied this request into **Burp Suite Repeater** and replaced the email address with **my own email**.
- I then sent the modified request.

### 🎯 Result:

- After sending the modified request, I got the flag : 1D4855F7337C0C14

---

## 🏁 Conclusion:

This vulnerability highlights an **issue in the password reset process**, likely caused by **poor validation of the email address** in the request. By simply manipulating the email parameter in the intercepted request, I was able to retrieve sensitive information (the flag). This vulnerability could also be exploited to access sensitive data from other users if the system wasn’t properly secured.

- ![Capture d’écran 2025-04-08 à 23 58 31](https://github.com/user-attachments/assets/e80dc80f-3c0a-4a53-8085-7e86312d7b78)
- ![Capture d’écran 2025-04-14 à 19 07 43](https://github.com/user-attachments/assets/2e8b88c9-5eb0-42d4-b65b-72fcea4fa2fe)
![Capture d’écran 2025-04-14 à 18 58 02](https://github.com/user-attachments/assets/3501e22a-c49b-4ced-bc0d-862843106c0f)
