---
title: "Angular - injection tokens"
datePublished: Tue Jan 18 2022 21:36:51 GMT+0000 (Coordinated Universal Time)
cuid: ckykmzhba07zutos15w5h5mba
slug: angular-injection-tokens
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1642541765026/b0KFrd4es.png
tags: web-development, angular, beginners, frontend-development, dependency-injection

---

In this article we'll take a quick look at how injection tokens can be used through implementing an rxjs based wrapper around `navigator.geolocation.watchPosition()`. 


## What is an injection token? 

These tokens can be used in the DI provider. It helps you represent dependencies which doesn't have a runtime representation, but they get set during module setups. This means we could eventually disable functionalities if these tokens are setup properly. 
 
## Examples 

You can find the docs here: https://angular.io/api/core/InjectionToken 

Let's create our first token which will be called `GEOLOCATION`


```typescript
import { DOCUMENT } from '@angular/common';
import { inject, InjectionToken } from '@angular/core';

export const GEOLOCATION = new InjectionToken<Geolocation>(
  'Token for window.navigator.geolocation object', // debugging purposes
  // eslint-disable-next-line @typescript-eslint/no-non-null-assertion
  { factory: () => inject(DOCUMENT).defaultView!.navigator.geolocation } 
);
``` 

With `factory: () => ...` we can define where the values are coming from by default. With these lines we are trying to make sure that the geolocation API is there. 

Let's try to break it up more this factory:
1. we inject the `document`
2. grab the `defaultView`, it is nullable, but in this case we know it won't be null
3. then we can pretty much just access the `geolocation` through the `navigator` object

Let's look at a much much much smaller example 

```typescript
import { InjectionToken } from '@angular/core';

export const POSITION_OPTIONS = new InjectionToken<PositionOptions>(
  'Token for position options',
  { factory: () => ({}) }
);

```

Nothing fancy happens here, in this case the default value is an empty object, we just provide capabilities to change these options per module for example: 

In the providers array we can just say:

```typescript
{
      provide: POSITION_OPTIONS,
      useValue: {
        enableHighAccuracy: true,
      } as PositionOptions,
    },
```

And this way when we use the tokens we can change the options for our service. 

We can also inject a token into a token. Let's try to check if the `geolocation` object is present or not, that means if `geolocation` is supported or not. 

```typescript
import { inject, InjectionToken } from '@angular/core';
import { GEOLOCATION } from './geolocation.token';

export const GEOLOCATION_SUPPORT = new InjectionToken<boolean>(
  'Is Geolocation API supported?',
  {
    factory: () => !!inject(GEOLOCATION),
  }
);

```

We were able to simply inject our token into it and simply return if it is present or not. 

## Service implementation 

With these tokens set up we can setup a fairly configurable service and wrap around rxjs `Observable`. 

Let's first inject our tokens

```typescript

constructor(
    @Inject(POSITION_OPTIONS) options: PositionOptions,
    @Inject(GEOLOCATION) geolocationRef: Geolocation,
    @Inject(GEOLOCATION_SUPPORT) geolocationSupported: boolean
  ) 

```

If the tokens are not set during our module setup the value will be resolved what is inside the factory method. 

So based on our previous steps just above; we can assume the options is `{}`, `geolocationRef` is a `geolocation` object and `geolocationSupported` is true. 

Let's quickly implement an `Observable` wrapper around this:

```typescript

import { Inject, Injectable } from '@angular/core';
import { finalize, Observable } from 'rxjs';
import { GEOLOCATION } from './geolocation.token';
import { POSITION_OPTIONS } from './geolocation-options.token';
import { GEOLOCATION_SUPPORT } from './geolocation-support.token';

@Injectable({
  providedIn: 'root',
})
export class GeolocationService extends Observable<
  Parameters<PositionCallback>[0]
> {
  constructor(
    @Inject(POSITION_OPTIONS) options: PositionOptions,
    @Inject(GEOLOCATION) geolocationRef: Geolocation,
    @Inject(GEOLOCATION_SUPPORT) geolocationSupported: boolean
  ) {
    let watchPositionId: number;

    super((sub) => {
      if (!geolocationSupported) {
        sub.error('Geolocation is not supported in your browser');
      }

      watchPositionId = geolocationRef.watchPosition(
        (position) => sub.next(position),
        (positionError) => sub.error(positionError),
        {
          ...options,
        }
      );
    });

    return this.pipe(
      finalize(() => geolocationRef.clearWatch(watchPositionId))
    ) as GeolocationService;
  }
}

```

If the geolocation API is not supported our service will give as an error, the `watchPosition` callbacks are fairly straightforward those are just pushing values into the right stream and since we have options as well we just copy them into the function call third parameter. One thing you would have to do manually is to clear the watching, now if the Observable completes or unsubscribes it will auto-magically stop the watching. 

Lastly you can provide these tokens in the provider array as previously mentioned, but another really cool win with these kinda stuff is the ease of testing. If we want to test what happens when the user doesn't allow the geolocation API we can just do:

```typescript
   {
      provide: GEOLOCATION_SUPPORT,
      useValue: false
    },
```

This makes testing edge cases super easy. 


## Repo link

Example repo with full code: https://github.com/benceHornyak/geolocation-service 
Npm package: https://www.npmjs.com/package/@bencehornyak/geolocation-service

As you can see on the issues it is not fully ready to be consumed, but it is a great start if you want to play with it. If you are up to the challenge and want to improve your knowledge you can contribute to the repo 😊

## Resources

Here are some resources which were useful for me when I started to learn this topic:


1. https://angular.io/api/core/InjectionToken
2. https://angular.io/guide/dependency-injection-providers
3. https://angular.io/guide/lightweight-injection-tokens

## Final thoughts 

I hope you have found this short article about my example useful, let know if I've missed anything 
