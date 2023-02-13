# DB 연동

## mysql_query
```php
<?php
$mysql_host = 'localhost';
$mysql_user = 'root';
$mysql_pwd = '1234';
$mysql_db = 'test';
$dbcon = mysql_connect($mysql_host, $mysql_user, $mysql_pwd);
mysql_select_db($mysql_db, $dbcon);
mysql_query('set names utf8');
$query = 'select * from users';
$result = mysql_query($query);
while($row = mysql_fetch_row($result)) {
    echo $row[0];
}
mysql_close($dbcon);
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    body
</body>
</html>
```

## mysqli_query
```php
<?php
$mysql_host = 'localhost';
$mysql_user = 'root';
$mysql_pwd = '1234';
$mysql_db = 'test';
$dbcon = mysqli_connect($mysql_host, $mysql_user, $mysql_pwd);
$db = mysqli_select_db($dbcon, $mysql_db);
mysqli_query($dbcon, 'set names utf8');
$query = 'select * from users';
$result = mysqli_query($dbcon, $query);
while($row = mysqli_fetch_assoc($result)) {
    echo $row['name'];
}
mysqli_close($dbcon);
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    body
</body>
</html>
```
