# Section 5 Forms

## [The Seven RESTful Controller Actions](https://laracasts.com/series/laravel-6-from-scratch/episodes/21?autoplay=true)

### Index
Should render a list of resources.

#### ex. list of articles, list of users, list of projects
---

### Show *(id)*
Show a single resource.

#### ex. article with *id*, specific user

---
### Create
Shows a view to create a new resource.

---
### Store
A way to persist a new resource.

---
### Edit
Show a view to edit an existing item or resource.

---

### Update
Persist the edited resource.

---
### Destroy
A way to delete a resource entirely.

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



