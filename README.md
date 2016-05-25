SQLAnywhereClient
=================

Class to connect to the Sybase database using the SQLAnywhere extension.

This class is based on the native PDO class.

TODO:
- test class

## Installation

1. Install the [SQLAnywhere PHP module](http://scn.sap.com/docs/DOC-40537) first.
2. Use Composer to install the package. Add th following line to `require`:

    // ...
    "require": {
        "pxlcore/SQLAnywhereClient": "dev-master"
    },
    // ...

## How to use

### Initialize the connection `SQLAnywhereClient::__construct`:

```php
<?php
    require '../vendor/autoload.php';

    use Pxlcore\SQLAnywhereClient;

    try {
        $dns = "uid={user};pwd={pwd};ENG={eng};commlinks=tcpip{host={host};port={port}}";
        $con = new SQLAnywhereClient( $dns );
    } catch (Exception $e) {
        echo $e->getMessage();
    }
?>
```
You have two options: `auto_commit` e `is_persistent`.

* `auto_commit` autocommit, default is `true`;
* `is_persistent` persistent connection, default is `false`;

```php
<?php
    require '../vendor/autoload.php';

    use Pxlcore\SQLAnywhereClient;

    try {
        $dns = "uid={uid};pwd={senha};ENG={};commlinks=tcpip{host={seuuhost};port={suasenha}}";
        $autocommit = false;
        $persistent = true;

        $con = new SQLAnywhereClient( $dns, $autocommit, $persistent );
    } catch (Exception $e) {
        echo $e->getMessage();
    }
?>
```


### Excecute SQL command `SQLAnywhereClient::exec()`:

```php
<?php

    $sql = "SELECT * FROM users";
    $result = $con->exec( $sql );

    echo "<pre>";
    print_r($result->fetch());
    echo "</pre>";
    exit;
?>
```

### Execute SQL command with return values `SQLAnywhereClient::query()` :

Método retornar um array com várias posições

```php
<?php

    $sql = "SELECT name, email FROM users";

    foreach ($con->query( $sql ) as $result) {
        print_r($result);
    }
    exit;
?>
```

### Return one row `SQLAnywhereQuery::fetch`

Returns the first line

```php
<?php
    $sql = "SELECT name, email FROM users";
    $result = $con->exec( $sql );
    $user = $result->fetch();

    print_r($user);
    exit;
?>
```

### Return format

The return format of class `SQLAnywhereClient` could be

```php
<?php
    // ...
    SQLAnywhereClient::FETCH_ARRAY;

    // ...
    SQLAnywhereClient::FETCH_ASSOC; // default format!

    // ...
    SQLAnywhereClient::FETCH_OBJECT;

    // ...
    SQLAnywhereClient::FETCH_ROW;

    // ...
    SQLAnywhereClient::FETCH_FIELD;
?>
```

Example:

```php
<?php

    $sql = "SELECT name, email FROM users";
    $result = $con->exec( $sql );
    $user = $result->fetch( SQLAnywhereClient::FETCH_OBJECT );

    print_r($user);
    exit;
?>
```

### Return all rows `SQLAnywhereQuery::fetchAll`

```php
<?php
    $sql = "SELECT name, email FROM users";
    $result = $con->exec( $sql );
    $user = $result->fetchAll();

    print_r($user);
    exit;
?>
```

You could use different return formats.
```php
<?php

    $sql = "SELECT name, email FROM users";
    $result = $con->exec( $sql );
    $user = $result->fetchAll( SQLAnywhereClient::FETCH_OBJECT );

    print_r($user);
    exit;
?>
```

### Number of rows `SQLAnywhereQuery::rowCount`

Returns the number of rows.

```php
<?php
    $sql = "SELECT name, email FROM users";
    $result = $con->exec( $sql );

    echo "Number of rows: " . $result->rowCount();
    exit;
?>
```

Or like

```php
<?php
    $sql = "SELECT name, email FROM users";
    $result = $con->exec( $sql );

    echo "Number of rows: " . $result->count();
    exit;
?>
```

### Number of columns `SQLAnywhereQuery::fieldCount`

```php
<?php
    $sql = "SELECT name, email FROM users";
    $result = $con->exec( $sql );

    echo "Found " . $result->fieldCount() . " columns in the table.";
    exit;
?>
```

### Last ID `SQLAnywhereClient::lastInsertId()`

```php
<?php
    $sql = "INSERT INTO users  name, email VALUES ('Carlos', 'contato@carlosgartner.com.br')";
    if ($con->exec( $sql )) {
        echo $con->lastInsertId();
    }
    exit;
?>
```

### Prepared Statement `SQLAnywhereClient::prepare()`:

Use `?` for Prepared Statement:

```php
<?php
    $sql = "INSERT INTO users  name, email VALUES (?, ?)";
    $stmnt = $con->prepare( $sql );
    if ($stmnt->execute(array('Carlos', 'contato@carlosgartner.com.br'))) {
         echo $con->lastInsertId();
    }
    exit;
?>
```

or use named parameters:

```php
<?php
    $sql = "INSERT INTO users  name, email VALUES (:name, :email)";
    $stmnt = $con->prepare( $sql );
    if ($stmnt->execute(array(
        ':name' => 'Carlos',
        ':email' => 'contato@carlosgartner.com.br'
    ))) {
         echo $con->lastInsertId();
    }
    exit;
?>
```

### Bind Parameters `SQLAnywherePrepared::bindParam()`:

```php
<?php
    $sql = "INSERT INTO users  name, email VALUES (:name, :email)";
    $stmnt = $con->prepare( $sql );

    $name = "Carlos A.";
    $email = "contato@carlosgartner.com.br";

    $stmnt->bindParam(':name', $name);
    $stmnt->bindParam(':email', $email);

    if ($stmnt->execute()) {
         echo $con->lastInsertId();
    }
    exit;
?>
```
