---
layout: post
title: 'Material Typography - multiple fonts'
tags: 
- Material
- Angular
- Material Design
- Typography
---

I needed different font familys for Headers and Body for a recent Angular/Material Design project.

This guide is a starting point
https://material.angular.io/guide/typography
but it wasn't clear how to have different fonts for different parts.
Looking in the code as the article suggests (https://github.com/angular/material2/blob/master/src/lib/core/typography/_typography.scss)

it was quite easy to setup these rules in my styles.scss:

{% raw %}
```SASS
@import '~@angular/material/theming';


//FONTS
$head-fonts: 'Abhaya Libre, Roboto';
$base-fonts: 'Rubik, Roboto';
$general-typography: mat-typography-config(
  $font-family: $base-fonts,
  $display-4:     mat-typography-level(112px, 112px, 300, $head-fonts),
  $display-3:     mat-typography-level(56px, 56px, 400, $head-fonts),
  $display-2:     mat-typography-level(45px, 48px, 400, $head-fonts),
  $display-1:     mat-typography-level(34px, 40px, 400, $head-fonts),
  $headline:      mat-typography-level(24px, 32px, 400, $head-fonts),
  $title:         mat-typography-level(20px, 32px, 500, $head-fonts),
  $subheading-2:  mat-typography-level(16px, 28px, 400, $head-fonts),
  $subheading-1:  mat-typography-level(15px, 24px, 400, $head-fonts),
  $body-2:        mat-typography-level(14px, 24px, 500),
  $body-1:        mat-typography-level(14px, 20px, 400),
  $caption:       mat-typography-level(12px, 20px, 400),
  $button:        mat-typography-level(14px, 14px, 500),
  // Line-height must be unit-less fraction of the font-size.
  $input:         mat-typography-level(inherit, 1.125, 400)
);

// Override the typography in the core CSS.
@include mat-core($general-typography);

```

There you have it, Abhaya Libre as the font for headings and stuff,
Rubik as the font for all the rest.


Note: I kept all the default styles for $body-2 etc for future reference,
all the lines from that one to the end could be deleted, as the line 
$font-family: $base-fonts
is setting the default for this entire config.

