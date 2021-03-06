---
title: Nest Widgets
page_title: Nest Widgets | Kendo UI AngularJS Directives
description: "Learn about the nesting of Kendo UI widgets in AngularJS applications."
slug: nest_widgets_angularjs
position: 7
---

# Nest Widgets

To nest Kendo UI widgets in AngularJS applications, make sure that the parent widget is initialized before you initialize the nested one.

In such cases the [`k-ng-delay`](http://docs.telerik.com/kendo-ui/AngularJS/introduction#delay-widget-initialization) directive can come in handy.

###### Example - nest Kendo UI Editor in a TabStrip component

    <div id="example" ng-app="KendoDemos">

      <div class="demo-section k-header">
        <div ng-controller="MyCtrl">
          <div kendo-tab-strip="tabstrip">
            <ul>
              <li class="k-state-active">First tab</li>
            </ul>
            <div>
              <textarea kendo-editor k-ng-delay="tabstrip"></textarea>
            </div>
          </div>
        </div>
      </div>

    </div>

    <script>
      angular.module("KendoDemos", [ "kendo.directives" ])
      .controller("MyCtrl", function($scope){
      })
    </script>
