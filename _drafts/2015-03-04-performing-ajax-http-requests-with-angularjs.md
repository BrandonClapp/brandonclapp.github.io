---
layout: post
title:  "Performing AJAX Http Requests with AngularJS"
date:   March 4, 2015
permalink: /performing-ajax-http-requests-with-angularjs/
tags: AngularJS AJAX
categories: AngularJS
header: AngularJS_logo.png
comments: true
---

One of the great things about AngularJS is all of the great services that it comes with out of the box. One of these happens to be the $http service, which allows you to easily create http requests from any controller just by injecting it to the controllers constructor.

I'll quickly demonstrate how to use the `$http` service in order to create ajax requests from you angular controller.

First, add $http as a parameter to your controller's constructor so that angular can properly inject it and make it accessible inside of your controller.

#### GET

Next, we'll use the `$http.get` method, which will return a promise.

```javascript
var url = 'https://api.github.com/users/brandonclapp';

var promise = $http.get(url);
promise.success(function(response) {
    console.log('success response: ', response);
}).error(function(response) {
    console.log('error response: ', response);
});
```

In the code above, we're able to resolve the promise return from our `get` function with the success method. By altering the url to be invalid, you'll see that the error method is fired instead. An alternative way of resolving the promise is with the `then` function.

```javascript
$http.get(url).then(function (response) {
    console.log(response.data);
});
```

If you only specify one parameter to `then`, you will only catch the success response. In order to account for errors, you'll need to provide a second function, like so.

```javascript
$http.get(url).then(function (response) {
    console.log('all is good', response.data);
}, function (error) {
    console.log('an error occurred', error.data);
});
```

#### POST, PUT, & DELETE

POST, PUT, and DELETE are all very similar, so there's no point in each of them having their own heading.

`post` is similar to the `get` method, except it also accepts an object to be posted along with the url.

```javascript
var url = 'http://httpbin.org/post';
$http.post(url, { myProperty: 'example prop' }).then(function (response) {
    console.log('all is good', response.data);
}, function (error) {
    console.log('an error occurred', error.data);
});
```

`put` is almost interchangeable with post, but it's probably a good idea to specify some sort of Id so that you know what to update on the receiving end.

```javascript
var url = 'http://httpbin.org/put';
$http.put(url, { Id: 100, myProperty: 'example prop updated' }).then(function (response) {
    console.log('all is good', response.data);
}, function (error) {
    console.log('an error occurred', error.data);
});
```

Finally, `delete` is very similar to `get`, because we don't specify a data object with our request. Although we don't provide a data object, it would still be possible to pass query string parameters along with your url in order to know which record to delete. (i.e. ...com/delete?id=100)

```javascript
var url = 'http://httpbin.org/delete?Id=100&somethingElse=whatever';

$http.delete(url).then(function (response) {
    console.log('all is good', response.data);
}, function (error) {
    console.log('an error occurred', error.data);
});
```
