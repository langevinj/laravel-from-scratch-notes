# Section 12: Events

## [Eventing Pros and Cons (ep49)](https://laracasts.com/series/laravel-6-from-scratch/episodes/49)

>An **event** represents an action that just took place in your system.

**Listeners** will be classes that perform some action when an event is triggered.

### Some PHP Artisan Commands
```
php artisan event:list
```

Displays a list of all events across your application as well as their listeners.

To create an event simply:
```
php artisan make:event EventName
```

To create a listener:
```
php artisan make:listener ListenerName -e EventName
```

### Inside the Models

All properties inside the Event model should be public.

Raise that the event has happend in the controller:

```
public function store()
{
    ProductPurchased::dispatch('toy');
}
```

In the listener, the *handle()* method performs the action. This is where you should place your listeners action.

### Linking the Event and Listener
The event and listener must be linked in the EventServiceProvider.

```
protected $listen = [
    Registered::class => [
        SendEmailVerificationNotification::class,
    ],
    ProductPurchased::class=> [
        AwardAchievements::class
    ]
];
```

To make things easier, we can override a function called *shouldDiscoverEvents()* in the EventServiceProvider. This means Laravel will automatically scan the Listener directory.

```
public function shouldDiscoverEvents()
{
    return true;
}
```

---
