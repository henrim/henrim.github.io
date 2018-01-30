---
layout: post
title: 'Angular : Using a filter for overdue dates'
tags: 
- angular
- filter
- date
- overdue
---

A simple filter that returns true/false if a date is overdue.
Here I'm using moment.js but any ol' js would do:


{% highlight javascript linenos %}

angular.module('my.utils', [])
.filter('overdue', function () {
    return function (date) {
        if (date) {
            var m = moment(date);
            if (m.isValid()) {
                return moment().diff(m) > 0 ;
            }
        }
        return false;
    };
})
{% endhighlight %}

## Usage in html-template

Here I'm using the filter in an ng-class statement (you need to wrap in paranthesis), applying text-warning if the survey is overdue.

{% highlight handlebars linenos %}
{% raw %}
<tr ng-repeat="s in surveys"  ng-class="(s.Survey.Due|overdue) ? 'text-warning' : ''">

    <td>{{s.Survey.Due|date}}</td>
    <td>{{s.Survey.Name}}</td>
</tr>
{% endraw %}
{% endhighlight %}



To filter the elements in the ng-repeat you would need a slightly different filter.
See my next post for an example

