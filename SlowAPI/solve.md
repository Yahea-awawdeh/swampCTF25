# Challenge description

The Gator Research Institute asked me to develop an app for them. I've been hearing a lot of hullabaloo recently about Next.js, so I decided to make my app inspired by their code. Can you see if you can access the flag? Authentication hasn't been implemented yet, so you'll need to find a way around it.

# Solve

![Screenshot 2025-03-31 191033](https://github.com/user-attachments/assets/7b6bfdda-7aac-404e-8664-8b7ffbfa58f3)
There is Two main function:
<br>
-Login
<br>
-SecretFlag
<br>

Let's try to login:

![Screenshot 2025-03-31 191228](https://github.com/user-attachments/assets/df568d5f-bb9e-4f57-a0d1-c3185e29b219)

Opps the login and registration is unavailable.

<br>
Let's try SecretFlag:
<br>

![Screenshot 2025-03-31 191409](https://github.com/user-attachments/assets/9ad6b3f7-1405-4962-8be7-e6f81fa7f644)

It seems there is a problem in the api but there is hint 
# Hint: Only swamp creatures can access this page. Maybe there's a way to convince the API that you're a server-side middleware process...
After some search i found a Poc in how can bypass the middleware in Next.js
https://projectdiscovery.io/blog/nextjs-middleware-authorization-bypass

let's try to use it now:
![Screenshot 2025-03-31 192613](https://github.com/user-attachments/assets/aa829164-93ee-4705-b3d4-a51ba21eb78d)

And it work 
# Flag:swampCTF{b3w4r3_th3_m1ddl3w4r3}

