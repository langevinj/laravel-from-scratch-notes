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







