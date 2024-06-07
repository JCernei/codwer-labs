## User 1

![alt text](/images/web/image.png)

On http://localhost:81 there is the Car Rental Management System 1.0.

By searching for Common Vulnerabilities and Exposures we find
CVE-2020-29227: Unauthenticated Local File Inclusion. This means an unauthenticated adversary can include/execute PHP files like PHP web shells, sensitive PHP files from the system, etc. by exploiting the Local File Inclusion vulnerability. Also, an unauthenticated adversary can disclose/read the code of PHP files of the system through ```php://filter``` meta-wrapper.

Vulnerable Endpoint
```/index.php?page=```

Reading the ```/flag.php``` file of the web application through PHP meta-wrapper
http://localhost:81/index.php?page=php://filter/convert.base64-encode/resource=/flag

After decoding the base64 string found in the page source we get the password for the next user.

Password for user 2: ```CWA{D15CL053_Y0UR_F1L35}```

## User 2

![alt text](/images/web/image2.png)

On http://localhost:82 there are the login and register buttons. After registering we notice that the url looks like this: ```http://localhost:82/profile/3```
We try different numbers instead of 3. On the ```/profile/2``` endpoint we get the flag.

The vulnerability in this case is called Insecure Direct Object Reference (IDOR). It arises when attackers can access or modify objects by manipulating identifiers used in a web application's URLs or parameters. It occurs due to missing access control checks, which fail to verify whether a user should be allowed to access specific data.

Password for user 3: ```CWA{1_d0n7_0r64n1z3_r4v35}```

## User 3

![alt text](/images/web/image3.png)

we go to ```http://localhost:82```
and log in with the credentials provided in the comments when we inspect the page.

In the inspector mode we go to the storage tab and select ```cookies```. Here we find the jwt token assigned to the user at login. After decoding the token using https://jwt.io/ we see that the current user has ```"role": "user"```. We change it to ```"admin"```, then replace the jwt from the web page with the new one, refresh the page and get the flag.

The problem here is that  the server is solely relying on the information contained within the JWT to determine a user's role or privileges without performing additional authorization checks on the server side.
  
Password for user 4: ```CWA{Jw7_5ucc355fully_D3cod3d}```

## User 4

![alt text](/images/web/image4.png)

We look through the provided code, inside the ```Task3/Controllers/AuthController.cs```. Here we see all the endpoints, like:
- HttpGet("login")
- HttpPost("login")

and finally
- HttpGet("supersecret")

which means we can acces it from the browser and get the flag.


Password for root: ```CWA{Endp01nt_P0l1c13s_Appl13d_Succ3ssfully}```


![alt text](/images/web/image5.png)