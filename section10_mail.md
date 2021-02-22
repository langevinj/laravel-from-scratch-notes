# Section 10: Mail

# [Send Raw Mail (ep42)](https://laracasts.com/series/laravel-6-from-scratch/episodes/42?autoplay=true)

## Setup
To setup a project for this video I recommend creating a new project, adding authentication as outlined in Section 8. Then following user "WLeyes" comment on the video for setting up a contact view. I will be proceeding under the assumption you have done so.

Next, make sure to create a controller and model:

```
php artisan make:controller ContactController -r -m Contact
```

Remember to adjust your *MAIL_MAILER* in the .env file to *log* for this exercise.

## Adding Validation
In the ContactController, we can add valiation:

```
public function store()
{
    request()->validate(['email' => 'required|email']);
}
```

Then, you can display the message on the frontend.

## Sending the Email
The simplest way to send an email is to use the mail facade:

```
public function store()
{
    request()->validate(['email' => 'required|email']);

    Mail::raw('It works!', function($message) {
        $message->to(request()->email)
                ->subject('Hello There');
    });

    return redirect('/contact');
}
```

## Specifying the Sent From Email
This can be done with a *->from* method or in the */config/mail.php* file.

## Showing Success
On the backend, we send back a message to the contact page:
```
return redirect('/contact')
        ->with('message', 'Email Sent!');
```

Then display the message on the frontend:
```
@if (session('message'))
    {{ session('message') }}
@endif
```

---
# [Simulate anInbox using Mailtrap (ep43)](https://laracasts.com/series/laravel-6-from-scratch/episodes/43?autoplay=true)

