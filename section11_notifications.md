# Section 11: Notifications

## [Database Notifications (ep 47)](https://laracasts.com/series/laravel-6-from-scratch/episodes/47?autoplay=true)

[Link to the Laravel docs on notifications](https://laravel.com/docs/8.x/notifications)

>A **database notification** stores the notification information in a databse table.

### Setting up
To use database notifications we need to setup a table:

```
php artisan notifications:table

php artisan migrate
```

You can select one or many channels for the notification:

```
public function via($notifiable)
{
    return ['mail', 'database'];
}
```

### Sending/Storing Specific Data

You can pass specific information from your controller to the Notification class:

```
request()->user()->notify(new PaymentReceived(900));
```

The we are able to access it in the Notifiable class with a few steps:
```
protected $amount

public function __construct($amount)
{
        $this->amount = $amount;
}

public function toArray($notifiable)
{
    return [
            'amount' => $this->amount
    ];
}
```

The type of data can also be varied.

### Presenting a Notification to the User

Create a UserNotificationsController, and add it to the routes:

```
Route::get('/notifications', [UserNotificationsController::class, 'show'])->middleware('auth');
```

In the UserNotificationsController, add a *show()* method and pass the user's notifications.

```
return view('notifications.show', [
             'notifications' => auth()->user()->notifications
         ]);
```

Then adjust the view accordingly.

### Marking Notifications as Read
To start, we can just grab the unread notifications to display, and then mark them as read for the next load. This is just an example, and isn't time-complexity ideal.

```
public function show()
{   
    return view('notifications.show', [
            'notifications' => tap(auth()->user()->unreadNotifications)->markAsRead()
        ]);
}
```
This code snippet uses *higher order tap*. We can call what will return us our value, but then call a method on it as well.