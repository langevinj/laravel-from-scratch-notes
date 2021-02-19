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

### The Edit View

Similarly to the *show* controller, we need to find the item by ID and pass it to the view:

```
public function edit ($id)
{   
            $article = Article::find($id);

            return view('articles.edit', compact('article'));
}
```

We then set form values, or textareas with the corresponding item's instances.

Browsers currently understand POST and GET requests. To specify that we want to send a PUT request, use *@method*.

```
<form method="POST" action="/articles/{{$article->id}}">
        @csrf
        @method('PUT')
```

### The Update Route

#### ex. the route
```
Route::put('/articles/{article}', [ArticlesController::class, 'update']);
```

#### ex. the controller
```
public function update($id)
{
            $article = Article::find($id);
        
            $article->title = request('title');
            $article->excerpt = request('excerpt');
            $article->body = request('body');

            $article->save();

            return redirect('/articles/' . $article->id);
}
```
In this example we redirect the user back to the updated item's view.

---

## [Form Validation Essentials(ep25)](https://laracasts.com/series/laravel-6-from-scratch/episodes/25?autoplay=true)

**Always assume user provided data is malicious.**

Find out more about [Laravel validation in the docs](https://laravel.com/docs/8.x/validation).

### Basic Validation

On the frontend we have access to validation, such as *required*:
```
<input type="text" name="title" class="input" id="title" required>
```

On the server side, a simple example of validation would be:
```
public function store() {

    request()->validate([
            'title' => ['required', 'min:3', 'max:25'],
            'excerpt' => 'required',
            'body' => 'required'
    ]);
}
```

### Displaying Errors
When any validation fails in Laravel, a redirect back to the page occurs and an *errors* variable is populated. 

Then to display an error to the user:
```
@error('title')
        <p class="help is-danger">{{ $errors->first('title') }}</p>
@enderror
```

The *@error* wrapper prevents the *\<p>* from being shown unless there is an error.

### Persisting Correct Input
When some form fields are valid while others aren't, it's a good idea to not erase the passing fields. 

To do so, use *old*:
```
<input type="text" name="title" class="input" id="title" value="{{ old('title') }}">
```

---
## [Leverage Route Model Binding (ep26)](https://laracasts.com/series/laravel-6-from-scratch/episodes/26?autoplay=true)





