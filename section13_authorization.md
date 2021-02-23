# Section 13: Authorization

## [Limit Access to Authorized Users (ep50)](https://laracasts.com/series/laravel-6-from-scratch/episodes/50?autoplay=true)

### Representing Authorization in Blade
Surround particular buttons or sections:
```
@can ('update-conversation', $conversation)
    <form action="#">
        <button></button>
    </form>
@endcan
```

"If the current use can update-conversation the current conversation, then display the button."

### Updating Authorization
You can update authorization in the AuthServiceProvider file.

#### ex. *inside the boot() method*
```
Gate::define('update-conversation', function (User $user, Conversation $conversation){
        return true;
});
```

You can also add a *?* in front of 'User' to make the authorization optional.

### Checking the Specific User
Once a *user()* method has been defined on the class with an instance we are handling, we can check that the specific user has the correct authorization:

```
Gate::define('update-conversation', function (User $user, Conversation $conversation){
        return $conversation->user->is($user);
});
```

### Setting Up the Correct Routes
You want to avoid anyone sending a POST request to the server that is not authorized.

If you cannot one of the standard action names, create a new controller and return to one of those action names.

#### ex. *from the video* 
```
php artisan make:controller ConversationBestReplyController

*inside the controller*

public function store(Reply $reply)
{
    $this->authorize('update-conversation', $reply->conversation);

    $reply->conversation->best_reply_id = $reply->id;
    $reply->conversation->save();

    return back();
}
```

### Handling Non-Validated Users
You can handle denials of authorization:

```
if(Gate::denies('update-conversation', $reply->convertion))
```

### Policies
```
php artisan make:policy ConversationPolicy --model=Conversation
```

A policy is a class that encapsulates and authorization policy for a model.

Now we can move the authorization into the policy model the same way as in the previously explored controller:
```
public function update(User $user, Conversation $conversation)
{
    return $conversation->user->is($user);
}
```

Because this specific policy is created, we can completely remove this from the AuthServiceProvider. Then, in the main controller, change the auth name:

```
$this->authorize('update', $reply->conversation);
```

---
## [Authorization Filters (ep51)](https://laracasts.com/series/laravel-6-from-scratch/episodes/51?autoplay=true)

You can add a *before()* method to a policy that will fire before the actual authorization is checked.

#### ex.
```
public function before(User $user){
    if($user->admin === true){
        return true;
    }
}
```

Do not just return the condition, it will interfere with the flow.
For some authorizations, like admin checks, you can move them to the AuthServiceProdiver if they will be called regularly. 

---
## [Guessing the Ability Name (ep52)](https://laracasts.com/series/laravel-6-from-scratch/episodes/52?autoplay=true)

A simple discussion of ability names, watching the video is the best course.

---
## [Middleware-Based Authorization (ep53)](https://laracasts.com/series/laravel-6-from-scratch/episodes/53?autoplay=true)

We can authorize on the routing level as a middleware.

```
Route::get('/conversations/{conversation}', [ConversationsController::class, 'show'])->middleware('can:view, conversation');
```

Leverages route model binding to pass the conversation.

---

