# The Simply PHP Framewrok
[![Latest Stable Version](https://poser.pugx.org/simplyphp/framework/v/stable)](https://packagist.org/packages/simplyphp/framework)
[![Total Downloads](https://poser.pugx.org/simplyphp/framework/downloads)](https://packagist.org/packages/simplyphp/framework)
[![License](https://poser.pugx.org/simplyphp/framework/license)](https://packagist.org/packages/simplyphp/framework)
[![Monthly Downloads](https://poser.pugx.org/simplyphp/framework/d/monthly)](https://packagist.org/packages/simplyphp/framework)

## About Simple
The Simple PHP is lightweight web application micro framework.
- Uses Model, View, Controller Pattern
- Simple Routing engine
- Using namespace in classes with autoloading
- Controllers with method filters
- Elegant Syntax
- Uses Twig Template Engine (optional)
- Models with resource-friendly connectivity
- Evironment Configuration
- Error Handling and Reporting
- Easily manage dependency using composer

## Demo
http://simply-framework.herokuapp.com/


# Simple PHP dependencies
- [Twig Template Engine](https://twig.symfony.com)
- [Latitude Query Builder](https://github.com/shadowhand/latitude)

# Installation
Via Composer: (recommended) 
```
composer create-project reyjhon/simple-php
cd simple-php
```

# Serving Simple-PHP
PHP-simple doesn't need a real web server to run when in development(experimental).
```
php cli serve
```
or define host and port:
```
php cli serve 127.0.0.1 port=8000
```
then you can now navigate _localhost:8000_ to to your browser. Awesome!

You need a mysql server if your app need to communicate with database. I recommend xampp for windows.
# Using the Simple Framework

## configuration:
Configuraion file is located at: App/Config/global.php
```md
-
|-app
   '-|Config
       '-|global.php
```
## Directory Structure:
```md
The root directory:
|-app
    The App directory:
    '-|Config
    '-|Controllers
    '-|Helper
    '-|Model
    '-|Views
|-database
|-public
|-simply
|-vendor
```
**The App directory:**
- **Config**: This is the folder where the application configuration is located. You can create your own.
- **Controllers:** The _Controllers_ DIR contains all your application controllers. 
- **Helper:** You can create your own class helper in this folder.
- **Model:** All your application models must be place in here.Simple use Models to communicate with the database.
- **Views:** Your Frontend is located here. All html, php files needed for presentation.
- 
**The root Directory**
- **App:** Most of your application logic is here. Eg.Controllers, Models & Views.
- **database:** put your sql files here.
- **public:** The _public_ DIR contains the index.php as the central entry point and front controller of the framework.
- **simply:** Contains logs and cache of your application. This is generated when needed
- **vendor:** Contains all your composer dependencies.

# The Basics
## Routing
The basic url route accepts **_url_**. and parameters: **_controller_**, **_action_**, **_namespace_**:
```php
Router::set('home',[
  'controller' => 'home',
  'action'     => 'index'
]);
```

Parameters can be pass as **_variables_** along with the URL:
```php
Router::set('users/{action}',[
   'controller' => 'UserController'
]);
```

if you want to specify the controller/method in the url:

Example: thesimplephp.com/**controller**/**method**
```php
Router::set('{controller}/{action}');
```
Simple will automatically find the controller and method to run.

Route Optional Parameters: (just add ? to the route variable eg: {id?} 
```php
Router::set('product/{id?}',[
   'controller' => 'ProductController',
   'action' => 'someAction'
]);
```

When specifying controller:
```
'controller' => 'user'
```
is the same as 
```
'controller' => 'UserController'
```

The **_namespace_** parameter tell the router where too look for that controller in the namespace given.
```php
Router::set('admin/{controller}/{action}',[
  'namespace' => 'Admin'
]);
```
And your directory should look like this:
```md
|-App
    '-|Controllers
        '-|Admin
            '-YourController.php
```

Passing Parameters from url to the router. Eg. id,name,prodct etc.
```php
Router::set('users/show/{id:\w+}',[
   'controller' => 'user',
   'action' => 'show'
]);
```

on the variable id, to be able to accept letters and numbers, you need to set the regex(regular expression).

BY default variable only accepts letters.

example: 
```php
{variableName: regex}
```
- **[a-z0-9]+(?:-[a-z0-9]+)*$**   Valid Slug eg: news-report-2019
- **_\w+_**   accepts _letters_ and _numbers_
- **_\d+_**   accepts only _numbers_
- **show** accepts _show_ only
- **\bSHOW|\bEDIT|\bUPDATE** accepts _show_, _edit_ and _update_ (case insensitive).
- You can set your own regex.

## Route Resource
Simply has a route resource that address CRUD in a controller. Instead of declaring mutltiple route for CRUD in a controller
just declare a route resource for your controller.
 ```php
Router::resource('products','ProductController'); 
// you may ommit the word Controller by just using **product** instead of **ProductController**
 ```
 **Actions Handle by Route Resource**
- /products - uses *index*
- /products/create - uses *create*
- /products/store - uses *store*
- /products/show/{id} - uses *show*
- /products/edit/{id} - uses *edit*
- /products/update/{id} - uses *update*
- /products/destroy/{id} - uses *destroy*
 
## USING CLI for _Controller_ & _Model_

Simply PHP has a CLI utility.

To create a controller:
```
php cli make:controller ControllerName
```

To create a model:
```
php cli make:model ModelName
```

## Using CLI for importing database tables(mysql)

To import users table included in the framework:
```
php cli migrate users
```
Additionaly you can import your own .sql file (just put in the database DIR) then:
```
php cli migrate yourSqlFilename
```
Inserting data to users table using cli:
```
php cli user:seed
```
if you're promt to enter a password (Enter mysql server password)

You will be promt to enter the following fields: _name, email and password_.


# Authentication
Simply PHP provides a quick way to scaffold all of the routes and views you need for authentication using one simple command:
```
php cli make:auth
```
### restrict controller to authenticated users only
add the _Action_ suffix into your method name.  

example: if you have a method index in your controller:
```php
   public function index()
```
Make it:
```php
public function indexAction()
```
Then add this to 'App/Controllers/Controller.php':
```php
use App\Helper\Auth\AuthHelper as auth;
use Simple\Request as r;
```
And create a new method _before_ like this:
```php
    public function before()
    {
        if(!auth::user()) {
            r::redirect('/auth/index');
        }
    }
```
The un-authenticated user tries to access your restricted controller will be redirected to login page.

## Using auth in views
If the user is authenticated the user variable is not null.:
```html
  {% if user is null %}
      <p> Please login </p>
  {% else %}
      <p> Hello {{ user.name }} </p>  
  {% endif %}
```
- {{ user.name }} display name of current logged in user.
- {{ user.email }} display email of current logged in user.
- {{ user.id }} display ID of current logged in user.

# Validation
Read documentation at https://github.com/jhayann/simple-php/blob/master/validation.md

# Database Query
Read documentation at https://github.com/jhayann/simple-php/blob/master/queryBuilder.md

# Simply Cryptography 
Simply Framework offers encryption library using a KEY. This uses defuse/php-encryption you can read more at https://github.com/defuse/php-encryption

Before using encryption please run this command **ONCE**:
```
php cli key:generate
```
## Encryption
To encryp a text or string:
```php
use Simple\Security\SimplyEncrypt;
```
Then you can use is as:
```php
$encrypted = SimplyEncrypt::encrypt('secret text');
```
## Decryption
To decrypt a text or string:
```php
use Simple\Security\SimplyDecrypt;
```
Then you can use is as:
```php
$decrypted = SimplyDecrypt::decrypt($ciphertext);
```


## Documentation will be updated soon... 




