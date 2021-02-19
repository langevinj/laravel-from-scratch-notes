# Section 7 Eloquent

## [Basic Eloquent Relationships(ep29)](https://laracasts.com/series/laravel-6-from-scratch/episodes/29?autoplay=true)

Say you have a User model. A user can have many projects and many articles. An article can be written by a user, and a project can be created by a user. How do we access these instances? Luckly there are some useful methods to do just that.

### hasOne
The first argument passed to the hasOne method is the name of the related model class.

```
class User extends Model
{
    public function phone()
    {
        return $this->hasOne(Phone::class);
    }
}
```

### hasMany
A User can have many articles. This method runs a query selecting from the articles table all articles where the *user_id* is that of the specific user.

```
class User extends Model
{
    public function articles()
    {
        return $this->hasMany(Article::class);
    }
}
```


### belongsTo
Say a project belongs to user, we can use this method to retrieve the user that the project belongs to. This means that the Project table must have a *user_id* connection to the user. When *belongsTo* is run on *$this*, the query is looking for the User associated with the project found at *$this->$project_id*.

```
class Project extends Model
{

    public function user()
        {
            return $this->belongsTo(User::class);
        }
}
```

---
