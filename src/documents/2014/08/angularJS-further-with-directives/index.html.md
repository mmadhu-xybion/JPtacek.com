---
layout: post
title: AngularJS - Further with Directives
date: 2014-8-22
ignore: true
tags: ["AngularJS","JavaScript","Web"]
---
 
[AngularJS](http://www.angularjs.org) is a Javascript MVC framework from the fine folks over at
[Google](http://www.google.com). The focus of Angular is building complex
 HTML based client applications. Its design philosophy is data first, where your data will be updating the DOM.
 Contrast this to a framework like JQuery where the DOM will update your data.

![AngularJS Logo](angularLogo.png)

This is the tenth (I know, right!!?!!?!) in a series of posts on AngularJS where we are using Chemistry data from the periodic table
to help us understand the framework. The others posts are

1. [AngularJS - Introduction](http://www.jptacek.com/2013/10/angularjs-introduction/)
2. [AngularJS - Introducing AngularJS Controllers](http://www.jptacek.com/2013/10/introducing-angularjs-controllers/)
3. [AngularJS - Introducing NG-Repeat](http://www.jptacek.com/2013/10/angularjs-introducing-ng-repeat/)
4. [AngularJS - More with NG-Repeat](http://www.jptacek.com/2014/01/angularjs-further-with-ng-repeat/)
5. [AngularJS - Image Binding](http://www.jptacek.com/2014/01/angularjs-lou-reed/)
6. [AngularJS - Introducing Templates](http://www.jptacek.com/2014/02/angularJS-templates/)
7. [AngularJS - Introducing Routing](http://www.jptacek.com/2014/02/angularJS-IntroToRouting/)
8. [AngularJS - Introduction to Services](http://www.jptacek.com/2014/05/angularJS-Intro-To-Services/)
9. [AngularJS - Introduction to Directives](http://www.jptacek.com/2014/06/angularJS-intro-to-directives/)
10. AngularJS - Further with Directives

>Note: AngularJS does not allow for more than one ng-app directive. When I have multiple angular posts on
the home page of my blog, only one application will work. I need to refactor the entire site to account for
this. All of that to say this, you are best clicking on a single article so you can see the pages in action.

Previously, we took an introductory look at directives. This time we will go a bit deeper.

In our previous example, we displayed data from the periodic table, specifically, the atomic weight, atomic number
and element name. Our data source, has a lot more information, such as boiling and melting points, density, etc. In
this example, we will expand the directive by enabling a click event to display expanded data about the selected element.

The first thing to mention is scope. Scope, as we have already seen, is the magical tool that Angular uses to 
communicate between your view and services, etc. By default, a directive does not get a new child scope, but
uses the parent scope. So, in our case, when the directive is inside a controller, it will use the 
controller's scope.

To get access to the scope, we can use the ``link`` function. The ``link`` function gets called every time the 
element gets bound to the data in the ``$scope`` object. In general, ``link`` is used to manipulate the DOM, 
either by adding event listeners or updating the dom.

The link function takes three arguments:

* ``scope`` - Scope gets passed to the directive. This is generally the scope of the calling controller
* ``elem`` - The element on which the directive is derived. In our case, it will be the template markup for
the chemical element
* ``attrs`` - Attributes associated with our element

It is worth noting that AngularJS ships with jQLite, which is a subset of the full 
[jQuery](http://jquery.com/) library. If you are using full
jQuery within your web app, then that will be used instead. All of that to say this, you access DOM elements 
similarly as you do in jQuery, and since jQuery is already wrapped, you don't need to use your old school
``$`` for access.

In our example, we are going to use the ``link`` function to manipulate the DOM. First, let us take a look
at the JavaScript

```javascript
chemistryApp.directive('periodicchartelement', function (chemistryService) {;
    return {
        restrict: 'E',
        templateUrl: './template/periodic-template.html',
        link: function (scope, elem, attrs) {
            elem.addClass('nonMetal');
            elem.bind('click', function() {
                // Change state
                scope.$apply(function(){
                    scope.fullElement = !(scope.fullElement);
                });

            });
            elem.bind('mouseover', function() {
                elem.css('cursor', 'pointer');
            });
        },
        scope:{
            element:'=',
            elementCssClass:'@elementCssClass'
        }

    }

});
```

With the exception of adding the link function, things are pretty similar to before. The link function does several 
pieces of functionality.

First, we add a default class to our element. The second, is we bind a click event to our chemical element. When
that click event is called, we update a boolean variable on our scope, which is our parent scope from the calling 
controller and set it to the opposite. Third, we also bind a mouseover event to manipulate our CSS to change the 
 cursor to a pointer.
 
Our template has now also been updated. The boolean scope variable, ``fullElement`` is used to show one of two different
HTML "paths" in our template, one shows a small subset of data, the other a larger subset of data from the periodic 
table. 

```xml
<div class='periodicCell {{elementCssClass}}' data-ng-hide="fullElement">
          <div class="atomicNumber">{{element.atomicNumber}}</div><div class="atomicWeight">{{element.atomicWeight}}</div>
          <div class="atomicSymbol">{{element.symbol}}</div>
          <div class="centerElementDisplay">{{element.name }}</div>
      </div>
      <div class='periodicPop {{elementCssClass}}'  data-ng-show="fullElement">
          <div class="atomicNumberPop">{{element.atomicNumber}}</div>
          <div class="atomicWeightPop">{{element.atomicWeight}}</div>
          <div class="atomicSymbolPop">{{element.symbol}}</div>
          <div class="density">{{element.density || '-'}}</div>
          <div class="meltingPoint">{{element.melting + 273.15|number:2}}</div>
          <div class="boilingPoint">{{element.boiling + 273.15|number:2}}</div>
          <div class="elecConfig">{{element.elecconfig }}</div>
          <div class="elctroNeg">{{element.electronegativity}}</div>
          <div class="centerElementDisplayPop">{{element.name }}</div>
      
      </div>
```



<script type="text/javascript" src="/2014/08/angularJS-further-with-directives/js/chemistryApp.js"></script>
<script type="text/javascript" src="/2014/08/angularJS-further-with-directives/js/chemistryController.js"></script>
<script type="text/javascript" src="/2014/08/angularJS-further-with-directives/js/chemistryService.js"></script>
<script type="text/javascript" src="/2014/08/angularJS-further-with-directives/js/chemistryDirective.js"></script>

