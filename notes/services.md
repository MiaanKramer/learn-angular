# Services

- Curently displaying fake data (Eish)

Heroes COmponent will be lean and  focussed on support the view. It will also be easier to unit-test with a mock service.

## Create the HeroService
```sh
ng generate service hero
```

`hero.service.ts`

```ts 
import { Injectable } from '@angular/core';

@Injectable()
export class HeroService {

  constructor() { }

}
```

## Get hero data

`hero.service.ts`

```ts
import { Hero } from './hero';
import { HEROES } from './mock-heroes';

getHeroes(): Hero[] {
    return HEROES;
}
```

## Provide the HeroService
`Console`
Addittional options prior to service generation
```sh
ng generate service hero --module=app
```

`app.module.ts` (Providers)
```ts
provider: [
    HeroService,
    // 
],
```
**This is a interim code sample that will allow you to provide and use the HeroService. At this point, the code will differ from the HeroService in the "final code review".**
- The *providers* array tells Angular to create a single, shared instance of            *HeroService* and inject into any class that asks for it.

- The *HeroService* is now ready to plug into the *HeroesComponent*.

## Update HeroesComponent

`heroes.component.ts'

```ts
/** replace HEROES import */
import { HeroService } from '..hero.service';

heroes = HEROES; - > heroes: Hero[];
```

## Inject the HeroService

`hero.service.ts`

Add a private heroService parameter of type HeroService to the constructor.

```ts
constructor(private heroService: HeroService) { }
```

## Add *getHeroes()*

```ts
getHeroes(): void {
  this.heroes = this.heroService.getHeroes();
}

ngOnInit() {
  this.getHeroes();
}
```

## Observable *HeroService*

*Observable* is one of the key classes in the **RxJS library**.

`hero.services.ts`
```ts
import { Observable } from 'rxjs/Observable';
import { of } from 'rxjs/observable/of';

/** Replace getHeroes method with:*/
getHeroes(): Observable<Hero[]> {
    return of(HEROES);
}
```

## Subscribe in HeroesComponent
`heroes.component.ts`
```ts
getHeroes(): void {
  this.heroService.getHeroes()
      .subscribe(heroes => this.heroes = heroes);
}
/** Replace method with this code */
//* Observable.subscribe() is the critical difference */
```

## Create *MessagesComponent*
`Console`
```sh
ng generate component messages
```

`app.component.html`
```HTML
<h1> {{ title }} </h1>
<app-heroes></app-heroes>
<app-messages></app-messages>
```

## Create the *MessageService*

`Console`
```sh
ng generate service message --module=app
```

`message.service.ts`
```ts
import { Injectable } from '@angular/core';

@Injectable()
export class MessageService {
  messages: string[] = [];

  add(message: string) {
    this.messages.push(message);
  }

  clear() {
    this.messages = [];
  }
}
```
The service exposes its cache of messages and two methods: one to add() a message to the cache and another to clear() the cache.

## Inject it into the HeroService
`hero.service.ts` (import MessageService)
```ts
import { MessageService } from './message.service';

// Modify constructor
constructor(private messageService: MessageService) { }
```

## Send a message from HeroService
`hero.service.ts` Modify the *getHeroes* method to send  a message when the heroes are fetched.
```ts
getHeroes(): Observable<Hero[]> {
  // Todo: send the message _after_ fetching the heroes
  this.messageService.add('HeroService: fetched heroes');
  return of(HEROES);
}
```

## Display the message from HeroService

`messages.component.ts`
```ts 
import { MessageService }  from '../message.service';

constructor(public messageService: MessageService) {}
```

## Bind to the MessageService
`messages.component.html`
```HTML
<div *ngIf="messageService.messages.length">

  <h2>Messages</h2>
  <button class="clear"
          (click)="messageService.clear()">clear</button>
  <div *ngFor='let message of messageService.messages'> {{message}} </div>

</div>
```

This template binds directly to the component's messageService.

- The *ngIf only displays the messages area if there are messages to show.
- An *ngFor presents the list of messages in repeated <div> elements.
- An Angular event binding binds the button's click event to MessageService.clear().

The messages will look better when you add the private CSS styles to messages.component.css as listed in one of the "final code review" tabs below.

The browser refreshes and the page displays the list of heroes. Scroll to the bottom to see the message from the HeroService in the message area. Click the "clear" button and the message area disappears.

# Summary
- You refactored data access to the **HeroService** class.
- You provided the **HeroService** in the root **AppModule** so that it can be injected anywhere.
- You used *Angular Dependency Injection* to inject it into a component.
- You gave the **HeroService** get data method an asynchronous signature.
- You discovered **Observable** and the RxJS *Observable* library.
- You used RxJS **of()** to return an Observable of mock heroes **(Observable<Hero[]>)**.
- The component's **ngOnInit** lifecycle hook calls the **HeroService** method, not the constructor.
- You created a **MessageService** for loosely-coupled communication between classes.
- The **HeroService** injected into a component is created with another injected service, **MessageService**.


