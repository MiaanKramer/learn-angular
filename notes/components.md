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

## Create mock heroes

`++ src/app/mock-heroes.ts`

```TypeScript
import { Hero } from './hero';

export const HEROES: Hero[] = [
  { id: 11, name: 'Mr. Nice' },
  { id: 12, name: 'Narco' },
  { id: 13, name: 'Bombasto' },
  { id: 14, name: 'Celeritas' },
  { id: 15, name: 'Magneta' },
  { id: 16, name: 'RubberMan' },
  { id: 17, name: 'Dynama' },
  { id: 18, name: 'Dr IQ' },
  { id: 19, name: 'Magma' },
  { id: 20, name: 'Tornado' }
];
```

## Displaying Heroes
Import mock-heroes.ts

`heroes.component.ts`

```TypeScript
import { HEROES } from './mock-heroes';
// Expose heroes for binding
heroes = HEROES;
```
## List heroes with **ngFor*

```HTML
<h2>My Heroes</h2>
<ul class="heroes">
    <!-- * asteriks critical part of the syntax -->
	 <li *ngFor='let hero of heroes'>  
         <!--Iterates over every hero in the list and does the following code with that hero in mind -->
        <span class="badge">{{hero.id}}</span> {{hero.name}}
        <!-- current hero.id + current hero.name -->
	</li>
</ul>
```
### Displays:

```
My Heroes
11 Mr. Nice
12 Narco
13 Bombasto
14 Celeritas
15 Magneta
16 RubberMan
17 Dynama
18 Dr IQ
19 Magma
20 Tornado
```

## Style the Heroes
Add private styling to the heroes components by pasting in the css given at the bottom of the page. 

## Add a click event binding

`heroes.component.html`

```HTML
<li *ngFor="let hero of heroes" (click)="onSelect(hero)">
<!-- Example of Angular's event binding -->
<!-- onSelect() is a HeroesCOmponent method that you're about to write -->
<!-- Passing the current hero object as an argument -->
```

## Add the click event handler

`heroes.compnent.ts`

```TypeScript
selectedHero : Hero;

onselect(hero : Hero): void {
  this.selectedHero = hero;
}
```

## Update the details template

`heroes.component.html`

```HTML
<h2>{{ selectedHero.name | uppercase }} Details</h2>
<div><span>id: </span>{{selectedHero.id}}</div>
<div>
  <label>name:
    <input [(ngModel)]="selectedHero.name" placeholder="name">
  </label>
</div>
```


## Hide empty details with **ngIf*

`Error in console`

```
HeroesComponent.html:3 ERROR TypeError: Cannot read property 'name' of undefined
```

### The Fix

`heroes.componetn.html`

```HTML
<div *ngIf="selectedHero">
  <!-- Wrap in div element with *ngIf = selectedHero -->

  <h2>{{ selectedHero.name | uppercase }} Details</h2>
  <div><span>id: </span>{{selectedHero.id}}</div>
  <div>
    <label>name:
      <input [(ngModel)]="selectedHero.name" placeholder="name">
    </label>
  </div>

</div>
```

### Why it works

- When selectedHero is *undefined*, the ngIf removes the hero detail from the     DOM.
- There are no selectedHero bindings to worry about.
- When the user picks a hero, selectedHero has a value and ngIf puts the hero     detail into the DOM. 

## Style the selected hero

`heroes.component.html`

```HTML
<ul class="heroes">
  <li *ngFor="let hero of heroes"
    [class.selected]="hero === selectedHero" 
    (click)="onSelect(hero)">
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>
<!-- When the hero isselected the selected class is applied -->
```

