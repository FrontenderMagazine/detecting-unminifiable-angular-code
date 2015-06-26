When you write Angular components such as controllers, services, or directives,
you can specify all dependencies your component needs to do its job. Angular's 
injector will then resolve those dependencies and make them accessible within 
your component.

The simplest way to declare dependencies is to name the arguments of your
component's factory method or constructor function exactly like the required 
components:

    angular
        .module("someModule")
        .factory("someService", function($http, $q) {
            // ...
        });
    

There's one issue with this approach: Because the dependency injector resolves
the dependencies based on the argument names, the resolution**doesn't work with
minified code**. In that case, parameters are generally shortened to single-
letter variables, thereby making it impossible to use their names for dependency
resolution.

Angular offers a solution to that problem. If you specify the dependency names
explicitly as strings rather than relying on precisely matching argument names, 
your components become**minification-safe**. You just have to make sure the
order of the provided strings matches the order of parameters to the factory 
method or constructor function:

    angular
        .module("someModule")
        .factory("someService", ["$http", "$q", function($http, $q) {
            // ...
        }]);
    

Instead of using the above inline array annotation syntax, you can also
annotate the function with the`$inject` property, which is a simple string
array of dependency names. This is the syntax I prefer to use:

    angular
        .module("someModule")
        .factory("someService", someService);
    
    someService.$inject = ["$http", "$q"];
    
    function someService($http, $q) {
        // ...
    }
    

Either way, the code can now be minified without issues. The problem with both
approaches, though, is that you only notice that your code is broken once you 
actually minify it, execute it, and run into the dependency resolution error. It
would be better if the code**failed fast** and **failed early**.

As it just so happens, you can use the `ng-strict-di` attribute to make Angular
behave exactly like that. This attribute is applied on the app element itself:

    <html ng-app="someModule" ng-strict-di>
        <!-- ... -->
    </html>
    

Quoting from the [Angular documentation][1], here's what it does:

> If this attribute is present on the app element, the injector will be created
> in "strict-di" mode. This means that the application will fail to invoke 
> functions which do not use explicit function annotation (and are thus unsuitable
> for minification), as described in the
>[Dependency Injection guide][2], and useful debugging info will assist in
> tracking down the root of these bugs.
>

Using strict dependency injection allows you to detect that an Angular
component is unsuitable for minification without minifying your code and trying 
it out. You should add`ng-strict-di` to your app element if you haven't done so
already!

[Tweet][3]

 [1]: https://docs.angularjs.org/api/ng/directive/ngApp
 [2]: https://docs.angularjs.org/guide/di
 [3]: https://twitter.com/share