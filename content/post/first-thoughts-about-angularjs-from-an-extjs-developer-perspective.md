+++
date = "2013-11-22 22:59:32+03:00"
title = "First thoughts about AngularJS from an ExtJS developer perspective"
description = ""
tags = [
    "javascript",
    "angularjs",
    "extjs"
]
categories = [
    "AngularJS1"
]
+++

I thought that is time for me to learn something else, something new from the Front-end world just to be on same wave with latest trends and to feed the brain with a different perspective. After all life is all about learning and in doing we learn.

<!--more-->
<img class="right" src="../../images/feelings_about_angularjs_over_time.png" style="display: block;margin: 0 auto; width:400px;height:300px;">

I like everything what&#8217;s connected with web apps and lately I&#8217;ve been keeping my eyes on AngularJS. Seeing it&#8217;s growing popularity and that AngularJS advocates a different approach of building web apps, got me intrigued and I&#8217;ve started the learn process. After a week of diving in many resources I&#8217;ve decided to put down some of my thoughts.

**Learning curve**  
I can agree with this [guy][1]. You get down and up while trying to understand how the core works and you think sometimes that some of the things are easier and faster to achieve with less effort in ExtJS. sometimes the opposite.

**Documentation.**  
Being indulged by ExtJS&#8217;s good documentation, what I&#8217;ve found on AngularJS made my start not easy. For more resources google is your friend. My search brought to surface a few good posts which I think worths to check:
<!-- more -->

*   <http://stackoverflow.com/questions/14049480/what-are-the-nuances-of-scope-prototypal-prototypical-inheritance-in-angularjs>
*   <http://stackoverflow.com/questions/15666048/angular-js-service-vs-provider-vs-factory>
*   <http://www.ng-newsletter.com/posts/>
*   <http://pluralsight.com/training/Courses/TableOfContents/angularjs-fundamentals>

**First experiment and thoughts**  
Ended up with a <a title="http://plnkr.co/edit/61rmik" href="http://plnkr.co/edit/61rmik" target="_blank">simple directive</a> analog to the <a title="https://github.com/vadimpopa/Ux.field.Multiselect" href="https://github.com/vadimpopa/Ux.field.Multiselect" target="_blank">multiselectfield</a> Sencha Touch component I built a few months ago. The implementation wasn&#8217;t smooth and intuitive. Had to make some debugging and reread some resource to understand how scopes work in AngularJS for directives, parent and child elements. Other important AngularJS specifics on my learn list, is to understand how the watchers work in directives and how to write the code to end up with less of them. 

Angular seems a powerful, not big library. But building your first web components will take some time. Is not like you would extend an ExtJS `Panel` with some child items or a `View` with a configured `XTemplate` and some methods.

Overall when building an app or component you could spent 40% of your time on writing html and 60% javascript, while on ExtJS around 85% is javascript. Also ExtJS has built in nice layouts and a platform for theming, while on AngularJS is needed a 3&#8242;rd-party CSS/JS library or some personal efforts.

I like Angular&#8217;s two way binding, though I&#8217;m not sure yet when there are a lot of watches, from the performance perspective how the app will behave. Directives are very nice to make reusable components, but I&#8217;ll need some time to get used to Angular&#8217;s way of configuring components(directives) trough html attributes when in ExtJS  I did it trough javascript objects.

``` html
<div ng-controller="ExampleCtrl">
    <nvd3-cumulative-line-chart
            data="exampleData"
            id="exampleId"
            width="800"
            height="400"
            showXAxis="true"
            showYAxis="true"
            tooltips="true"
            interactive="true"
            x="xFunction()"
            y="yFunction()"
            color="colorFunction()"
            isArea="true"
            margin="{left:50,top:50,bottom:50,right:50}"
            >
        <svg></svg>
    </nvd3-cumulative-line-chart>
</div>
```

**Next Step**  
Climb next peak on that learning curve, with some more patient along with some beers, going to reread some of the resources and build some more directives.

 [1]: http://www.bennadel.com/blog/2439-My-Experience-With-AngularJS-The-Super-heroic-JavaScript-MVW-Framework.htm "http://www.bennadel.com/blog/2439-My-Experience-With-AngularJS-The-Super-heroic-JavaScript-MVW-Framework.htm"