## Final Code Review
`heroes.component.css`
```CSS
/* HeroesComponent's private CSS styles */
.selected {
    background-color: #CFD8DC !important;
    color: white;
  }
  .heroes {
    margin: 0 0 2em 0;
    list-style-type: none;
    padding: 0;
    width: 15em;
  }
  .heroes li {
    cursor: pointer;
    position: relative;
    left: 0;
    background-color: #EEE;
    margin: .5em;
    padding: .3em 0;
    height: 1.6em;
    border-radius: 4px;
  }
  .heroes li.selected:hover {
    background-color: #BBD8DC !important;
    color: white;
  }
  .heroes li:hover {
    color: #607D8B;
    background-color: #DDD;
    left: .1em;
  }
  .heroes .text {
    position: relative;
    top: -3px;
  }
  .heroes .badge {
    display: inline-block;
    font-size: small;
    color: white;
    padding: 0.8em 0.7em 0 0.7em;
    background-color: #607D8B;
    line-height: 1em;
    position: relative;
    left: -1px;
    top: -4px;
    height: 1.8em;
    margin-right: .8em;
    border-radius: 4px 0 0 4px;
  }
```
`heroes.component.html`
```HTML
<h2>My Heroes</h2>
<ul class="heroes">
  <li *ngFor="let hero of heroes"
    [class.selected]="hero === selectedHero"
    (click)="onSelect(hero)">
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>
 
<div *ngIf="selectedHero">
 
  <h2>{{ selectedHero.name | uppercase }} Details</h2>
  <div><span>id: </span>{{selectedHero.id}}</div>
  <div>
    <label>name:
      <input [(ngModel)]="selectedHero.name" placeholder="name">
    </label>
  </div>
 
</div>
```
`heroes.component.ts`
```TypeScript
import { Component, OnInit } from '@angular/core';
import { Hero } from '../hero';
import { HEROES } from '../mock-heroes';
 
@Component({
  selector: 'app-heroes',
  templateUrl: './heroes.component.html',
  styleUrls: ['./heroes.component.css']
})
export class HeroesComponent implements OnInit {
 
  heroes = HEROES;
 
  selectedHero: Hero;
 
 
  constructor() { }
 
  ngOnInit() {
  }
 
  onSelect(hero: Hero): void {
    this.selectedHero = hero;
  }
}
```

# Master/Detail Components

- Refactor Master/Detail component into smaller more maintanable components

- Each component focuseed on a spesific task

## Make the HeroDetailComponent

`Console`
```sh
ng generate component hero-detail
```

## Write the template
`hero-detail.component.html`
```HTML
<div *ngIf="hero">
	<h2>{{ hero.name  | uppercase}} Details</h2>
	<div><span>id: </span>{{hero.id}}</div>
	<div>
		<label> name: 
			<input [(ngModel)]="hero.name" placeholder="name"/> 
		</label>
	</div>
</div>
```

## Add the @Input( ) hero property
`hero-detail.component.ts`
```TypeScript

import { Component, OnInit, Input} from '@angular/core';
/** Amend the @angular/core import statement to include the Input symbol. */
import { Hero } from '../hero';

@Component({
  selector: 'app-hero-detail',
  templateUrl: './hero-detail.component.html',
  styleUrls: ['./hero-detail.component.css']
})
export class HeroDetailComponent implements OnInit {
	/**  Add a hero property */
	@Input() hero: Hero;

	constructor() { }

	ngOnInit() {
	}

}
```

## Show the HeroDetailComponent
`heroes.compoentn.html`
```HTML
<app-hero-detail [hero]="selectedHero"></app-hero-detail>
```

## Final code review

`hero-detail.component.ts`
```ts
import { Component, OnInit, Input } from '@angular/core';
import { Hero } from '../hero';
 
@Component({
  selector: 'app-hero-detail',
  templateUrl: './hero-detail.component.html',
  styleUrls: ['./hero-detail.component.css']
})
export class HeroDetailComponent implements OnInit {
  @Input() hero: Hero;
 
  constructor() { }
 
  ngOnInit() {
  }
 
}
```

`hero-detail.component.html`
```HTML
<div *ngIf="hero">

  <h2>{{ hero.name | uppercase }} Details</h2>
  <div><span>id: </span>{{hero.id}}</div>
  <div>
    <label>name:
      <input [(ngModel)]="hero.name" placeholder="name"/>
    </label>
  </div>

</div>
```

`heroes.component.html`
```HTML
<h2>My Heroes</h2>

<ul class="heroes">
  <li *ngFor="let hero of heroes"
    [class.selected]="hero === selectedHero"
    (click)="onSelect(hero)">
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>

<app-hero-detail [hero]="selectedHero"></app-hero-detail>
```







