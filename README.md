# laravelNotes



<details>
<summary><b>Table of Contents</b></summary>
 
 - [Creating a Project](#creating-a-project "Creating a Project")

</details>

### Commands

| Function  | Command |
| ------------- | ------------- |
| Laravel Version | php artisan --version |
| Create laravel project  | composer create-project laravel/laravel example-app  |
| Install laravel globally, you can use laravel new testLarvel after  | composer global require laravel/installer  |
| Start local PHP server | php artisan serve |
| List available routes | php artisan route:list |
| Create controller | php artisan make:controller |
| Create controller, migration and model | php artisan make:model Product -rcm |
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


**optional route parameters**

This will match user or user/whatever is passed
```php
$router->get('user[/{name}]', function ($name = null) {
    return $name;
});
```

**regular expression**

```php
$router->get('user/{name:[A-Za-z]+}', function ($name) {
    //
});
```

**passing route params to controller**

- Route::get('/sensorgateway/lrv/{id}', [\App\Http\Controllers\sensorController::class,'show']);


**route groups**

You might need to group routes attributes that are used for more than 1 routes.

ex, when you want to call a middleware in multiple routes.

**grouping with middleware**

```php
$router->group(['middleware' => 'auth'], function () use ($router) {
    $router->get('/', function () {
        // Uses Auth Middleware
    });

    $router->get('user/profile', function () {
        // Uses Auth Middleware
    });
});
```

**grouping when prexing apis**

```php
$router->group(['prefix' => 'admin'], function () use ($router) {
    $router->get('users', function () {
        // Matches The "/admin/users" URL
    });
});
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
 

### Relationships

**one-to-one**

Ex.we need to be able to get user's posts, for that you need to update the post migration table to add a user_id
 $table->integer('user_id')->unsigned();
 
 then in the user model, add this function
 
 ```php
  public function post() {
        return $this->hasOne('App\Models\Post');
    }
 ```
 
 You can now get user's post by calling
 
 - User::find($id)->post;

**inverse relation**

Given a post, get me that post's user info.

from the post model, add this

```php
  public function user() {
        return $this->belongsTo('App\Models\User');
    }
    
Route::get('/post/{id}/user', function ($id) {
    return Post::find($id)->user->name;
});
```

This i will get the post's user name.

**one-to-many**
to get all posts

```php
 public function posts() {
        return $this->hasMany('App\Models\Post');
    }
```

**many-to-many(pivot tables - relating tables together)**

We want to assign roles to users, for that create a roles model

- php artisan make:model Role -m

Now create the pivot table, laravel will understand this is a pivot table if you follow this command convention

- php artisan make:migration create_users_roles_table --create=role_user

- update the roles table, add a name field
- update the role_user table add user_id, role_id


With a many-to-many relationship you can get, insert, update and delete user's roles using the relation.

in the user model add

```php
 public function roles() {
        return $this->belongsToMany('App\Models\Role');
    }
 ```
 
if you follow naming convention, table is named alaphabetically, users_roles, if for example it was named something else then we need to manually define the relationship 
return $this->belongsToMany('App\Models\Role', 'user_role', 'user_id','role_id);
 
 You can now find user's role by user's id 
 
 -    return User::find($id)->roles;

**get**

```php
Route::get('/get', function () {
    $users = User::find(1);
    foreach($users->roles as $role ) {
       return $role;
    }
});
```

**insert**

```php
    $user = User::find(1);
    $role = new Role(['name' => 'administrator']);
    $user->roles()->save($role);
```

**update**

```php
 $user = User::find(1);
    if ($user->has('roles')) {
        foreach($user->roles as $role) {
            if ($role->name ==='administrator') {
                $role->name = 'member';
                $role->save();
            }
        }
    }
 ```
 
 **delete**
 
 ```php
    $user = User::find(1);
    $user->roles()->delete();
```

**attach/detach**

You can add a role using the attach method (even if the role is assigned to the user, this method will still insert the role)
```php
//attach
    $user = User::find(1);
    $user->roles()->attach($id);
    
//detach
     $user = User::find(1);
    $user->roles()->dettach($id);
```
**sync**

You can assign multiple roles to the user using sync method (this method will insert new record in role_user table, doesn't check if the role exist is Roles table)

```php
  $user = User::find(1);
    $user->roles()->sync([1,2,3,4,5,6]);
```

In the above call, the user will be asigned the roles [1,2,3,4,5,6], if they user has other roles asigned, they will get removed.


**has-many-through**

ex. create a new country table, update user's table to have a country_id. (posts table is already linked to users)
from the country model we can add this ft.

given a country_id, i can get a user's posts


In the below ft., to get the post, i need to go through the user. In this case, laravel will automatically look for country_id in user table.

In the country model, add this ft.

```php
  public function posts() {
        return $this->hasManyThrough('App\Models\Post', 'App\Models\User');
        //  return $this->hasManyThrough('App\Models\Post', 'App\Models\User', 'country_id', 'user_id'); // no need to add those primary keys if they are default, laravel will find them.
    }
 ```

```php
Route::get('/user/country/{id}', function ($id) {
    $country = Country::find($id);
    foreach($country->posts as $post) {
        return $post->name;
    }
});
```

**polymorphic-relation**

You can have users table and posts table relate to a photos table.

To do so create a new Photos model with migration

```php
   $table->string('path');
   $table->integer('imageable_id'); // laravel convention
   $table->string('imageable_type'); // this should be the model the photo related to, it could be any model.
```

In the user's model you can add this function that will return photos by this user.

```php
 public function photos() {
        return $this->morphMany('App\Models\Photo', 'imageable');
    }
```

in the photos table, the field Photo.imageable_type should be the path the the model this photo related to ex. App\Models\Post or App\Models\User

To get photos by a post, you can do the same for a user.

```php
Route::get('/post/photo/{id}', function($id) {
    $post = Post::find($id);
    return $post->photos;
});
```

**polymorphic-relation-inverse**
To get the Photos related Model record, you can do this

```php
Route::get('/photo/{id}/post', function($id) {
    $photo = Photo::findOrFail($id);
    return $photo->imageable;
});
```

//photo->imageable; gets the related record.

### Tinker

You use tinker to directly make calls to models for ex, to create, delete or update records

- $post  = App\Models\Post::find(1)->delete()
- $post  = App\Models\Post::create(['name' => 'test name', 'content' => 'test content from tinker'])


### Caching

By default laravel is configured to use file cache, you can change that from /config/cache

**get**
// Here we try to look for a cache using the key and by selecting "file" as the file for cache lookup.
- $value = Cache::store('file')->has("$lookupKey");

**store**

// cacheKey, cacheValue, #mins
- Cache::put("$lookupKey", $valueDb, 525600);
      
**delete-all**

- Cache::flush();

**delete-key**
 
- Cache::forget("$lookupKey");

### Query Builder

**retreiving all rows**

```php
use Illuminate\Support\Facades\DB;

$users = DB::table('users')->get();

foreach ($users as $user) {
    echo $user->name;
}
```

**retreiving a single row**

```php
$user = DB::table('users')->where('name', 'John')->first();
```

or if you want to retreive the value of a column

```php
$email = DB::table('users')->where('name', 'John')->value('email')
```

**retreiving single row by id column**

```php
$user = DB::table('users')->find(3);
```

**retreiving a list of column values**

In this example, you will retreive a collection containg only title column

```php
$titles = DB::table('users')->pluck('title');

foreach ($titles as $title) {
    echo $title;
}
```

**chunking results**

This method retrieves a small chunk of results at a time and feeds each chunk into a closure for processing.
You may stop the processing of chuncks by retuning false.
```php
DB::table('users')->orderBy('id')->chunk(100, function ($users) {
    foreach ($users as $user) {
        //
    }
});
```

**aggregates**

```php
$users = DB::table('users')->count();

$price = DB::table('orders')->max('price');
```

**determining if record exists**

```php
if (DB::table('orders')->where('finalized', 1)->exists()) {
    // ...
}

if (DB::table('orders')->where('finalized', 1)->doesntExist()) {
    // ...
}
```

**selects**

You may not always want to select all columns

```php
$users = DB::table('users')
            ->select('name', 'email as user_email')
            ->get();
```

**distinct**

```php
$users = DB::table('users')->distinct()->get();
```

**adding to already created query builder instance***

```php
$query = DB::table('users')->select('name');

$users = $query->addSelect('age')->get();
```

**raw expressions**

You may sometimes need to insert arbitrary strings into a query, these statements are injected into the query, so you need to be very careful to avoid creating sql injection vulnerabilities.

```php
$users = DB::table('users')
             ->select(DB::raw('count(*) as user_count, status'))
             ->where('status', '<>', 1)
             ->groupBy('status')
             ->get();
 ```
 
 **raw methods**
 
 The selectRaw method can be used in place of addSelect(DB::raw(...)). This method accepts an optional array of bindings as its second argument.
 
 ```php
 $orders = DB::table('orders')
                ->selectRaw('price * ? as price_with_tax', [1.0825])
                ->get();
```

**whereRaw/orWhereRaw**

```php
$orders = DB::table('orders')
                ->whereRaw('price > IF(state = "TX", ?, 100)', [200])
                ->get();
```

**havingRaw / orHavingRaw**

```php
$orders = DB::table('orders')
                ->select('department', DB::raw('SUM(price) as total_sales'))
                ->groupBy('department')
                ->havingRaw('SUM(price) > ?', [2500])
                ->get();
```

**orderByRaw**

```php
$orders = DB::table('orders')
                ->orderByRaw('updated_at - created_at DESC')
                ->get();
```

### Telescope Configuration

Telescope provides insight into the requests coming into your application, exceptions, log entries, database queries, queued jobs, mail, notifications, cache operations, scheduled tasks, variable dumps, and more.

- composer require laravel/telescope --dev (local only)
- php artisan telescope:install
- php artisan migrate

from config/app.php remove TelescopeServiceProvider service provider registration
manually register Telescope's service providers in the register method of your App\Providers\AppServiceProvider class. We will ensure the current environment is local before registering the providers:

```php
/**
 * Register any application services.
 *
 * @return void
 */
public function register()
{
    if ($this->app->environment('local')) {
        $this->app->register(\Laravel\Telescope\TelescopeServiceProvider::class);
        $this->app->register(TelescopeServiceProvider::class);
    }
}
```

Finally, you should also prevent the Telescope package from being auto-discovered by adding the following to your composer.json file:

```json
"extra": {
    "laravel": {
        "dont-discover": [
            "laravel/telescope"
        ]
    }
},
```

**config**

'enabled' => env('TELESCOPE_ENABLED', true),

**localhost fix**

copy layout.blade.php from  

project_directory/vendor/laravel/telescope/resources/views/layout.blade.php

to

project_directory/resources/views/vendor/telescope/layout.blade.php 

update 

```
<!-- Global Telescope Object -->
<script>
    window.Telescope = @json($telescopeScriptVariables);

    var telescopePath = "{{ ltrim(parse_url(url(config('telescope.path')))['path'], '/') }}";
    window.Telescope.path = telescopePath;
</script>
```
OR TRY  php artisan vendor:publish (this should be able to copy to vendor directory)

### Scheduler
In the past, you may have written a cron configuration entry for each task you needed to schedule on your server.
The scheduler allows you to fluently and expressively define your command schedule within your Laravel application itself.When using the scheduler, only a single cron entry is needed on your server. 

From app/Console/Kernel.php

```php
  /**
     * Define the application's command schedule.
     *
     * @param  \Illuminate\Console\Scheduling\Schedule  $schedule
     * @return void
     */
    protected function schedule(Schedule $schedule)
    {
        // $schedule->command('inspire')->hourly();

        $schedule->call(function () {
            // Schedule to delete telescope entries.
            DB::table('telescope_entries')->delete();
            DB::table('telescope_entries_tags')->delete();
            DB::table('telescope_monitoring ')->delete();
        })->everyFourMinutes();
    }
    
 ```
 
 to run the scheduler worker to test this , you can 
 
 - php artisan schedule:work

In reality, you need to create a cron job on the server for this.

You can run a cron on the server for ex. * * * * * cd /path-to-your-project && php artisan schedule:run >> /dev/null 2>&1



### Queue

For web requests tasks that require long time to complete, you can send the long running tasks to a queue. A can use Amason SQS, Redis or even a database queue.

**Sync queue**

For local developmene, you can use the synchronus queue to send jobs immedietly. This is only used for development purposes.

**default queue**

Each connection configuration in queue config file, contains a queue attribute which is the default queue that will be used the default configuration will be used if when you dispatch a job, no queue is seleted.


**multiple queues**

You may want to have multiple queues, laravel allows you to specify a priority for each queue.Laravel queue workers will then process jobs by priority.


php artisan make:job ProcessPodcast The generated class will implement the shouldQueue interface, indicating to Laravel that the job should be pushed onto the queue to run asynchronously.

**VERY IMPORTANT**

run php artisan config:clear when changing any config to take effect

**running the queue**

php artisan queue:listen

When this is run, whatever jon in the jobs table will be executed, then removed from the table, if for any reason the job couldnt be executed, then there record will stay in the jobs table and its attempts will be incremented.

This should be configured as a cron job.



