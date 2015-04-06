# angular-symfony

## Purpose

When creating a read-only API with FOSRestBundle you can use angular's ngResource right out of the box.
However, when validating input with symfony forms you're going to run into a few problems as symfony forms requires data to be nested with the form's name, this also allows for multiple forms to be submitted with one request. Okay, not that bad you would think... Unfortunately this causes havoc with angular (requires array for collection of items, etc).

### Security
From a security point it is better to return results as objects than an array as its not a valid JavaScript statement, it cannot be parsed and instantiated as a new object in JavaScript. This therefore prevents the cross-site scripting attack from accessing data from AJAX JSON services on other domains.
> http://haacked.com/archive/2008/11/20/anatomy-of-a-subtle-json-vulnerability.aspx/

## Quickstart

For a complete example see the examples folder.

Basic app setup (this can be omitted). 
```js
var myApp = angular.module('app', ['app.resources']);

myApp.config(['$httpProvider', '$injector', function ($httpProvider, $injector) {

    $httpProvider.defaults.headers.post['Content-Type'] = 'application/json; charset=UTF-8';
    $httpProvider.defaults.headers.put['Content-Type'] = 'application/json; charset=UTF-8';

    $httpProvider.interceptors.push(function($q) {
        return {
            'request' : function(request) {
                if(request.data) {
                    request.data._format = 'json';
                }
                return request;
            }
        };
    });

}]);
```

Example 
```js
var myAppResources = angular.module('app.resources', [ 'symfony.resource' ]);

myAppResources.factory('Contact', ['$resource', function($resource) {
    return $resource('api/contact/:id', { }, {
        query : { nest : 'people' } /* nest specific to  */
    }, { nest : 'person' } /* the default nest */);
}]);

```