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
## [Roles and Abilites (ep54)](https://laracasts.com/series/laravel-6-from-scratch/episodes/54?autoplay=true)

### Background

Think of the setup of users for a website. A moderator can edit, a manager can edit AND make design changes, and an owner can do anything, including actions noone else can. In this case, users has multiple roles, which have different abilities.

### Setup
Begin by creating a roles migration.

#### ex. *taken from this episode*
```
public function up()
{
    Schema::create('roles', function (Blueprint $table) {
        $table->bigIncrements('id');
        $table->string('name');
        $table->string('label')->nullable();
        $table->timestamps();
    });

    //copy of the above schema, but for 'abilities'

    Schema::create('ability_role', function (Blueprint $table) {
        $table->primary(['role_id', 'ability_id']);
        $table->unsignedBigInteger('role_id');
        $table->unsignedBigInteger('ability_id');
        $table->timestamps();

        $table->foreign('role_id')
            ->references('id')
            ->on('roles')
            ->onDelete('cascade');

        $table->foreign('ability_id')
            ->references('id')
            ->on('abilities')
            ->onDelete('cascade');
    });

    //copy of above pivot table, but for 'role_user'
}
```

### The Role and Ability Models
#### ex. *inside the Role model*
```
public function abilities()
{
    return $this->belongsToMany(Ability::class);
}
```
Then do the inverse in the Ability model.

### The User Model
Make sure there is a way to get the roles:

```
public function roles()
{
    return $this->belongsToMany(Role::class)->withTimestamps();
}
```
Because there are timestamps on this table, you must specify using the *withTimestamps()* method.

### Assigning a role to a User

#### ex. *inside the User model*
```
public function assignRole($role)
{
    $this->roles()->save($role);
}
```

### Displaying Based on Permission
If we want to continue using the *@can* component, we have to hook these abilities into Laravel's Gate functionality.

In the AuthServiceProvider we add a Gate to check if the user has a particular ability. The ability name will be passed by the form.

#### ex. *in the AuthServiceProvider*
```
public function boot()
{
    $this->registerPolicies();

    Gate::before(function ($user, $ability) {
        return $user->abilities()->contains($ability);
    });
}
```

### Changing the *assignRole* Method
Instead of checking if a role is already assigned to a user, then not doing anything, we can call *sync*, which will replace all entries in the pivot tables with a particular collection. In this case we need to specify *false*, otherwise anything not associated with the collection would be dropped.

#### ex. *inside the User model*
```
public function assignRole($role)
{
    $this->roles()->sync($role, false);
}
```
This will add any new records if necessary, but it won't drop anything.


---