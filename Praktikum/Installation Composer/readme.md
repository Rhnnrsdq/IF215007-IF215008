## Hello World Package for PHP Composer 

lebih lanjut bisa [klik link disini](https://packagist.org/packages/ehime/hello-world)
## Usage ##

```bash
$ composer require hexa/hello-world
$ touch test.php
```

```php
<?php
require_once "vendor/autoload.php";

$hello = new Hexa\Demo\Hello();
echo $hello->hello();
```

```bash
$ php test.php
```

It will print "Hello World!" then exit.
