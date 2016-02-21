AngularJS Reference Sheet
======================

> **Note:** Loosely based on video course on Udemy: [Learn and Understand AngularJS](https://www.udemy.com/learn-angularjs/) by Tony Alicea

Some Common Directives
-----------------------
```
ng-cloak
ng-click
ng-model
ng-repeat
ng-class
ng-show
ng-hide
ng-if
```
###Examples:
```
<div ng-cloak> {{ someVariable }} </div>
```
Forces hide the `{{stuff...}}` while loading page, useful for slow connections

```
<input ng-click="{{ alertMe() }}">
```

```
<div ng-model="name">{{name}}</div>
```
```
<div ng-repeat="rule in rules">
	<ul>
		<li>{{rule.key}}</li> // Assuming it comes from JSON file
	</ul>
</div>
```
```
<div ng-class="{ 'alert-warning' : input.length > characters , 'alert-danger' : input.length < characters } ">
```
```
<div ng-show="property.length > 5">Some alert message</div>
<div ng-hide="property.length !== 5">Some alert message</div>
<div ng-if="property.length > 5">Show this content if property bigger than 5</div>
```
ng-show and ng-if are similar, but ng-show adds a class display/hide, whereas ng-if removes the node from the DOM.

Routes
--------

```
var myApp = angular.module('myApp', ['ngRoute']); // create a module

myApp.config(function($routeProvider) {
	$routeProvider
		.when('/some/:page', {
			templateUrl : 'pages/somepage.html',
			controller  : 'someController',
			resolve     : { //check if user is logged in, else redirect
				currentAuth : function(AuthenticationService) {
					return Authentication.someRequireAuthMethod();
				}
			}
		})
});

myApp.controller('someController', ['$scope', '$routeParams', function($scope, $routeParams) {
	$scope.someVar = $routeParams.page || someDefaultValue;
}]);
```

Custom Service
------------------

```
myApp.service('myServiceName', function() {

	var self = this; // store 'this' function in 'self' so we can pass to an inner function
	self.propertyOne = 'someprop'; // alternatively: this.propertyOne

	this.propertyOneLength = function() {
		return self.propertyOne.length;
	}
});
```

Directives
------------
```
/searchResult normalized, equivalent in html is search-result
myApp.directive("searchResult", function() {
	return {
		// single string template
		// OR
		// string path using templateUrl: 'path/searchresult.html'
		//
		template: '<a href="#"><h3>Result title</h3><p>Detail</p></a>',

		// Tell type of directive u want to implement, default is AE
		// A=attribute, E=element, C=class, M=comment
		// A = <div search-result></div>
		// E = <search-result></search-result>
		// C = <div class="search-result"></div>
		// M = <!-- directive: search-result -->
		//
		restrict: 'AC', // only attribute and class mode are on

		// true replaces custom tag with the template string above,
		// false lets custom tag stay and put template string inside
		//
		replace: true

		// create own scope so it doesnt interfere with global $scope
		// to poke hole, add attributes to the route page directive, eg
		// <search-result person-age="{{ person.age }}"
		// person-name="{{ person.name }}"></search-result>
		//
		scope: {
			// blocks other scopes. Here we can 'poke holes'
			personName : '@' ,
			personAge : '@' , // @ means: pass text , one-way binding
			personAgeSpecial: '@personAge', // if we want custom name
			// now you can use personAge, personAgeSpecial, etc
			// in your template file

			personObject : '=', // = means: pass object, 2-way binding
			//
			// to poke hole do in template specifier:
			// <search-result person-object="person"></search-result>
			// and in template:
			// <h3>{{ personObject.name }}</h3>
			// <p>{{ personObject.address }}</p>

			formattedAddressFunction : '&' // means passing a function
			//
			// say we have a function in our controller:
			// myApp.controller('someservice', function($scope){
			// 		$scope.person = {
			//			address: 'some address',
			//			city: 'somecity'
			// 		}
			//		$scope.formattedAddress = function(person) {
			//			return person.address + ', ' + person.city;
			//		}
			// });
			//
			// to poke a hole do in template specifier:
			// <search-result person-object="person" formatted-address-
			// function="formattedAddress(aperson)"></search-result>
			//
			// and in template pass a mapping object in
			// function parameters:
			// <h3>{{ personObject.name }}</h3>
			// <p>{{ formattedAddress({ aperson: personObject }) }}</p>
		},

		// "onbind" if you cant do smth in the directive,
		// or data is coming from a database, etc -
		// during execution you can make changes here
		//
		link: function(scope, elements, attributes) {
			if(scope.personObject.name === 'Jane Doe') {
				elements.removeAttr('class');
			}
		},

		// transclude (true/false, default is false):
		// include one piece of document inside another.
		// you also need to specify where in the
		// actual template the text
		// to transclude, eg:
		// <h3>some title</h3>
		// <p>some text</p>
		// <!--then as element: -->
		// <ng-transclude></ng-transclude>
		// <!-- or as attribute: -->
		// <span ng-transclude></span>
		//
		transclude: true
	}
});


```
