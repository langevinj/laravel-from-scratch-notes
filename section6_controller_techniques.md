# Section 6 Controller Techniques

## [Leverage Route Model Binding (ep26)](https://laracasts.com/series/laravel-6-from-scratch/episodes/26?autoplay=true)

**findOrFail()**
<br>
If the item is not found, return a 404.

### Finding an Item 
Instead of manually finding an item associated with an identifier, we can leverage Laravel's binding:

```
public function show (Article $article)
{
    return view('articles.show', ['article' => $article]);
}
```
Your wildcard name is important, it must match your variable name. 

### Finding an Item Not Using a Primary Key
To find an item using a characteristic other than the id, you must overwrite the *getRouteKeyName()* method in the associated model.

```
class Article extends Model
{
    public function getRouteKeyName()
    {
      return 'slug';  // Article::where('slug', $article)->first();
    } 

}
```
---

## [Reduce Duplication (ep27)](https://laracasts.com/series/laravel-6-from-scratch/episodes/27?autoplay=true)

### Cleaning up *store()*
The *Article::create()* function can cleanup insertion significantly:
```
Article::create([
            'title' => request('title'),
            'excerpt' => request('excerpt'),
            'body' => request('body')
]);
```

For this to work however, we either have to declare what is fillable:
```
class Article extends Model
{
    protected $fillable = ['title', 'excerpt', 'body'];

}
```

OR, as long as you are protecting what the user is allowed to input, you can simply choose to turn off what is guarded:
```
class Article extends Model
{
    protected $guarded = [];

}
```

We've cleaned up the creation method, but our validation leaves us with a lot of repeated code. Fortunately, we can inline the validation, because *validate* returns the validated items.

So now our *store()* method can just be:

```
 public function store ()
    {

        Article::create(request()->validate([
            'title' => ['required', 'min:3', 'max:25'],
            'excerpt' => 'required',
            'body' => 'required'
        ]));

        return redirect('/articles');
    }
```

### Cleaning up Update
The same process is of refactoring done for *store()* can be applied to *update()* with one minor exception. The *::create()* method must switched to *update()* because there is already an instance of the item.

```
$article->update(request()->validate([
                'title' => ['required', 'min:3', 'max:25'],
                'excerpt' => 'required',
                'body' => 'required'
        ]));
```

----

## [Consider Named Routes](https://laracasts.com/series/laravel-6-from-scratch/episodes/28?autoplay=true)

What happens if a URI changes later? You'd have to rewrite an instances of it.

### Naming a Route
```
Route::get('/articles/{article}', [ArticlesController::class, 'show'])->name('articles.show');
```

### Directing to the Route
```
<h3><a href="{{ route('articles.show', $article) }}">{{ $article->title }}</a></h3>
```
You can also explicitly pass *$article->id* as the second parameter, which indicates the wildcard. But Laravel will automatically determine this.

### Adding a *path()* method
An optional way to clean up the process of redirecting to a certain resource is to add a *path()* method to the resources associated model.

```
class Article extends Model
{
    public function path()
        {
            return route('articles.show', $this);
        }
}
```

For example, this allows for some minor refactoring when directing towards a specific item:

```
return redirect($article->path());
```
