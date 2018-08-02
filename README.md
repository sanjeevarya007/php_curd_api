# PHP-CRUD-API

Single file PHP script that adds a REST API to a MySQL 5.5 InnoDB database.  

## Requirements

  - PHP 5.3 or higher with MySQLi, libpq, SQLSRV or sqlite3 enabled (PHP 7 recommended)
  - MySQL 5.6 / MariaDB 10.0 or higher for spatial features in MySQL

## Installation

This is a single file application! Upload "api.php" somewhere and enjoy!


## Limitations

  - Primary keys should either be auto-increment (from 1 to 2^53) or UUID
  - Column names must be strictly alphanumeric, hyphens/underscores are allowed
  - Composite primary or foreign keys are not supported
  - Complex filters (with both "and" & "or") are not supported
  - Complex writes (transactions) are not supported
  - Complex queries calling functions (like "concat" or "sum") are not supported
  - MySQL storage engine must be either InnoDB or XtraDB
  - SQLite does not support binary and spatial/GIS functionality
  - MySQL BIT field type is not supported (use TINYINT)

## Features

  - Single PHP file, easy to deploy.
  - Very little code, easy to adapt and maintain
  - Streaming data, low memory footprint
  - Supports POST variables as input
  - Supports a JSON object as input
  - Supports a JSON array as input (batch insert)
  - Supports file upload from web forms (multipart/form-data)
  - Condensed JSON output: first row contains field names
  - Sanitize and validate input using callbacks
  - Permission system for databases, tables, columns and records
  - Multi-tenant database layouts are supported
  - Multi-domain CORS support for cross-domain requests
  - Combined requests with support for multiple table names
  - Search support on multiple criteria
  - Pagination, sorting and column selection
  - Relation detection and filtering on foreign keys
  - Relation "transforms" for PHP and JavaScript
  - Atomic increment support via PATCH (for counters)
  - Binary fields supported with base64 encoding
  - Spatial/GIS fields and filters supported with WKT
  - Unstructured data support through JSON/JSONB/XML
  - Generate API documentation using Swagger tools

## Configuration

Edit the following lines in the bottom of the file "api.php":

```
$api = new PHP_CRUD_API(array(
	'username'=>'xxx',
	'password'=>'xxx',
	'database'=>'xxx',
));
$api->executeCommand();
```

These are all the configuration options and their default values:

```
$api = new PHP_CRUD_API(array(
	'dbengine'=>'MySQL',
	'username'=>'root',
	'password'=>null,
	'database'=>false,
// for connectivity (defaults to localhost):
	'hostname'=>null,
	'port'=>null,
	'socket'=>null,
	'charset'=>'utf8',
// callbacks with their default behavior
	'table_authorizer'=>function($cmd,$db,$tab) { return true; },
	'record_filter'=>function($cmd,$db,$tab) { return false; },
	'column_authorizer'=>function($cmd,$db,$tab,$col) { return true; },
	'tenancy_function'=>function($cmd,$db,$tab,$col) { return null; },
	'input_sanitizer'=>function($cmd,$db,$tab,$col,$typ,$val) { return $val; },
	'input_validator'=>function($cmd,$db,$tab,$col,$typ,$val,$ctx) { return true; },
	'before'=>function(&$cmd,&$db,&$tab,&$id,&$in) { /* adjust array $in */ },
	'after'=>function($cmd,$db,$tab,$id,$in,$out) { /* do something */ },
// configurable options
	'allow_origin'=>'*',
	'auto_include'=>true,
// dependencies (added for unit testing):
	'db'=>null,
	'method'=>$_SERVER['REQUEST_METHOD'],
	'request'=>$_SERVER['PATH_INFO'],
	'get'=>$_GET,
	'post'=>file_get_contents('php://input'),
	'origin'=>$_SERVER['HTTP_ORIGIN'],
));
$api->executeCommand();
```

NB: The "socket" option is not supported by MS SQL Server. SQLite expects the filename in the "database" field.

## Documentation

After configuring you can directly benefit from generated API documentation. On the URL below you find the generated API specification in [Swagger](http://swagger.io/) 2.0 format.

    http://localhost/api.php

Try the [editor](http://editor.swagger.io/) to quickly view it! Choose "File" > "Paste JSON..." from the menu.

## Usage

You can do all CRUD (Create, Read, Update, Delete) operations and one extra List operation. Here is how:

### List

List all records of a database table.

```
GET http://localhost/api.php/categories
```

Output:

```
{"categories":{"columns":["id","name"],"records":[[1,"Internet"],[3,"Web development"]]}}
```

### List + Transform

List all records of a database table and transform them to objects.

```
GET http://localhost/api.php/categories?transform=1
```

Output:

```
{"categories":[{"id":1,"name":"Internet"},{"id":3,"name":"Web development"}]}
```
### Apache

Enable mod_rewrite and add the following to your ".htaccess" file:

```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.*)$ api.php/$1 [L,QSA]
```

The ".htaccess" file needs to go in the same folder as "api.php".

### Nginx

For Nginx you may want to add something like this:

```
location /api {
    rewrite ^/api(.*)$ /api.php$1 last;
}
```

This should be added to your Nginx config, before or after the `location ~ [^/]\.php(/|$)` section.

## Debugging

If you have trouble getting the file to work you may want to check the two environment variables used. Uncomment the following line:

```
var_dump($_SERVER['REQUEST_METHOD'],$_SERVER['PATH_INFO']); die();
```

And then visit:

```
http://localhost/api.php/posts
```

This should output:

```
string(3) "GET"
string(6) "/posts"
```

If it does not, something is wrong on your hosting environment.

## Composer Installation

You can use [Composer](https://getcomposer.org/) to install. Include the library in your composer.json file:

```json
{
    "require": {
        "mevdschee/php-crud-api": "dev-master"
    }
}
```

Run `composer install` and then to use the library in your own code like this:

```php
<?php

include './vendor/autoload.php';

// DB Connection
$api = new PHP_CRUD_API(array(
 	'dbengine'=>'MySQL',
 	'hostname'=>'localhost',
 	'username'=>'',
 	'password'=>'',
	'database'=>'',
	'charset'=>'utf8'
));
$api->executeCommand();

```

## License

MIT
