# Section 8 Authentication

## [Build a Registration System in Mere Minutes (ep34)](https://laracasts.com/series/laravel-6-from-scratch/episodes/34?autoplay=true)

### Example Setup

For this example, create a new project, then add the *ui* outside dependency:

```
composer require laravel/ui --dev
```

Then, specify a preset type, in this example Jeffrey uses vue:

```
php artisan ui vue --auth
```

Then install dependencies and compile down assets:

```
npm install && npm run dev
```

The laravel ui package we brought in creates a new */auth* directory in the views directory with views for confirmation, email, reset, login, registration and verification.

*Auth::routes()* was also added to our *web.php file.

### Setting Up The Connection
I will not go into detail here about creating and linking a database in the *.env* file. Once you have done so, you must migrate the database.

### Quick Overview of Middleware
Functions that must be passed through to reach a certain point or state of the application. For ex: a user must be logged in to view the home route.

### Two Ways to use Middleware
One way is by placing the middleware inside your controllers.

#### ex. *inside the HomeController.php file*
```
public function __construct()
{
    $this->middleware('auth');
}
```

Another common way is to reference middleware directly in the routes with chaining.


#### ex. *in web.php*
```
Route::get('/home', [App\Http\Controllers\HomeController::class, 'index'])
    ->name('home')
    ->middleware('auth');
```

### Referencing the Current User
We can reference the logged in user in our views. For example:

#### e. *in the home.blade.php file*
```
You are logged in, {{ Auth::user()->name }}
```

### Displaying View Elements Based on Login Status
Sometimes different views are meant to be displayed based on a user being logged in. To do so, use the *@auth* and *@guest* directives.

#### ex. *@auth*
```
@auth
    Hello {{ Auth::user()->name}}
@else
    Laravel
@endauth
```

#### ex. *@guest*
```
@guest
    Please sign in
@endguest
```