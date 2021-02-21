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