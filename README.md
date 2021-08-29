# laravelNotes



<details>
<summary><b>Table of Contents</b></summary>
 
 - [Creating a Project](#creating-a-project "Creating a Project")

</details>



### Windows env mySQl

To add mySQL to windows environemt variables, from window, search for .env, open "edit the systen environemt variable" and add the location of mysql executable (if installed using xampp, it can be c:\Program Files\xampp\mysql\bin)
Add a new entry to PATH.

### Creating a laravel project


- composer create-project laravel/laravel example-app

**Global Installer**

- composer global require laravel/installer

next time you need to create a project, you can issue this command

- laravel new testLarvel

**Install a specific laravel version**

- composer create-project laravel/laravel="7.*" myProject

### Local artisan PHP server

- php artisan serve

This command will run a local PHP server.

### Folder Structure

- **app**: main folder, this is where we keep our models, controllers
- **bootstrap**: this is where the application starts
- **config**: all our configuration files are located (database, mail, auth)
- **database**: this is where we keep our migration files, tables, files to test our application
- **public**: this is where your public files are located (html, css, js, index)
- **resources**: the files that need to be compiled using webpack. This will also have our application views.
- **routes** 
  - **web**: will contain your app's routes
  - **api**: routes for your apis
- **vendor**: These will be libraries brought in by composer
- **.env**: This will contain all out sensitive info (database connection, username, password for sending emails, connection to aws), this is usually ommitted in the gitIgnore file, users won't usually see this file on the server becuase it has a dot.        
- **artisan**: a program that we use to generate controllers, models, tables
- **composer.json**: everytime you download something, composer will have a referece to it.
- **package.json**: npm dependencies
- **server.php**: program that wraps around php server, allows you to run a php server for your application (php artisan serve)
- **webpack.min.js**: a laravel wrapper for webpack



### Routing

**old laravel version**
app/http/routes

**new version**
routes/web.php


**adding a route**

From web.php add

```php
Route::get('/admin/{id}/{name}', function($id,$name) {
return "id is $id, name is $name";
});
```


**listing available routes**

- php artisan route:list








