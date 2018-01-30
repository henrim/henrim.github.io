---
layout: post
title: 'Angular: Filter overdue items from collectiondates'
tags: 
- angular
- filter
- ng-repeat
- date
- overdue
---

Last post we created a filter that returned true if item was overdue.
Now it's time to expand it a bit so we also can use it to filter items in ng-repeat:

{% raw %}
```javascript
angular.module('my.utils', [])
.filter('overdue', function () {    
    return function (input, selector) {
        if (input===undefined) {
            return false;
        }
        if(input.constructor===Array){
            var out=[];
            angular.forEach(input, function(i) {
                var test= Object.byString(i, selector);
                if (isOverDue(test)) {
                    out.push(i);
                }
            });
            return out;
        }
        else{
            return isOverDue(input);
        };

        function isOverDue(date){
            if (date) {
                var m = moment(date);
                if (m.isValid()) {
                    return moment().diff(m) > 0 ;
                }
            }
            return false;
        }
    };
})
``` 

There is also a small generic helper-function you should include:


```javascript

Object.byString = function(o, s) {
    s = s.replace(/\[(\w+)\]/g, '.$1'); // convert indexes to properties
    s = s.replace(/^\./, '');           // strip a leading dot
    var a = s.split('.');
    for (var i = 0, n = a.length; i < n; ++i) {
        var k = a[i];
        if (k in o) {
            o = o[k];
        } else {
            return;
        }
    }
    return o;
}

``` 

## Usage in html-template:

Now we can pass this as a filter to our ng-repeat:

Simple property:
```html
<!-- Simple property -->
<tr ng-repeat="s in surveys|overdue: 'due'" >
    <td>{{s.due|date}}</td>
    <td>{{s.name}}</td>
</tr>
```

__Nested property/compex object:__
```html
<tr ng-repeat="s in surveys| overdue: 'Survey.Due'">
    <td>{{s.Survey.Due|date}}</td>
    <td>{{s.Survey.Name}}</td>
</tr>
```
{% endraw %}
