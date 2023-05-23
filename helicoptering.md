
![Pasted image 20230523132550](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/9a9bebdc-c4b6-4c1c-9753-85dda462eba6)

This challenge was broken up into partial flags where you would obtain a piece of the flag for completing both part one and two. 

When both were completed, so was this challenge




## Part 1

![Pasted image 20230523132832](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/b9718be9-48c9-4cc8-9914-4abf55ec15f0)

 As shown in the image above, we have a rule that checks to see if any string is not explicitly localhost, then it is true, and then access to the resource is denied by a 403 FORBIDDEN 


I tried multiple different payloads before really analyzing the rule before I learned these are ruleset conditions written in the .htaccess file inside of the webserver. 


Breaking down %{HTTP_HOST} !^\localhost$:

- %{HTTP_HOST} - is the HTTP HOST HEADER, that indicates what host is accessing the resource

- !^\localhost$ - is the string the header is matching on, which in this case !^\localhost$ explicitly refers to anything that is not exactly the string "localhost"


- I decided the best way to change the `Host` header value was to do it in BurpSuite, so I opened up BurpSuite to change the `Host` header

### Burpsuite 


![Pasted image 20230523133255](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/e6648a0b-7b51-47bd-a0f5-306e78576d0c)




As we can see, its no wonder we are getting a 403 Forbidden, the `Host` header value is set to localhost:1337, which means that this matches the rule in the .htaccess rewrite condition




To rehash: 
	 %{HTTP_HOST} !^\localhost$ is matching to DENY all access to the resource if the `Host` header value is NOT "localhost"
	 This is followed by the next line of the file that states the RewriteRule "." "-" \[\F\], the "F" means forbidden here, so the RewriteRule is what is enforced based on what is met by the logic of the Rewrite condition. 
	 When \[F] is enforced, all access is redirected to 403 Forbidden when the `Host` header is NOT <u>localhost EXACTLY</u>





![Pasted image 20230523134139](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/83e79d4c-4b9a-469b-baa1-8d04a639d6ef)


If we remove :1337 from localhost in the `Host` header and forward the request, we won't pass the Rewrite Condition because the `Host` header value does not match what the server is expecting





![Pasted image 20230523134258](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/9691e903-5436-4829-9b03-189d8d353682)






We obtain part 1 of the flag:

- DUCTF{that_it_}



## Part 2


![Pasted image 20230523134452](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/8c977c23-ded4-4905-99a8-55b9e72e6c1a)


As shown in the image above, we have another .htaccess file we can see

The RewriteRule is the same as part 1, but the RewriteCondition is different this time


Breaking down .htaccess:

Our new condition is as follows:
%{THE_REQUEST} = whatever the request type is (PUT, GET, POST, OPTIONS, DELETE, etc.)
and the word flag

In english: Match on ANY request and the word "flag", if true then redirect to 403 Forbidden.


![Pasted image 20230523140006](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/e1099f34-50ae-4408-8542-e5a0fce08842)



The solution here is different than what I did in part1.


Just as in part1 I used burpsuite on the page 



### Burpsuite:

![Pasted image 20230523140245](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/256eab31-aafe-425c-81f7-122c9455ac3f)



I send the intercepted request to `Repeater` where I could test multiple payloads until I found the one that worked


![Pasted image 20230523140407](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/a09745e5-f521-4737-b2c1-9169a54255f9)



In repeater, I tried multiple different payloads of changing the slashes, but URL encoding the entire flag string turned out successful.

I also tested this by URL encoding just portions of the flag string like just the f so the string would be %66lag,
and I tried the l too, which would be f%6cag, every single one of these returned a 200 OK and the request went through successfully:


![Pasted image 20230523141110](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/0f06639b-22ef-48b1-b7a3-0a080a780e1c)


![Pasted image 20230523141127](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/e465ca41-9cec-408c-be3b-05a4e23f9e75)


----

![Pasted image 20230523141251](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/4a620126-ef6f-42f0-9e10-1a2b6d8f5668)



![Pasted image 20230523141320](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/7be4a820-033b-4ca0-aff2-e38bee7c8732)

---

![Pasted image 20230523141207](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/36984404-495b-4c7e-9ac1-1ed1b05e1d8a)


![Pasted image 20230523141330](https://github.com/GoldenEmp3R0R/DownUnderCTF-2022-Write-Up/assets/125948172/b6dfc7d2-1cc1-4e5a-84c9-89371a866c1f)


---



The flag returned from the web page revealed and was next_time_im_using_nginx}

combined with our first flag from part 1 we get:


- DUCTF{that_it_next_time_im_using_nginx}


The challenge is completed!
