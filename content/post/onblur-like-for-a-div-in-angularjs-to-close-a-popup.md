+++
date = "2014-01-24T17:50:16+03:00"
title = "Onblur like for a div in AngularJS to close a popup"
description = "I've coded a very simple AngularJS directive to close a popup when the user clicks anywhere else in the page but not on popup"
tags = [
    "angularjs",
    "directive",
    "popup",
    "onblur"
]
categories = [
    "AngularJS1",
    "Javascript"
]
+++
Often is needed to close a popup when the user clicks anywhere else in the page but not on popup. There are many workarounds for such functionality, including one of the simplest which is to give to the `div` the `tabindex` attribute and use `onblur` event.

But `onblur` wasn't suitable for me because is fired only if the popup have got the focus, and the user may want to close that popup without giving the focus to it.
<!--more-->

*   html 
``` html
<div tabindex="-1" onblur="closeMyPopup()"></div>
```

*   AngularJS 
``` html
<div class="popup" tabindex="-1" data-ng-show="form.popups.myPopup" data-ui-event="{ blur : 'closeMyPopup()'}"></div>
```
So I've coded a very simple AngularJS directive which evaluates an expression on the containing scope when the dom click event is triggered and the target is not a child of that popup:

``` javascript
app.directive("outsideClick", ['$document','$parse', function( $document, $parse ){
    return {
        link: function( $scope, $element, $attributes ){
            var scopeExpression = $attributes.outsideClick,
                onDocumentClick = function(event){
                    var isChild = $element.find(event.target).length > 0;

                    if(!isChild) {
                        $scope.$apply(scopeExpression);
                    }
                };

            $document.on("click", onDocumentClick);

            $element.on('$destroy', function() {
                $document.off("click", onDocumentClick);
            });
        }
    }
}]);
```

And simply to use:
``` html
<div class="popup" data-ng-show="form.popups.myPopup" outside-click="closeMyPopup()"></div>
```