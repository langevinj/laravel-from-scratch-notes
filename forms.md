# Section 5 Forms

## [The Seven RESTful Controller Actions (ep21 + 22)](https://laracasts.com/series/laravel-6-from-scratch/episodes/21?autoplay=true)

### Index
Should render a list of resources.

#### ex. list of articles, list of users, list of projects
-  *GET /users*

### Show *(id)*
Show a single resource.

#### ex. article with *id*, specific user
- *GET /users/id*

### Create
Shows a view to create a new resource.
- *GET /users/signup*

### Store
A way to persist a new resource.
- *POST /users*

### Edit
Show a view to edit an existing item or resource.
- *GET /users/id/edit*

### Update
Persist the edited resource.
- *PUT /users/id*

---
### Destroy
A way to delete a resource entirely.
- *DELETE /users/id*

## Generate a resource controller class
Use the *-r* flag to automatically generate all seven RESTful actions inside a controller as you create it:
```
php artisan make:controller CONTROLLERNAME -r
```

You can also add the *-m* flag to specify the associated model:
```
php artisan make:controller CONTROLLERNAME -r -m MODELNAME
```
##### *if the model doesn't exist, you will be prompted if you would like to create one*
---

## [Form Handling (ep23)](https://laracasts.com/series/laravel-6-from-scratch/episodes/23?autoplay=true)

### Order matters
Make sure to place routes with wildcards AFTER routes without, otherwise there may be an error in which route is matched.

#### ex. *Correctly ordered routes*
```
Route::get('/articles', [ArticlesController::class, 'index']);

Route::get('/articles/create', [ArticlesController::class, 'create']);

Route::get('/articles/{article}', [ArticlesController::class, 'show']);
```

By laying routes out in this order, a conflict between */{articles}* and */create* does not occur.

### The Form
Once you create an HTML form, make sure to insert the correct verb and route:
#### ex.
```
<form method="POST" action="/articles">
```

You'll also have to add a CSRF for protection inside the form, otherwise the site will throw a 419 error:
```
@csrf
```
### Storing the data
Since we have access to the request helper, we can use the helper to give us the data we need. 

A raw example of doing this:
```
public function store ()
{
        $article = new Article();
        
        $article->title = request('title');
        $article->excerpt = request('excerpt');
        $article->body = request('body');

        $article->save();

        return redirect('/articles');
}
```
It's common practice to redirect the user after a form submission.

It's important to give a proper name to form input elements, as the values of these fields will be gathered later by referencing these names.

---

## [Forms That Submit PUT Requests(ep24)](https://laracasts.com/series/laravel-6-from-scratch/episodes/24?autoplay=true)

