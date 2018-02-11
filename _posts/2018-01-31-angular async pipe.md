---
layout: post
title: 'Angular : Async pipe or unsubscribe'
tags: 
- angular
- async
- observable
- subscribe
---

Observables in Angular with Async Pipe

Using the `_async_` pipe in an angular template handles subscribing and unsubscribing of observables automaticly,
and you can use use your variable like this

{% raw %}
```html
    <pre>{{cloud|async|json}}</pre>
```

But usually you want to use the same variable multiple times and this is not a good idea:

```html
    <h1>{{cloud.name|async}}</h1>
    <h2>{{cloud.customer.name|async}}</h2>
```

as unique subscriptions will be created for each use.

Angular 4.4 introduced this syntax to handle this better:

```html
<div *ngIf="cloud$|async; let cloud; else loading">
<h1>{{cloud.name}}</h1>
<h2>{{cloud?.customer?.displayName}}</h2>
</div>

<ng-template #loading>
  <mat-progress-bar mode="indeterminate"></mat-progress-bar>
</ng-template>
```

The magic is in the first line, 
`_cloud$_` is the observable and then a local variable `_cloud_` is intialised.
Cloud can then be used in further template references.
This also allows us an easy way to display a loading screen with the `_else_` case displaying 
our `_ng-template_`.
{% endraw %}