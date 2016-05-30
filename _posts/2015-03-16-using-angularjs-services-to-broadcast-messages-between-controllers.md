---
layout: post
title:  "Using AngularJS services to broadcast messages between controllers"
date:   March 16, 2015
permalink: /using-angularjs-services-to-broadcast-messages-between-controllers/
tags:
  - AngularJS
  - Front End
  - Broadcast
  - Emit
categories:
  - AngularJS
comments: true
header: AngularJS_logo.png
---

Two handy functions built into the angular.js api are the *$scope.broadcast* and *$scope.emit* functions. Both of these functions are similar in the sense that they allow you to propagate messages up or down your html controller nesting. **Please note** that this applies to angular 1.x versions and does not apply to angular 2, as it is much different.


**Emit** will allow you to propagate messages *up* to controllers to which your current controller is nested within (in the markup).

**Broadcast** will allow you to propagate messages *down* to controllers to which are nested within your current controller (in the markup).

Suppose you have two controllers, *ParentCtrl* and *ChildCtrl*. These two controllers are nested in your markup accordingly. The `div` with `ng-controller="ParentCtrl"` is a parent of the `div` with `ng-controller="ChildCtrl"`.

Emitting from a child to a parent:

```javascript
// ** ChildCtrl Angular Controller **
$scope.emitToParent = function() {
    $scope.$emit('somethingHappened', {
        message: 'I clicked it'
    });
}

// ** ParentCtrl Angular Controller **
$scope.$on('somethingHappened', function(event, args) {
    // .. Do whatever you need to do.
    console.log('the message that we sent: ', args.message);
});
```

Broadcasting from a parent to a child:

```javascript
// ** ParentCtrl Angular Controller **
$scope.broadcastToChild = function(){
    $scope.$broadcast('publish');
};

// ** ChildCtrl Angular Controller **
$scope.$on('publish', function(){
    // .. subscribe & do something
});
```

### Creating a service to broadcast

**The problem**: Even though we can propagate up and down the html tree, we don't have a way to send a broadcast to an element that is on the exact level, such as two `div`'s that are not nested within each other that also have different controllers.

```html
<div ng-controller="searchCtrl">...</div>
<div ng-controller="someOtherCtrl">...</div>
```

In order to keep our controllers a little bit cleaner, as well as solve the problem stated above, we can move the broadcasting logic into a service of it's own.


```javascript
// Services
var app = angular.module('MyModule');
app.factory('broadcastService', function($rootScope) {
    return {
        send: function(msg, data) {
            $rootScope.$broadcast(msg, data);
        }
    }
});

app.factory('searchService', function($scope, broadcastService) {
    return {
        search: function(searchCriteria) {
            // Do some kind of searching
            // then broadcast the results
            var results = {};
            broadcastService.send('search', results);
        }
    }
});
```

From our controller, we would then use only our search service to do searching. The broadcasting is abstracted away from the controller and placed into the service layer.

```javascript
var searchCtrl = function($scope, searchService){
    $scope.onSearchClick = function(searchCriteria){
        searchService.search(searchCriteria);
        // No broadcasting directly from controllers!
    }
}
```

The broadcastService uses the `$rootScope` in order to broadcast the message. This means that all of our controllers throughout our application will be able to subscribe to the messages that it broadcasts.

```javascript
var someOtherCtrl = function($scope){
    $scope.$on('search', function(event, args){
        // args is the search results
        // from the searchService
    });
}
```
