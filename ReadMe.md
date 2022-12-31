# Ahia

Ahia helps with database CRUD operation for a more robust and syntactic operation.

## Installation

To get started all you need to do is:

```php
composer require sirmekus/ahia
```

and you're in. `Ahia`, in Igbo language, means **"Market"** and in context, a database is a "market place" with lots of 'items' (data) in it. Lol.

---

## Usage

---

You don't have to craft [complicated] SQL queries in your script anymore and make your code more readable and clean. **Welcome to the OOP way**.

Using this package requires knowledge of our convention (it's not much. You don't have to learn anything new at all. Don't be scared...#winks). But first, to start using this feature you should import the `Model` class to your script like so:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();
```

By default you should create a `.env` (preferred)  or `env.php` file that contains your database configurations. It is from this file that database configuration connection will be called and initialised. You should copy and place this in your root folder (with the exact keys):

```.env
#(.env)
DB_HOST=localhost
DB_NAME=your_database_name
DB_USER=your_username
DB_PASS=your_database_password
```

or

```php
//(env.php)
define( 'DB_HOST', 'localhost' );
define( 'DB_NAME', 'your_database_name' );
define( 'DB_USER', 'your_username' );
define( 'DB_PASS', 'your_database_password' );
```

We encourage you to store your core database connection config in a file as suggested above. However, you can choose to pass these configurations as aruments to the class like so:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model($DB_HOST, $DB_USER, $DB_NAME, $DB_PASS);
```

## Passing Name of Table

When using any of the CRUD methods in this class the last argument is always the name of the database table (as you'll soon see below). This is one way to always pass the name of the table at runtime.

Another way is by setting the public `table` property to the name of the table. Example:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();
$model->table = "table";
```

And finally, you can specify the table to use by extending the `Model` class and giving the class the same name as the table. By default, if none of the above is specified this is used. The **lower-case, snake-case name** of the class will become the name of the database table to use. Example:

```php
//Fictious Class => TestTable.php
require_once 'path_to_vendor/autoload.php';

namespace YourName\Folder;

use Sirmekus\Database\Model;

class TestTable extends Model
{
    //The table name is 'test_table'. You don't need to add any property or method (unless you wish to add)
}
```

```php
//Now you can use it like you would with the "Model" class => test.php
require_once 'path_to_vendor/autoload.php';

use YourName\Folder\TestTable;

$model = new TestTable();
//name of the table will be **"test_table"** and you can use every method defined in the parent class.
```

You can also, if you don't like our naming convention, set the public `table` property to the name of the table or pass it as argument to any of the CRUD methods. To get the name of your table simply call the `getTable()` method on an instance of `Model` or any class derived from it.

>Please note that you should always set the table by passing the name as argument to any of the CRUD method when called or by setting it via the `table` property on an instantiated class if you use the `Model` class directly.

---

## Examples

- ## **Inserting**

You can insert into table by using the `insert()` method of the class. This method expects a multi-dimensional array of data (specifying the table column(s) and appropriate value(s)) as first argument and an optional **"table"** parameter as second argument. Example:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();
$model->insert([
    'name'=>"Sir Mekus", 
    "country"=>"Nigeria"
    ]);

//or, with name of table
$model->insert([
    'name'=>"Sir Mekus", 
    "country"=>"Nigeria"
    ], 
    $table='developers'
    );
```

You can also insert into the table by specifying dynamic properties at runtime, and then call the `save()` method. Example:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();
$model->name = "Sir Mekus";
$model->country = "Nigeria";
$model->save();
```

They all do the same work effectively. You may choose to, instead of always specifying your table, extend the `Model` class with the name of your table as actual class name and then run your operation using this extended class.

---

- ## **Updating**

To update the table call the `update()` method, passing it an array of data (specifying the table column(s) and appropriate value(s)) as first argument, an optional array of **"where"** clause(s) as second argument and an optional **"table name"** as the final argument. Example:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();
$model->update([
    'name'=>"Sir Mekus", 
    "country"=>"Nigeria"
    ]);

//or
$model->update([
    'name'=>"Sir Mekus", 
    "country"=>"Nigeria"
    ], 
    [
        'email'=>'mekus600@gmail.com'
    ]
    );

//or
$model->update([
    'name'=>"Sir Mekus", 
    "country"=>"Nigeria"
    ], 
    [
        'email'=>'mekus600@gmail.com'
    ],
     $table='developers');
```

---

- ## **Updating or Creating Record**

Sometimes we will like to update a record if the record/row exists in the database else insert it. You can do this by calling the `updateOrCreate()` method, passing a key-value array specifying the *where* clause - the column(s) and key(s) that makes each record unique - as the first argument; key-value array containing the table's **column-value** as second argument (what should be inserted or created); and then an optional `table` as final argument. Example:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();

$model->updateOrCreate(
    [
        'email'=>$email
    ], 
    [
        'name' => $name, 
        'email' => $email, 
        'phone' => $tel
    ]
);

or 

$model->updateOrCreate(
    [
        'email'=>$email
    ], 
    [
        'name' => $name, 
        'email' => $email, 
        'phone' => $tel
    ], 
    $table='developers');
```

It searches the database for matching record using the first argument. If a record is found the table will be updated with the data provided in the second argument else a new record will be created by merging the first and second arguments.

---

- ## **Selecting Record**

To select from table simply call the `select()` method. This method accepts an optional array as first argument, like in the above cases, but with specific keys that have special meanings; an optional array of key-value (table "column=>value") **"where"** clause(s) as second argument; and an optional `table` as final argument.

Example:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();

$model->select(
    [
        'limit'=>20,
        'offset'=>3,
        'orderBy'=>'email',
        'groupBy'=>'location'
    ],
);
```

or

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();

$model->select(
    [
        'column'=>'name, email, phone_number, location',
        'limit'=>20,
        'offset'=>3,
        'orderBy'=>'email',
        'groupBy'=>'location',
        'debug'=>true,
    ],
    [
        'location' => 'Nigeria'
    ]
);
```

The expected array keys in the first argument are optional and they are:

- `column`: A string containing columns to select (multiple columns should be coma-delimited). If not specified all the columns will be selected.

- `limit`: An integer containing the `limit` clause

- `offset`: An integer containing the `offset` clause

- `orderBy`: A string containing the name of a particular column the result should be ordered by using the `orderBy` clause

- `groupBy`: A string containing the name of a particular column the result should be ordered by using the `groupBy` clause

- `debug`: If set or present the crafted query will be echoed in the script for inspection.

---

- ## **Counting Record**

To count record simply use the `count()` method. The `count()` method works like the `select()` method above but the only possible array key is just the: `column`. Example:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();

$model->count(
    [
        'column'=>'name, email, phone_number, location'
    ]
);
```

In the above example, the 'column' is specified. This is useful since you can pass the same config array used in a `select()` method call to the `count()` method so that only the `column` key can be picked (as it is all it needs).

You can also specify a `where` clause via the second argument of the method. Example:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();

$model->count(
    [
        'column'=>'name, email, phone_number, location'
    ],
    [
        'email'=>'email@example.com'
    ]
);
```

Or, if without the first argument then it will be:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();

$model->count(
    null,
    [
        'language' => 'php'
    ]
);
```

> Remember that the last argument is the name of the table to query, and it's an optional argument.

---

- ## **Deleting Record**

To delete a record simply call the `delete()` method and pass it an optional key-value array, like in other cases, as first argument and an optional `table` parameter value as final argument. Example:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();

$model->delete([
        'email'=>'fake@example.com'
    ]);
```

Note that if you've already set dynamic properties and then call this method without passing the first argument the dynamic property, or properties, added will be considered and used as the **"where"** clause. Example:

```php
require_once 'path_to_vendor/autoload.php';

use Sirmekus\Database\Model;

$model = new Model();

$model->email = 'fake@example.com';

$model->delete();
```

## Meanwhile

 You can connect with me on [LinkedIn](https://www.linkedin.com/in/sirmekus) for insightful tips and so we can grow our networks together.

 Patronise us on [Webloit](https://www.webloit.com).

 And follow me on [Twitter](https://www.twitter.com/Sire_Mekus).

 I encourage contribution even if it's in the documentation. Thank you, and I really hope you find this package helpful.
