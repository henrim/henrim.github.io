---
layout: post
title: 'Angular : Countdown with Cancel'
tags: 
- angular
- rxjs
- observable
- interval
- cancel
---

Create a countdown timer you can cancel
This countdown counts down from 3 to 0
and then redirects to another route.

It is possible to cancel the countdown before it reaches 0 by clicking the cancel button.
This vil stop the subscription and cancel the redirect.
{% raw %}
```angular

import { Component, OnInit} from '@angular/core';
import { ActivatedRoute, Router } from '@angular/router';
import { Subject, Subscription } from 'rxjs';
import { Observable } from 'rxjs/Observable';

@Component({
  selector: 'app-system-home',
  templateUrl: './system-home.component.html',
  styleUrls: ['./system-home.component.scss']
})
export class SystemHomeComponent implements OnInit{

  countdown: Subscription;
  private ngUnsubscribe: Subject<void> = new Subject<void>();
  vm: VM = new VM()

  constructor(private router: Router) {
        this.count()
  }
  ngOnInit() {
  }
  
  cancel() {
    this.vm.cancelled=true
    this.countdown.unsubscribe()
  }
  count() {
    if (!this.countdown) {
      this.countdown = Observable.interval(1000)
        .take(this.vm.counter)
        .map(() => --this.vm.counter)
        .subscribe(x => {
          console.log(this.vm.counter)
          if (this.vm.counter == 0) {
            this.router.navigate(['addsystem'], { relativeTo: this.route })
          }
        })
    }
  }
}
class VM {
  counter: number = 3
  cancelled: boolean = false
}
```

HTML

```html
 
    <div *ngIf="!vm.cancelled">
      Redirecting in {{vm.counter}}
      <br>
      <button mat-raised-button (click)="cancel()">Cancel</button>
    </div>

```
