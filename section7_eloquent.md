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
## [Understanding Foreign Keys and Database Factories(ep 30)](https://laracasts.com/series/laravel-6-from-scratch/episodes/30?autoplay=true)

### Foreign Keys
When adding a foreign key, make sure the type of the key matches the one it is referencing.

#### ex. the user migration
```
$table->bigIncrements('id');
```

#### ex. the articles migration:
```
$table->unsignedBigInteger('user_id');
```

The primary key for User is automatically set to a big integer, so we had to match it.

### Having Dummy Data
Useful for all sorts of testing.

Creating of dummy data can be found in the \database\factories directory.

For example, to create a factory for the user, use the following commands:
```
php artisan tinker

User::factory()->create();
```
To create multiple instances you can pass the number of desired dummies into factory:
```
User::factory(5)->create();
```

### Creating a Factory
To create a factory, use the make:factory command followed by the model flag to link the factory to the correct model:

#### ex.
```
php artisan make:factory ArticleFactory -m Article
```

After the factory is created and linked to the model. We have to make sure the columns are being correctly mocked.

Full documentation on the Faker PHP library can be found [here](https://github.com/fzaninotto/Faker).

### Adding Defaults for Dummy Data
Inside a factory, you will need to add dummy data. For example in the ArticleFactory:

```
public function definition()
{
        return [
            'user_id' => User::factory(),
            'title' => $this->faker->sentence,
            'excerpt'=>$this->faker->sentence,
            'body'=>$this->faker->paragraph,
        ];
}
```
Because the *user_id* must reference a specific user instance, we call the User factory.

### Specifying Parts of Test Data
You can add specific properties by passing them into the *create()* method:

```
Article::factory()->create(['title'=>'I DID IT']);
```

### Cascading Deletions
When rows in a table rely on a reference to another key, we need to make sure that these rows are deleted when their parent reference is deleted. To do this, we specify *onDelete()*:

```
public function up()
{
        Schema::create('articles', function (Blueprint $table) {
            $table->id();
            $table->unsignedBigInteger('user_id')->nullable();
            $table->string('title');
            $table->text('excerpt');
            $table->text('body');
            $table->timestamps();

            $table->foreign('user_id')
                ->references('id')
                ->on('users')
                ->onDelete('cascade');
        });
}
```

### Accessing a Relationship
Even though our *articles()* relationship is method of our User model, to access the relationships for a user, *articles* must be called as a property:

#### ex.
```
$user = User::find(USER_ID_NUMBER);
$user->articles;
// a list of the user's articles
```

### Passing the Foreign Key

When defining a relationship method, if the method is not named the same as the property being accessed, for example a *user()* method for the Article class referencing *user_id* you must specify the foregin key. For example:

```
class Article extends Model
{
    public function author()
        {
            return $this->belongsTo(User::class, 'user_id');
        }
}
```
If we wanted to rename the relationship method to something more meaningful, it's important to specify the foreign key.

---

## [Many to Many Relationships With Linking Tables(ep31)](https://laracasts.com/series/laravel-6-from-scratch/episodes/31?autoplay=true)

For an example of many to many, let's look at articles. Articles could have many tags to reference their content. Each tag could also belong to many articles.

To call on this many to many relationship we must use *belongsToMany*:

```
class Article extends Model
{
    public function tags()
    {
        return $this->belongsToMany(Tag::class);
    }
}
```

### Creating a Linking Table

The convention for connecting via a many to many relationship is to create a separate table. The standard naming convention is alphabetal, singular, and space by an underscore. For example:

*article_tag*

After creation of the Tag model and table, we need to declare a linking table. In this relationship table declaration, you must declare that the ids must be unique. Then set up the foreign key relationships.

#### ex. *inside the up() method of the Tag table*
```
Schema::create('article_tag', function (Blueprint $table) {
            $table->id();
            $table->unsignedBigInteger('article_id');
            $table->unsignedBigInteger('tag_id');
            $table->timestamps();

            $table->unique(['article_id', 'tag_id']);

            $table->foreign('article_id')->references('id')->on('articles')->onDelete('cascade');
            $table->foreign('tag_id')->references('id')->on('tags')->onDelete('cascade');
});
```






