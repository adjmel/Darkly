## 🧩 Solution to the Challenge

![Capture d’écran 2025-05-27 à 22 18 58](https://github.com/user-attachments/assets/1c8fa165-b3aa-4dee-b3b6-09e34d2009a5)


While testing the comment form on the site, I noticed it filtered certain characters, blocking classic XSS payloads like `<script>alert(1)</script>`. This made me suspect there was some input filtering in place.

---

## 🕵️ Manual Testing:

I experimented with different inputs, trying to bypass the filters. Eventually, I submitted the payload:

```
<a>script<a>
```

This payload was accepted and rendered on the page. Although it wasn’t a real script tag and didn’t execute any code, the presence of the word `"script"` in the comment caused the flag to be revealed directly.

![Capture d’écran 2025-05-27 à 22 19 30](https://github.com/user-attachments/assets/fcda0c17-8b17-4f50-a8a5-337ff3af17e3)

---

## 🚨 Exploitation:

At first, I was confused because the payload contained no executable JavaScript. After some reflection, I realized that the application likely treated the word `"script"` as if it were an actual script tag, and that was enough to trigger the flag display.

In other words, **the flag was “hidden” behind the keyword “script”**, and my guess that `"script"` would represent a real script tag helped me discover it.

The flag is : 0fbb54bbf7d099713ca4be297e1bc7da0173d8b3c21c1811b916a3a86652724e 
---

## 🛡️ Conclusion:

This challenge was less about executing XSS and more about understanding how the application reacted to certain keywords in user input. Despite filtering, the word `"script"` itself triggered the flag. This shows how sometimes the logic behind input handling can be the key to solving a challenge, even when full script injection isn’t possible.
