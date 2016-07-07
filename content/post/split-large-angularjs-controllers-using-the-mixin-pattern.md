+++
date = "2014-03-02 21:12:06+03:00"
title = "Split large AngularJS Controllers using the Mixin Pattern"
description = "Split large angularjs controllers using the mixin pattern and the $controller service"
tags = [
    "angularjs",
    "controller",
    "mixin"
]
categories = [
    "AngularJS1",
    "Javascript",
    "ExtJS 4"
]
+++

In case if your controller became too large and the inheritance is not a solution, the Mixin pattern can solve this problem very easy by splitting the logic in mixins each serving a functionality or a feature. Within AngularJS I found a easy to do it by using the `$controller` service.
<!--more-->
``` javascript
//AngularJS Mixin
app.controller('CanSingController', ['$scope'
    function ($scope) {
    	$scope.sing = function() {
	         alert("I'm on the highway to hell...")
	    };
    }
]);


app.controller('MusicianController', ['$scope'
    function ($scope) {
    	var canSingCtrl = $controller('CanSingController',{$scope: $scope});

    	$scope.whatMusicianCanDo = function() {

    		//Call locally, or best call sing in your view
    		$scope.sing();
    	};
    }
]);
```

For fun and comparation I brought also an ExtJS example found in its [documentation][1]:

``` javascript
//ExtJS Mixin
Ext.define('CanSing', {
     sing: function() {
         alert("I'm on the highway to hell...")
     }
});

Ext.define('Musician', {
     mixins: ['CanSing']
})
```
So the `Musician` will get a `sing` method from `CanSing` `controller`/`mixin`.

If to raise same issue from ExtJS's documentation: *what if the Musician already has a sing method? Or you want to mix in two classes, both of which define sing?*

In AngularJS this can be solved by defining the `sing` method in the instantiated mixin, and use an identifier or a local variable within the parent controller to keep that instance:

``` javascript
//AngularJS Mixin without overriding
app.controller('CanSingController', ['$scope'
    function ($scope) {

	    return {
	    	sing: function() {
		         alert("I'm on the highway to hell...")
		    }
	    }
    }
]);

app.controller('MusicianController', ['$scope'
    function ($scope) {
    	var canSing = $controller('CanSingController as canSing',{$scope: $scope}),

    	$scope.sing = function() {

    		// call trough the local variable
    		canSing.sing();	

    		//call trough the indentifier
    		$scope.canSing.sing();
    	}
    }
]);
```
Usually I define within the `$scope` the methods which are going to be exposed in views and in the instance the ones which will be called locally in the parent controller. This way I preserve a sort of encapsulation and also looks cleaner. But when defining wihtin the `$scope` just make sure not to have overrides. To avoid them you could just use the `identifier` approach which in some way also gives sort of encapsulation of all methods within one property.


In ExtJS: *it's good to define mixins as an object, where you assign a name to each mixin. In this case the sing method of `Musician` will overwrite the mixed in `sing` method. But you can access the original mixed in method through special mixins property.*

``` javascript
//ExtJS Mixin without overriding
Ext.define('Musician', {
     mixins: {
         canSing: 'CanSing'
     },

     sing: function() {
         // delegate singing operation to mixin
         this.mixins.canSing.sing.call(this);
     }
})
``` 


[1]: http://docs.sencha.com/extjs/4.2.2/#!/api/Ext.Class-cfg-mixins "http://docs.sencha.com/extjs/4.2.2/#!/api/Ext.Class-cfg-mixins"