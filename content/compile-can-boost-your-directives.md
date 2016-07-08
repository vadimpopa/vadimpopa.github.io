+++
date = "2015-02-01 22:59:32+03:00"
title = "Compile can boost your directives"
description = "Use whenever you can [compile] to boost your Angular directives and get rid of whatchers"
tags = [
    "javascript",
    "angularjs",
    "directive",
    "compile",
    "link"
]
categories = [
    "AngularJS1"
]
+++

<img class="right" src="../../images/rocket.png" style="display: block;margin: 0 auto; width:150px;height:150px;">
Use whenever you can [compile](https://github.com/angular/angular.js/wiki/Understanding-Directives) to boost your Angular directives and get rid of whatchers. For instance we have a `directive` which acts as a data row in a `ngRepeat`, like a grid row. Only besides the trivial columns this row has some action buttons.
<!--more-->
```html
<!--myDataRow.tpl.html-->
<div class="my-data-row" ng-class="{ 'my-data-row-selected': row.isSelected }" layout="row">
    <span class="my-data-row-name">
      <a class="my-data-row-id" href="">{{ row.id }}</a>
      <span>{{ row.name }}</span>
    </span>
    <span class="my-data-row-actions" flex>
      <i ng-show="canCommentAction" class="my-data-row-actions-comment icon" ng-click="commentOnEntity($event)"></i>
      <i ng-show="canDeleteAction" class="my-data-row-actions-delete icon" ng-click="deleteEntity($event)"></i>
      <i class="icon"></i>
    </span>
</div>
```

```javascript
function myDataRowDirective() {
    return {
        restrict: 'E',
        replace: true,
        templateUrl: 'directives/myDataRow/myDataRow.tpl.html',
        scope: {
            row: '=',
            onDelete: '&?',
            onComment: '&?'
        },
        link: myDataRowLink
    };
}

function myDataRowLink(scope, element, attrs) {
  //the ngShow flags are defined for the row actions
    scope.canDeleteAction = attrs.onDelete;
    scope.canCommentAction = attrs.onComment;
}
```

```html
<!--Use of myDataRow-->
 <my-data-row
      on-delete="stories.deleteStory(story, $event)"
      on-comment="stories.commentOnStory(story, $event)"
      ng-repeat="story in stories.userStories | filter: stories.searchUserStories | filter: stories.filterUserStoriesObj | orderBy: 'index' track by story.id"
      row="story">
</my-data-row>
```
Everything is fine, the directive works ok. But once the view gets heavy and has a lot of rows to show the user encounters slow responsiveness. The reasons may be beacause of the digest(sometimes running form `rootScope`) cycle which runs trough many watchers. In an AngularJS app you can see whatchers everywhere including in [ngShows](https://github.com/angular/angular.js/blob/master/src/ng/directive/ngShowHide.js)  used within above template. Having them in a directive within an `ngRepeat` adds more overhead to responsivness.

Here comes `compile` which gets rid of whatchers. Instead of checking whether to show or not the action buttons in the `link` function, do it in `compile`. This way the directive gets rid of 2 `ngShows` and at the same time of 2 `whatchers`:

```javascript
// myDataRowDirective optimized
function myDataRowDirective() {
    return {
        restrict: 'E',
        replace: true,
        templateUrl: 'directives/myDataRow/myDataRow.tpl.html',
        scope: {
            row: '=',
            onDelete: '&?',
            onComment: '&?'
        },
        compile: myDataRowCompile
    };
}

function myDataRowCompile(tElement, tAttrs) {
    if (!tAttrs.onDelete) {
        tElement.find('.my-data-row-actions-delete').remove();
    }
    if (!tAttrs.onComment) {
        tElement.find('.my-data-row-actions-comment').remove();
    }
}
```
In the end would like to suggest you to read this stackoverflow [thread](http://stackoverflow.com/questions/12164138/what-is-the-difference-between-compile-and-link-function-in-angularjs) which references a few examples of when and how to use `compile` instead of `link`. 

Happy 2015 coding !
