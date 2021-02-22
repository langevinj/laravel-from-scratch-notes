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
# [Simulate an Inbox Using Mailtrap (ep43)](https://laracasts.com/series/laravel-6-from-scratch/episodes/43?autoplay=true)

[Sign up for a free Mailtrap Account](https://mailtrap.io/)

Go to your inbox, and choose "Laravel" from the integration drop down.

Adjust your .env settings accordingly and you are good to go!

---

# [Send HTML Emails Using Mailable Classes (ep44)](https://laracasts.com/series/laravel-6-from-scratch/episodes/44?autoplay=true)

For anything more than simple text emails, we will want something better then the *Mail::raw* approach.

PHP artisan provides a make function for a mail class:
```
php artisan make:mail NAME
```

Now there is a new Mail folder in the app directory, this is where to build the message.

## Changing How an Email is Sent

Follow the steps for creating a mail view, then we setup the new way to send an email in *ContactController.php*:

```
Mail::to(request('email))
    ->send(new ContactMe());
```

Make sure to *use App\Mail\ContactMe*.

## Specific Data in the View
Any public value in the Mailable class will instantly be available in the view.

Now we can initialize the value in the Mailable class constructor, and send it from the Controller.
```
Mail::to(request('email'))
    ->send(new ContactMe('shirts'));
```

### [Here is a list of Mailable methods](https://laravel.com/api/5.5/Illuminate/Mail/Mailable.html)

---
