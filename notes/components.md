# Angular Components

`TODO: add notes here about Angular's Component architecture`

Creates a new heroes component
```sh
ng generate component heroes
```

`/src/app/heroes component`

## Add a hero property

```TypeScript
import { Component, OnInit } from '@angular/core';

@Component({
    selector: 'app-heroes',
    templateUrl: './heroes.component.html',
    styleUrls: ['./heroes.component.css']
})

export class HeroesComponent implements OnInit {

    constructor() { }

    hero = 'Windstorm'; // Property Added

    ngOnInit() {

    }
    
}
```
## Show the hero
`heroes.component.html`
```TypeScript
{{ hero }}
```

## Show the HeroesComponent view
`app.component.html`

```HTML
<h1>{{ title }}</h1>
<app-heroes></app-heroes>
```

**Displays:**
```
Tour of Heroes -> title

Windstorm -> Hero
```

## Create a Hero class

`/src/app/ + hero.ts`

```TypeScript
export class Hero {
  id: number;
  name: string;
}
```

## Import and Refactor

```TypeScript
import { Component, OnInit } from '@angular/core';
import { Hero } from '../hero';

@Component({
  selector: 'app-heroes',
  templateUrl: './heroes.component.html',
  styleUrls: ['./heroes.component.css']
})
export class HeroesComponent implements OnInit {
  hero: Hero = {
    id: 1,
    name: 'Windstorm'
  };

  constructor() { }

  ngOnInit() {
  }
}
```

## Show the hero object
`heroes.component.html`
```HTML
<h2>{{ hero.name }} Details</h2>
<div><span>id: </span>{{hero.id}}</div>
<div><span>name: </span>{{hero.name}}</div>
```
`Hero` is now an **object** ->
### **Displays:**
```
Windstorm Details -> hero.name

id: 1 -> hero.id
name: Windstorm -> hero.name
```

## Format with the UppercasePipe
`heroes.component.html`
```HTML
<h2>{{ hero.name | uppercase}} Details</h2>
                 <!--Pipe-->
```
- Hero name now displays in uppercase

## Edit the hero
### Two-way binding
`src/app/heroes.component.html`
```HTML
<div>
    <label>
        <input [(ngModel)]="hero.name" placeholder="name">
    </label>
</div>
```

## The missing *FormsModule*

## App stopped working
`[(ngModel)]`  Valid Angular directive, by isn't available by default.
```
Template parse errors:
Can't bind to 'ngModel' since it isn't a known property of 'input'.
```

## Import *FormsModule*
`src/app/app.module.ts`
```TypeScript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
+++
import { FormsModule } from '@angular/forms'; // <-- NgModel lives here!
^^^

import { AppComponent } from './app.component';
import { HeroesComponent } from './heroes/heroes.component';


@NgModule({
  declarations: [
    AppComponent,
    HeroesComponent
  ],
  imports: [
    BrowserModule,
    +++
    FormsModule // Metadata import
    ^^^
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## Summary
- You used the CLI to create a second HeroesComponent.
- You displayed the HeroesComponent by adding it to the AppComponent shell.
- You applied the UppercasePipe to format the name.
- You used two-way data binding with the ngModel directive.
- You learned about the AppModule.
- You imported the FormsModule in the AppModule so that Angular would recognize and     apply the ngModel directive.
- You learned the importance of declaring components in the AppModule and appreciated   that the CLI declared it for you.

## @**Component** Annotation

`TODO: give an example of a Component and describe each attribute`

The Heroes App Component

- `Selector` - The component's CSS element selector.

- `templateURL` - The location of the component's template file.

- `styleUrls` - The location of the component's private CSS styles.

## Lifecycle

`TODO: descibe the lifecycle events of an Angular component`

- It gets created.

- It gets rendered and it's children get rendered.

- Gets checked when its data-bound properties change, and destroys it before      removing it from the DOM.