# laravelNotes



<details>
<summary><b>Table of Contents</b></summary>
 
 - [Creating a Project](#creating-a-project "Creating a Project")

</details>

### Commands

| Function  | Command |
| ------------- | ------------- |
| Laravel Version | php artisan ==version |
| Create laravel project  | composer create-project laravel/laravel example-app  |
| Install laravel globally, you can use laravel new testLarvel after  | composer global require laravel/installer  |
| Start local PHP server | php artisan serve |
| List available routes | php artisan route:list |
| Create controller | php artisan make:controller |
| Create Controler with default CRUD methods | php artisan make:controller --resource PostController |
| Create a new migration/table| php artisan make:migration create_posts_table --create="posts" |
| Run the migration file | php artisan migrate |
| Available artisan commands | php artisan|
| Rollback to the previous migration file | php artisan migrate:rollback |
| Add column to existing table | php artisan make:migration add_is_admin_column_to_posts_table --table="posts"  |
| Reset migration files | php artisan migrate:reset |
| Check which migration files already ran | php artisan migrate:status |
| ------------- | ------------- |
| ------------- | ------------- |
| ------------- | ------------- |
| ------------- | ------------- |
| ------------- | ------------- |
| ------------- | ------------- |
| ------------- | ------------- |
| ------------- | ------------- |
| ------------- | ------------- |
| ------------- | ------------- |
| ------------- | ------------- |
| ------------- | ------------- |

### Laravel version

= php artisan --version

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



### Routes

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

**named route**

You can view the routes using php aritsan route:list
```php
Route::get('/admin/posts/example' ,array('as' => 'admin.home', function(){

    $url = route('admin.home');
    return "this url is $url";
}));
```
**listing available routes**

- php artisan route:list

**calling controller from route**

```php
Route::get('/about/{id}', '\App\Http\Controllers\PostController@index');
```

**resource routes**

You can create resource routes

- Route::resource('posts', '\App\Http\Controllers\PostController');

try php artisan route:list

This route will automatically route posts/create posts/{postID} .. (you can check the available route when listing)

### Controllers

Controllers are like a middleman between the model and the view.

- php artisan make:controller
- php artisan make:controller --resource PostController(this will add all CRUD methods by default to your newly created controller)

### Views

Laravel using blade templating engine.

```php
  public function contact() {
        return view('contact');
    }
```

This will render the contact view.

**passing variables to view**

**Passing 1 variable**

```php
public function showPost($id, $name, $password) {
       return view('post')->with('id', $id);
}
```
**example route used**

```php
Route::get('post/{id}/{name}/{password}', '\App\Http\Controllers\PostController@showPost');
```

**Preferred method, passing multiple variable**

Laravel will automatically transfer the var names into variables and pass it to the view
```php
  public function showPost($id, $name, $password) {
        return view('post', compact('id','name','password'));
    }
```

You can read the values from the view using 

```<h1>Post id is {{$id}} name is {{$name}} password is {{$password}}</h1>```


**blade loops and conditionals**

```blade
 <h1>Contact Page</h1>
    @if(count($people))
        <ul>
        @foreach($people as $person)
            <li>{{$person}}</li>
        @endforeach
        </ul>
    @endif
```

### .env .env.example

When you ship your application, you want users to know which application configuration to use/setup. You include .env.example file with default configuration.
You can keep your sensitive passworrds and logins in .env for your local development.


### Migration

**create table**
Create a database, and set the correct credentials in .env file.

Run the command, this will create a new migration file, for posts table.

- php artisan make:migration create_posts_table --create="posts"

head over to the newly created migration file, you can add new fields to the table, ex.

```php
   $table->string('title');
   $table->text('body')
 ```
 **update table fields**
 once done, issue this command to add those fields
 
 - php artisan migrate

**rollback to previous migration**

- php artisan migrate:rollback

**adding columns to existing table**

- php artisan make:migration add_is_admin_column_to_posts_table --table="posts" 

now go to the created migration file, add the new column, also include the column in the down() ft. 

```php
$table->dropColumn('isAdmin');
```

After that, issue the command, php artisan migrate, the new column should now be added without having to drop the table and its records.


**migration reset**

To clear all migration files

- php artisan migrate:reset

**updating fields without dropping everything**

If you update a field ex default values changes, you can issue this command to update the migration

- php artisan migrate:refresh

**migration status**

To know which migration files already ran

- php artisan migrate:status


### Raw SQL Queries

```
use Illuminate\Support\Facades\DB;
```

```php
Route::get('/insert/', function() {
    DB::insert('insert into posts(title,body) values(?,?)', ['php with laravel', 'laravel framework']);
});


Route::get('/get/', function() {
    $result = DB::select('select * from  posts where id = ?', [1]);
    foreach($result as $post) {
        return $post->title;
    }
});
```

### Models

- php artisan make:model PostModel -m (-m if you need to create its migration)

Model name will be the table name in the database, so if you create a Post model, and run migration, it will create the post table.

You can override the table name in the model by adding a protected property $table = 'tableName';
You can also override $primaryKey the same way.

### Eloquent / ORM

**get**

```php
  $result  = sensors::where('id',1)->orderBy('id','desc')->take(1)->get();
```

**insert**

```php
  $sensor = new sensors();
  $sensor->name = 'test sensor';
  $sensor->save();


You can also use a shorthand method 

```php
   sensors::create(['name' => 'this is a name']);
 ```

But in this case you need to add a property to the sensor model in this case to tell it that the name property is fillable.

```php
  protected $fillable = [
        'name'
    ];
 ```

**update**

```php
    $sensor = sensors::find($id);
    $sensor->name = $name;
    $sensor->save();
 ```
 or
 
 ```php
  sensors::where('id', 2)->where('name', 'test sensor')->update(['name'=>'test sensor updated']);
 ```
 
 **delete**
 
 ```php
  $post = sensors::find(1);
    $post->delete();

```

or

```php
 sensors::destroy(2);
```

**soft delete**

Soft delete will keep a copy of the record in the trash, to implement this feature, you need to add a deleted_at field to the model then implement the soft delete.

add this propert

```php  
   use Illuminate\Database\Eloquent\SoftDeletes;
   use SoftDeletes;
  
  protected $date = ['deleted_at'];
```
 run migrate to add the newly added deleted_at field.
 
 now anytime you call the delete/destroy method, the record will still be in the db with a deleted_at.
 
 **restore a soft deleted record**
 
  return sensors::withTrashed()->where('id', 3)->restore();
  
  
 **hard delete**
 
 If soft delete is implemented, then to hard delete you need to force delete it.
 
 - sensors::withTrashed()->where('id', 3)->forceDelete();
 
