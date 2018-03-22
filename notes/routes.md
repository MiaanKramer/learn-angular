# Angular Routing
`TODO: add notes here about Angular's router module`

## Add the AppRoutingModule
`Console`
```sh
ng generate module app-routing --flat --module=app
```
- --flat puts the file in src/app instead of its own folder.
- --module=app tells the CLI to register it in the imports array of the AppModule.

`app-routing.module.ts` (generated)

```ts
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';

@NgModule({
  imports: [
    CommonModule
  ],
  declarations: []
/* To this */
imports: []
// --------^
})
export class AppRoutingModule { }
```

`app-routing.module.ts` (v1)
```ts
import { NgModule }             from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

@NgModule({
  exports: [ RouterModule ]
})
export class AppRoutingModule {}
```

## Add routes

Routes tell the router which view to display when a user clicks a link or pastes a URL into the browser address bar.

A typical Angular Route has two properties:

- Path: a string that matches the URL in the browser address bar.
- Component: the component that the router should create when navigating to this route.

`app-routing.module.ts`
```ts
import { NgModule }             from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HeroesComponent } from './heroes/heroes.component';

@NgModule({
  exports: [ RouterModule ]
})
export class AppRoutingModule {}

const routes: Routes = [
  { path: 'heroes', component: HeroesComponent }
];
```

## *RouterModule.forRoot()*

`app-routing.module.ts`
```ts
import { NgModule }             from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HeroesComponent } from './heroes/heroes.component';

const routes: Routes = [
  { path: 'heroes', component: HeroesComponent }
];

@NgModule({
  exports: [ RouterModule ],
  imports: [ RouterModule.forRoot(routes) ]
})
export class AppRoutingModule {}
```

## Add *RouterOutlet*

`app.component.html` (router-outlet)
```html
<h1>{{title}}</h1>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

You removed <app-heroes> because you will only display the HeroesComponent when the user navigates to it.

The <router-outlet> tells the router where to display routed views.

## Add a navigation link (routerLink)

`app.component.html`
```HTML
<h1>{{title}}</h1>
<nav>
  <a routerLink="/heroes">Heroes</a>
</nav>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

## Add a dashboard view

`Console`
```sh
ng generate component dashboard
```

`dashboard.component.html`
```HTML
<h3>Top Heroes</h3>
<div class="grid grid-pad">
  <a *ngFor="let hero of heroes" class="col-1-4">
    <div class="module hero">
      <h4>{{hero.name}}</h4>
    </div>
  </a>
</div>
```

`dashboard.component.ts`
```ts
import { Component, OnInit } from '@angular/core';
import { Hero } from '../hero';
import { HeroService } from '../hero.service';
 
@Component({
  selector: 'app-dashboard',
  templateUrl: './dashboard.component.html',
  styleUrls: [ './dashboard.component.css' ]
})
export class DashboardComponent implements OnInit {
  heroes: Hero[] = [];
 
  constructor(private heroService: HeroService) { }
 
  ngOnInit() {
    this.getHeroes();
  }
 
  getHeroes(): void {
    this.heroService.getHeroes()
      .subscribe(heroes => this.heroes = heroes.slice(1, 5));
  }
}
```

`dashboard.component.css`
```css
/* DashboardComponent's private CSS styles */
[class*='col-'] {
  float: left;
  padding-right: 20px;
  padding-bottom: 20px;
}
[class*='col-']:last-of-type {
  padding-right: 0;
}
a {
  text-decoration: none;
}
*, *:after, *:before {
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
h3 {
  text-align: center; margin-bottom: 0;
}
h4 {
  position: relative;
}
.grid {
  margin: 0;
}
.col-1-4 {
  width: 25%;
}
.module {
  padding: 20px;
  text-align: center;
  color: #eee;
  max-height: 120px;
  min-width: 120px;
  background-color: #607D8B;
  border-radius: 2px;
}
.module:hover {
  background-color: #EEE;
  cursor: pointer;
  color: #607d8b;
}
.grid-pad {
  padding: 10px 0;
}
.grid-pad > [class*='col-']:last-of-type {
  padding-right: 20px;
}
@media (max-width: 600px) {
  .module {
    font-size: 10px;
    max-height: 75px; }
}
@media (max-width: 1024px) {
  .grid {
    margin: 0;
  }
  .module {
    min-width: 60px;
  }
}
```
The template presents a grid of hero name links.

