# Challenge description
Somewhere on this message-board is a hidden flag. Nothing has worked so far but we have noticed a weird comment in the HTML. Maybe it's a clue?

# Solve
![image](https://github.com/user-attachments/assets/c0250fc2-8b39-48e8-ab59-1610852a1d14)

At first, I suspected an XSS (Cross-Site Scripting) vulnerability, but after testing, I realized it was just a standard text input form without script execution.

I decided to inspect the page source for anything interesting and came across a curious comment in the HTML:

![image](https://github.com/user-attachments/assets/b0e1f759-a9a8-4be4-9f10-de3e310a7641)

The comment hinted at an element called <flagstuff> with an attribute code.
This led me to test submitting G1v3M3Th3Fl@g!!!! in the form, but nothing happened.

Realizing that the code might need to be set in the attribute itself, I attempted to modify the element directly via the browser console:
```
const element = document.getElementById("flagstuff");
element.setAttribute("code", "G1v3M3Th3Fl@g!!!!");
```
After executing this, I tried submitting any input in the form again—and boom! The flag appeared:

![image](https://github.com/user-attachments/assets/8aaef517-a7cb-44ba-9ae7-3f32b89a4ea8)

# Flag:swampCTF{Cr0ss_S1t3_Scr1pt1ng_0r_XSS_c4n_ch4ng3_w3bs1t3s}
