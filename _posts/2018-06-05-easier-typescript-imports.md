---
layout: post
title: 'Better imports in Typescript and Angular with path mapping'
tags: 
- Angular
- Typescript
- import
---


Another bookmark:
https://netbasal.com/sexier-imports-in-typescript-e3c645bdd3c6

{% raw %}
import { MyComponent } from '@shared/components/my'
{% endraw %}


tsconfig.json:
{% raw %}
{
  "compilerOptions": {
    ...
    "baseUrl": "./src",
    "paths": {
      "@datorama/utils/*": ["app/utils/*"],
      "@datorama/pipes/*": ["app/pipes/*"]
    }
    ...
  }
}
{% endraw %}

