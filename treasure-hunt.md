BEHOLD.

THE TIME HAS COME

TO FIND THE `ONE PIECE!!!`


- Note: Listening to the music is critical to solving the challenge



Luffy hadn't find jack squat in 25 years of his show running, so hackers hired us to find the One Piece, because Gold Roger was actually a coder, and the father of the internet.



**The actual challenge-**

We are presented with the 

following page when we enter the webpage for the challenge:





- There are no cookies created for a session, or any interesting artifacts in the page source worth looking into. 

- There are few interactable elements on the page. There is a Login Button, a Sign Up button, and a Home button that just redirects to the main page.

- By going into the Sign Up, we can create any user we want to.




 On the sign up page we get the following:
 

![Pasted image 20230523145634](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/7dabc6be-8855-4f2f-a8f1-3f0409078530)


Here we have input fields to create our user with a username to login with, a password, and a name for that user and the treasures they possess.

I create my favorite pirate  Shanks:

Username: Shanks
Name: Shanks
Treasures: Strawhat
Password: redhair


After creating my user, I was redirected to the login page.


![Pasted image 20230523150457](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/001d9243-ca23-400c-a60a-1777de0160a8)


I logged in with my credentials as Username=Shanks and Password=redhair, but I also wanted to see if the login page authenticated password fields.

I got an error that asked my to check my user info again.


As I was logged in I didn't see too much change, however there was now a jwt token created for me I noticed- 


![Pasted image 20230523150724](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/40d4b8b0-22d9-4d39-bf8f-4e4315646b8e)


access_token_cookie =

eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJmcmVzaCI6ZmFsc2UsImlhdCI6MTY4NDg2ODc1MCwianRpIjoiODdjMDllMDEtZjgwZS00ODc3LTkxZmYtNzhkM2JjNTg5ZmMyIiwidHlwZSI6ImFjY2VzcyIsInN1YiI6OCwibmJmIjoxNjg0ODY4NzUwLCJleHAiOjE2ODQ4Njk2NTB9.bRrCdBxn1vRH-3ZKDwhTIVLhYcwZIAYIc_sFCgkw8Sk


I took the jwt to jwt.io to decode it


## Decoding the Cookie:


![Pasted image 20230523150914](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/143e32b8-0fc2-4e21-bf27-87c771725270)



As per usual the JWT was using HS256 which means it was using standard jwt format- Header, Payload, and Signature used to sign the token. This means there is a secret value that exists that could be used to sign the token


When decoding the cookie I see something very interesting that immediately pokes out to me: sub: 8. I assumed this to be the identifier the server uses to store each user on the server and wondered if I could leverage this like an IDOR to view the information of other subject users if I was able to sign the token with the same secret but assign a different subject it refers to. 


In order to change the token, I needed to obtain the secret value of the jwt token. This is where hashcat comes in handy for its JWT mode.


## Breaking the secret 

I took the jwt to hashcat and GPU cracked the jwt's secret almost instantly.


![240404844-ce227b9f-4e45-4d2d-897e-33b3ed08b37a](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/d4730ad2-f3ab-4904-a1ff-97b18244bff1)


As seen in the image above. the secret used to sign the token was...........onepiece lmao I honestly was dying for a hot minute after I saw this.


Back to the challenge though, I went back to jwt.io and changed the value of subject to 1, as 1 refers to the first user created I assumed, and I used the secret key "onepiece" for my new token 


With my now completed token, I changed the value of the existing token in the webpage with my newly made token.


![Pasted image 20230523151950](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/d1f6a3b2-758c-48bd-91e9-804a778d0368)




I inputted the new token inside of the access_token_cookie field (too long to see entire token):


![Pasted image 20230523152211](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/613db4fa-d2d8-4760-9a64-5dd5c7a13d80)



After we refresh the page with the new token utilized we get the following:


![Pasted image 20230523152410](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/0c89d539-e60a-48f0-a586-eaff32ed5ecb)



The flag: DUCTF{7h3-0n3-p13c3-15-4ll-7h3-fl465-y0u-637-4l0n6-7h3-w4y}

The challenge is completed! and we found the one piece!










