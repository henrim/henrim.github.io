---
layout: post
title: Angular Material: Indicate active tab with ui-router
tags: 
- angular
- ui-router
- ui-sref
- md-active
- material
- material design
- md-tabs
- md-tab
---

I'm trying to indicate active tab in this application:
```html
    <md-tabs class="md-primary" flex>
        <md-tab ui-sref="member" label="Home"></md-tab>
        <md-tab ui-sref="member.salary" label="Salary"></md-tab>
        <md-tab ui-sref="member.about" label="About"></md-tab>
    </md-tabs>
```
It is developed with Angular Material and ui-router,  and usually I would use 
*ui-sref-active* to indicate the active tab.

I could not get that to work, so the workaround I found was:

    md-active="$state.includes('my state')"


 With same example as over:

```html
    <md-tabs class="md-primary" flex>
        <md-tab ui-sref="member" label="Home"></md-tab>
        <md-tab ui-sref="member.salary" label="Salary" md-active="$state.includes('member.salary')"></md-tab>
        <md-tab ui-sref="member.about" label="About" md-active="$state.includes('member.about')"></md-tab>
    </md-tabs>
``` 