- The *ngFor repeater creates as many links as are in the component's heroes array.
- The links are styled as colored blocks by the dashboard.component.css.
- The links don't go anywhere yet but they will shortly.

The class is similar to the HeroesComponent class.

- It defines a heroes array property.
- The constructor expects Angular to inject the HeroService into a private heroService property.
- The ngOnInit() lifecycle hook calls getHeroes.

## Add the dashboard route

`app-routing.module.ts` (import DashboardComponent)
```ts
import { DashboardComponent }   from './dashboard/dashboard.component';
```
`app-routing.module.ts`
```ts
{ path: 'dashboard', component: DashboardComponent }
```

## Add a default route
`app-routing.module.ts`
```ts
{ path: '', redirectTo: '/dashboard', pathMatch: 'full' },
```

## Add dashboard link to the shell

`app.component.html`
```HTML
<h1>{{title}}</h1>
<nav>
  <a routerLink="/dashboard">Dashboard</a>
  <a routerLink="/heroes">Heroes</a>
</nav>
<router-outlet></router-outlet>
<app-messages></app-messages>
```

# Navigating the hero details
## Delete hero details from HeroesComponent
Open the HeroesComponent template (heroes/heroes.component.html) and delete the <app-hero-detail> element from the bottom.

## Add a hero detail route
A URL like ~/detail/11 would be a good URL for navigating to the Hero Detail view of the hero whose id is 11.

Open AppRoutingModule and import HeroDetailComponent.
`app-routing.module.ts` (import HeroDetailComponent)
```ts
import { HeroDetailComponent }  from './hero-detail/hero-detail.component';
```
`Routes` ++
```ts
{ path: 'detail/:id', component: HeroDetailComponent },
```

## DashboardComponent hero links
`dashboard.component.html` (hero links)
```html
<a *ngFor="let hero of heroes" class="col-1-4"
    routerLink="/detail/{{hero.id}}">
```

## HeroesComponent hero links
`heroes.component.html` (list with onSelect)
```html
<ul class="heroes">
  <li *ngFor="let hero of heroes"
    [class.selected]="hero === selectedHero"
    (click)="onSelect(hero)">
    <span class="badge">{{hero.id}}</span> {{hero.name}}
  </li>
</ul>

--->

<ul class="heroes">
  <li *ngFor="let hero of heroes">
    <a routerLink="/detail/{{hero.id}}">
      <span class="badge">{{hero.id}}</span> {{hero.name}}
    </a>
  </li>
</ul>
```

## Remove dead code (optional)

`heroes.component.ts`
```ts
export class HeroesComponent implements OnInit {
  heroes: Hero[];

  constructor(private heroService: HeroService) { }

  ngOnInit() {
    this.getHeroes();
  }

  getHeroes(): void {
    this.heroService.getHeroes()
    .subscribe(heroes => this.heroes = heroes);
  }
}
```

## Routable *HeroDetailComponent*
`hero-detail.component.ts`
```ts
import { ActivatedRoute } from '@angular/router';
import { Location } from '@angular/common';

import { HeroService }  from '../hero.service';

constructor(
    private route: ActivatedRoute,
    private heroService: HeroService,
    private location: Location
) { }
```

## Extract the *id* route parameter
`hero-detail.component.ts`
```ts
ngOnInit(): void {
  this.getHero();
}

getHero(): void {
  const id = +this.route.snapshot.paramMap.get('id');
  this.heroService.getHero(id)
    .subscribe(hero => this.hero = hero);
}
```

## Add HeroService.getHero( )
`hero.service.ts`
```ts
getHero(id: number): Observable<Hero> {
  // Todo: send the message _after_ fetching the hero
  this.messageService.add(`HeroService: fetched hero id=${id}`);
  return of(HEROES.find(hero => hero.id === id));
}
```

## Find the way back
`hero-detail.component.html` (back button)
```html
<button (click)="goBack()">go back</button>
```
`hero-detail.component.ts`
```ts
goBack(): void {
  this.location.back();
}
```




