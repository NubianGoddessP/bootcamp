# MySQL with PHP

In this lesson we will use PHP's PDO library to work with our database.

The first step is to create a connection to your database. You connection script will likely be part of larger configuration.

Under your example.com create the file *core/db_connect.php*

```php
<?php

$host = '127.0.0.1';
$db   = 'bootcamp';
$user = 'root';
$pass = 'password';
$charset = 'utf8mb4';

$dsn = "mysql:host=$host;dbname=$db;charset=$charset";
$options = [
    PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION,
    PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
    PDO::ATTR_EMULATE_PREPARES   => false,
];
try {
     $pdo = new PDO($dsn, $user, $pass, $options);
     var_dump($pdo);
} catch (\PDOException $e) {
     throw new \PDOException($e->getMessage(), (int)$e->getCode());
}
```

Create the file */var/www/example.com/webroot/posts.php*
```php
<?php
include '../core/db_connect.php';
```
Preview this file in your browser [http://localhost/example.com/webroot/posts.php](http://localhost/example.com/webroot/posts.php)

Update posts.php as follows
```php
<?php
include '../core/db_connect.php';

$stmt = $pdo->query("SELECT * FROM posts");

while ($row = $stmt->fetch())
{
    var_dump($row);
}
```

```php
<?php
include '../core/db_connect.php';

$content=null;
$stmt = $pdo->query("SELECT * FROM posts");

while ($row = $stmt->fetch())
{

    $content .= "<a href=\"post?slug={$row['slug']}\">{$row['title']}</a>";
}

echo $content;
```

Add the template
```
<?php
include '../core/db_connect.php';

$content=null;
$stmt = $pdo->query("SELECT * FROM posts");

while ($row = $stmt->fetch())
{

    $content .= "<a href=\"post.php?slug={$row['slug']}\">{$row['title']}</a>";
}

include '../core/layout.php';
```

Create a copy of posts.php called post.php
```php
<?php
include '../core/db_connect.php';

$slug = "'{$_GET['slug']}'";

$content=null;
$stmt = $pdo->query("SELECT * FROM posts WHERE slug={$slug}");

while ($row = $stmt->fetch())
{

    $content .= "<a href=\"post.php?slug={$row['slug']}\">{$row['title']}</a>";
}

echo $content;
```
Change as follows. Everything about this is wrong - why?
```php
<?php
include '../core/db_connect.php';

$slug = "'{$_GET['slug']}'";

$content=null;
$stmt = $pdo->query("SELECT * FROM posts WHERE slug={$slug}");

$row = $stmt->fetch();
$content .= "<h1>{$row['title']}</h1>";

echo $content;
```


//$slug='hello';

$slug="(SELECT slug FROM posts WHERE slug = 'hello')";

$stmt = $pdo->query("SELECT * FROM posts WHERE slug={$slug}");

//$stmt = $pdo->prepare('SELECT * FROM posts WHERE slug = ?');
//$stmt->execute([$slug]);

//$stmt = $pdo->prepare('SELECT * FROM posts WHERE slug = :slug');
//$stmt->execute(['slug' => $slug]);

while ($row = $stmt->fetch())
{
    var_dump($row);
    echo $row['title'] . "\n";
}