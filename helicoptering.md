
![Pasted image 20230523132550](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/9a9bebdc-c4b6-4c1c-9753-85dda462eba6)

This challenge was broken up into partial flags where you would obtain a piece of the flag for completing both part one and two. 

When both were completed, so was this challenge




## Part 1

 ![Pasted image 20230523132832.png]
 As shown in the screenshot above, we have a rule that checks to see if any string is not explicitly localhost, then it is true, and then access to the resource is denied by a 403 FORBIDDEN 


![[Pasted image 20230523132928.png]]


I tried multiple different payloads before really analyzing the rule before I learned these are ruleset conditions written in the .htaccess file inside of the webserver. 


Breaking down %{HTTP_HOST} !^\localhost$:

%{HTTP_HOST} - is the HTTP HOST HEADER, that indicates what host is accessing the resource

!^\localhost$ - is the string the header is matching on, which in this case !^\localhost$ explicitly refers to anything that is not exactly the string "localhost"


I decided the best way to change the `Host` header value was to do it in BurpSuite, so I opened up BurpSuite to change the `Host` header

![[Pasted image 20230523133255.png]]


As we can see, its no wonder we are getting a 403 Forbidden, the `Host` header value is set to localhost:1337, which means that this matches the rule in the .htaccess rewrite condition

To rehash: 
	- %{HTTP_HOST} !^\localhost$ is matching to DENY all access to the resource if the `Host` header value is NOT "localhost"
	- This is followed by the next line of the file that states the RewriteRule "." "-" \[\F\], the "F" means forbidden here, so the RewriteRule is what is enforced based on what is met by the logic of the Rewrite condition. 
	- When \[F] is enforced, all access is redirected to 403 Forbidden when the `Host` header is NOT <u>localhost EXACTLY</u>



![[Pasted image 20230523134139.png]]

If we remove :1337 from localhost in the `Host` header and forward the request, we won't pass the Rewrite Condition because the `Host` header value does not match what the server is expecting

![[Pasted image 20230523134258.png]]


We obtain part 1 of the flag:

- DUCTF{that_it_}






## Part 2


![[Pasted image 20230523134452.png]]


As shown in the image above, we have another .htaccess file we can see

The RewriteRule is the same as part 1, but the RewriteCondition is different this time


Breaking down .htaccess:

Our new condition is as follows:
%{THE_REQUEST} = whatever the request type is (PUT, GET, POST, OPTIONS, DELETE, etc.)
and the word flag

In english: Match on ANY request and the word "flag", if true then redirect to 403 Forbidden.

![[Pasted image 20230523140006.png]]


The solution here is different than what I did in part1.


Just as in part1 I used burpsuite on the page 



Burpsuite:


![[Pasted image 20230523140245.png]]

I send the intercepted request to `Repeater` where I could test multiple payloads until I found the one that worked



![[Pasted image 20230523140407.png]]

In repeater, I tried multiple different payloads of changing the slashes, but URL encoding the entire flag string turned out successful.

I also tested this by URL encoding just portions of the flag string like just the f so the string would be %66lag,
and I tried the l too, which would be f%6cag, every single one of these returned a 200 OK and the request went through successfully.


![[Pasted image 20230523141110.png]]

![[Pasted image 20230523141127.png]]

----


![[Pasted image 20230523141251.png]]

![[Pasted image 20230523141320.png]]



---


![[Pasted image 20230523141207.png]]

![[Pasted image 20230523141330.png]]

---



The flag returned from the web page revealed and was next_time_im_using_nginx}

combined with our first flag from part 1 we get:


- DUCTF{that_it_next_time_im_using_nginx}


The challenge is completed!
