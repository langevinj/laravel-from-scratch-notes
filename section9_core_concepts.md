# Section 9: Core Concepts

## [Collections (ep36)](https://laracasts.com/series/laravel-6-from-scratch/episodes/36?autoplay=true)

If you fetch multiple elements you will receive a collection. Continuing the "Articles" example from earlier in the series. Using *Article::all();* will return to you a collection of articles.

To view the methods you can perform on a collection, find the */vendor/laravel/framework/src/Collections/Collection.php* file.

### Creating a Collection From Scratch
Simply use the *collect* method:

```
collect(['one', 'two', 'three']);
```

### Some Commonly Used Methods
- [*filter()*](#filter())
- [*map()](#map())*
- *flatMap()*
- *where()*
- *merge()*

Since a new collection is returned by each, they are all chainable.

### filter()

#### ex.
```
$items = collect([1, 2, 3, 4, 5, 6, 7, 8, 9, 10]);
$items->filter(function ($item) { return $item >= 5;} );
// [5, 6, 7, 8, 9, 10]
```

*filter()* returns a new collection, and doesn't modify the original. If you want to access the collection, you have to save it to a variable.

### map()
Do something to each member of a collection.

#### ex.
```
$items = collect([1,2,3,4,5]);
$items->map(function ($item) { return $item * 3 });
// [3, 6, 9, 12, 15]
```

### Eager Loading
A way to add further data when gathering information.

#### ex. using the *tags()* method to specify which articles to select
```
$articles = Article::with('tags')->get();
```
This returns a collection of all Articles, but each article also has a 'tags' collection.

---
## [CSRF Attacks, With Examples (ep37)](https://laracasts.com/series/laravel-6-from-scratch/episodes/37?autoplay=true)
This section is best understood by watching the video.

From wikipedia: 
>"In a CSRF attack an innocent end user is tricked by an attacker into submitting a web request that they did not intend.

### Broken Img Tag
In this example, the 'target' website uses a GET request for its logout route. The attacker website can add a broken *img* tag to the loading screen of the attacker site, with the source set to the logout route for the target site, meaning on load, it will logout a user.

#### ex. *at the bottom of the attacker welcome page*
```
<img src="http://target.test/logout">
```

### False Button

#### ex. *at the bottom of the attacker welcome page*
```
<form method="POST" action="http://target.test/logout">
    <button>Continue</button>
</form>
```
This can be aided by a hidden AJAX request in the backend. Laravel automatically tries to protect from this by using a token for the actual session.

### @csrf directive

As mentioned in Section 5 on forms, the *@csrf* directive adds a hidden input to, for example, the logout form. This adds another level of verification.

When you are creating a form that submits a POST, PUT, or DELETE request, make it a habit to add *@csrf*.

---
## [Service Container Fundamentals (ep38)](https://laracasts.com/series/laravel-6-from-scratch/episodes/38?autoplay=true)

A container for services. A place to store and retrieve services. 

This lesson rapidly went through creating your own container, without much stopping for explaination. So I would recommend checking out the [Laravel docs on this subject](https://laravel.com/docs/8.x/container)


### An exmaplaination from the docs *(paraphrased from the docs directly)*
The service container is a tool for managing class dependencies, and injecting dependencies into a class via the constructor or "setter" methods.

## [Level 1 - The Best Introduction To the Service Container](https://laravelcoreadventures.com/raiders-of-the-lost-service-container/level/1)

I took a quick sidebar to check out this video for further explaination for the Laravel service container.

Laravel can tell when we need to create an instance of a class and inject it into another method.

#### ex. *taken from this vide*
```
use App\Exporter\UserStatsCsvExporter;

class ExportController extends Controller
{

    public function handle(UserStatsCsvExporter $userStatsExporter)
    {
        return $userStatsExport->export(12);
    }
}
```
So in this example, Laravel will know to create a new instance of the *UserStatsCsvExporter* class.

>"**Providers** are the main place to configure your application. You can use them to register services while Laravel boots up."

### Adding the Provider
You need to register your provider in the */config/app.php* file.

#### ex. *inside the app.php 'providers' array*
```
UserStatsExporterProvider::class
```

Then, inside the provider, in the *register* method, we bind something to the container.

```
public function register()
{
    $this->app->bind(UserStatsCsvExporter::class, function(){
        return new UserStatsCsvExporter(new Translator(config('app.locale)));
    });
}
```
Telling Laravel how to create the instance when we want to inject the class. This mean we want to return a new instance of the *UserStatsCsvExporter* and provide dependencies of the exporter.

 We can also directly ask the container for an instance.

 >'The service container helps us manage our dependencies by:
 >1. providing auto-resolving
 >2. giving us a central place to store instantiation information
 >3. letting us bind to interfaces
 >4. giving us the opportunity to share instances'

---
## [Automatically Resolve Dependencies (ep39)](https://laracasts.com/series/laravel-6-from-scratch/episodes/39?autoplay=true)

### Laravel's Container
Laravel's container is the application itself and can refereneced using the *app()* helper function.

#### ex. *in the web.php file*
```
app()->bind('example', function() {
    return new \App\Example();
});
```

So we now have a new key bound in the service container.

### Fetching a Service

One way is using the *resolve()* helper. 

```
Route::get('/', function() {
    resolve('example');
});
```

The bound method will resolve to whatever is bound to that key. 

***** **The rest of this lesson went right over my head, at this time I don't feel adequately informed enough to take notes elaborating** *****

---

## [Laravel Facades Demystified (ep40)](https://laracasts.com/series/laravel-6-from-scratch/episodes/40?autoplay=true)

Facades provide: a static interface to underlying components in the framework. They are for conveinience: easier than manually building up objects + dependency chains.

```
class PagesController extends Controller
{
    public function home()
    {
        return View::make('welcome');
    }
}
```

So by calling something like this in a controller file, the view factory is being referenced and a *make* method is being called. But, you didn't have to instantiate the factory OR pull in all the dependencies that the object requires.

Facades in Laravel are more testable. 

Each facade has a method that will return a key that represents a binding in the service container.

When you say something like:

```
Request::input('foo')
```

"This is a static interface that proxies to an underlying class."

If you need to debug a call, you can find the underlying class in the facade file, and look at it directly.

When using a facade you don't have to inject any objects inside a public function constructor.

### Warning
Although this is convenient, keep in mind that defining all the classes dependencies in the constructor, it is more clear exactly what is needed for the class to function.

All depends on scope of your project and what conventions you are following.

----
## [Service Providers are the Missing Piece (ep41)](https://laracasts.com/series/laravel-6-from-scratch/episodes/41?autoplay=true)

Each Laravel component is brought in under the */vendor/laravel* directory and each component has a service provider.

These service providers provide a service to the framework.
May need to:
- register keys in the service container
- trigger some functionality after the framework has been booted

### Inside a Component Service Provider

The *register()* method is for registering keys into the service container. The key will now resolve to a new instance of the class.

The *boot()* method is triggered after every single service provider in the framework has been registered.

### Creating a Facade
When creating a facade, you must overide the getFacadeAccessor method, by creating one inside the facade to return the key. Then you must bind that key the App service provider. Then you we be able to use the facade to proxy an underlying class.



