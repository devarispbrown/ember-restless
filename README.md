# Ember RESTless

RESTless is a lightweight data persistence library for Ember.js.

It is used to communicate with a remote persistence layer (such as a JSON API) and map that data between your client side JavaScript Ember application.

Its goal is to reproduce much of the simple, useful features of [ember-data](https://github.com/emberjs/data), with a similar API, while remaining lightweight and stable.  Transitioning between the two should be possible with minimal effort.  The main departure from ember-data is the idea of the 'Store'. Instead, all transactions are executed immediately.  This can help alleviate bugs found in regards to syncing the Store with the backend service.

## Getting started

Include ember-restless.js from the dist/ folder in your application

### Namespace

RESTless can be referenced either with the namespace **RESTless** or the shorthand **RL**.  Similar to **Ember** and **Em**.

### Defining a Client

Similar to defining the 'Store' using ember-data, instead define the REST 'Client' for your application.  RESTless will automatically detect the RESTClient on your application namespace.  Defining a client is currently optional, and only needed if you would like to provide a custom adapter.

``` javascript
App.RESTClient = RL.RESTClient.create({
  revision: 1,
  adapter: App.RESTAdapter
});
```

### Defining a custom Adapter

Create a custom adapter to set a url to your backend REST service and optionally the namespace.
For example, if your REST API is located at http://api.myservice.com/v1
``` javascript
App.RESTAdapter = RL.RESTAdapter.create({
  url: 'http://api.myservice.com',
  namespace: 'v1'
});
```
See the **Advanced** section below for further use of a custom adapter.

### Defining Models

Each model you create should extend RL.Model
``` javascript
App.Person = RL.Model.extend();
```

### Attributes

Supported types are string, number, boolean, and date.  With a custom adapter, you can register custom types and transformations.
``` javascript
App.Person = RL.Model.extend({
  firstName: RL.attr('string'),
  lastName: RL.attr('string'),
  birthday: RL.attr('date'),
  createdAt: RL.attr('date', { readOnly: true }); // read-only attributes won't get sent back up to the API.
});
```

### Relationships

One-to-one use the _belongsTo_ attribute helper

``` javascript
App.User = RL.Model.extend({
  profile: RL.belongsTo('App.Profile')
});
App.Profile = RL.Model.extend({
  user: RL.belongsTo('App.User')
});
```

One-to-many and many-to-many, use the _hasMany_ helper
``` javascript
App.Post = RL.Model.extend({
  tags: RL.hasMany('App.Tag')
});
```

### Finding records

To find a Post with an id of 1:

``` javascript
var post = App.Post.find(1);
```

To use a query to find:
``` javascript
var people = App.Person.find({ name: "Peter" });
```

### Find all

Use findAll(), or alternatively use find() and omit parameters

``` javascript
var posts = App.Post.findAll();
```
or
``` javascript
var posts = App.Post.find();
```

### Creating records

Create records like you would a normal Ember Object

``` javascript
var post = App.Post.create({
  title: 'My First Post',
  body: 'Lorem ipsum...'
});
```
### Saving records

To save a record to the REST service call: saveRecord()
RESTless will automatically POST to save a new record, or PUT to update and existing record

``` javascript
var post = App.Post.create();
post.saveRecord();
```
Updating:
``` javascript
post.set('title', 'My Very First Post');
post.saveRecord();
```

### Deleting records

Will tell the REST service to delete the record, then destroy the object
``` javascript
post.deleteRecord();
```

### Callbacks

Both saveRecord and deleteRecord return Promises, allowing you to do the following:
``` javascript
var post = App.Post.create();
post.saveRecord().done(function() {
  //Show success message in UI
}).fail(function() {
  //Show error message in UI
}).always(function() {
  //Refresh the view
});
```

### Model lifecycle

RESTless supports most of the lifecycle states and events of ember-data.
All model objects have the following properties added:

* **isLoaded**: Record(s) have been retrieved from REST service
* **isDirty**: The record has local changes that have not yet been saved remotely
* **isSaving**: Record is in the process of saving to the backend
* **isNew**: Record has been created but not yet saved remotely
* **isError**: Record has been attempted to be saved, updated, or deleted but the backend returned an error

Additionally, you can subscribe to events that are fired during the lifecycle

* **didLoad**
* **didCreate**
* **didUpdate**
* **becameError**

``` javascript
post.on('didCreate', function() {
  console.log('post created!');
});
```

## Advanced

### Custom transforms
You can use a custom adapter to add custom transforms:
``` javascript
App.RESTAdapter.registerTransform('formattedDate', {
  deserialize: function(serialized) {
    //return custom date string
  },
  serialize: function(deserialized) {
    //return custom date json format
  }
});
```

### Custom plurals configuration
You can use a custom adapter to set irregular plural resource names
``` javascript
App.RESTAdapter.configure("plurals", {
  person: "people"
});
```

### Read-only attributes
Make attributes 'read-only', which will exclude them from being serialized into the json sent to your service when saving.
``` javascript
App.Person = RL.Model.extend({
  firstName: RL.attr('string'),
  lastName: RL.attr('string'),
  createdAt: RL.attr('date', { readOnly: true })
});
```

## Building RESTless

Uses node.js and Grunt

## Tests

Coming soon.

## Example App

Coming soon.
