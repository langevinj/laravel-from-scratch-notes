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
---

## [The Password Reset Flow (ep35)](https://laracasts.com/series/laravel-6-from-scratch/episodes/35?autoplay=true)

### What Happens When a User Clicks "Reset Password"?
1. Click "Forgot Password"
2. Fill out a form with their email address.
3. Prepare a unique token and associate it with the user's account.
4. Send an email with a unique link back to our site that confirms email ownership.
5. Link back to website, confirm the token, and set a new password.

### Setting Up Email
All the email variables are located in the *.env* file with the *MAIL_* prefix. More of the email configuration can be found in */config/mail.php*.

For this example, we are setting *MAIL_MAILER* to *log*, so we can view the email that would be sent in */storage/logs*.

Because Laravel handles this path we don't have to set everything up ourselves.