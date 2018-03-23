# Angular HttpClient


`TODO: add notes here about Angular's http module`

## Enable HTTP services

**HttpClient** is Angular's mechanism for communicating with a remote server over HTTP.

To make **HttpClient** available everywhere in the app,

open the root **AppModule**,
import the **HttpClientModule** symbol from **@angular/common/http**,
add it to the **@NgModule.imports** array.

```ts
import { HttpClientModule }    from '@angular/common/http';

HttpClieantModule,
```

## Simulate a data server
`Console`
```sh
npm install angular-in-memory-web-api --save
```

`app.module.ts`
```ts
import { HttpClientInMemoryWebApiModule } from 'angular-in-memory-web-api';
import { InMemoryDataService }  from './in-memory-data.service';
under @NgModule.imports
HttpClientModule,

// The HttpClientInMemoryWebApiModule module intercepts HTTP requests
// and returns simulated server responses.
// Remove it when a real server is ready to receive requests.
HttpClientInMemoryWebApiModule.forRoot(
  InMemoryDataService, { dataEncapsulation: false }
)
```
`in-memory-data.service.ts`
```ts
import { InMemoryDbService } from 'angular-in-memory-web-api';

export class InMemoryDataService implements InMemoryDbService {
  createDb() {
    const heroes = [
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
    return {heroes};
  }
}
```
## Heroes and HTTP
`hero.service.ts`
```ts
import { HttpClient, HttpHeaders } from '@angular/common/http';

constructor(
  private http: HttpClient,
  private messageService: MessageService) { }

  private log(message: string) {
  this.messageService.add('HeroService: ' + message);

  rivate heroesUrl = 'api/heroes';  // URL to web api
}
```

## Get heroes with *HttpClient*
`hero.service.ts` (getHeroes with RxJs 'of( )')
```ts
getHeroes(): Observable<Hero[]> {
  return of(HEROES);
}

/** GET heroes from the server */
getHeroes (): Observable<Hero[]> {
  return this.http.get<Hero[]>(this.heroesUrl)
}
```

## Error handling

``` ts
`hero.service.ts`
import { catchError, map, tap } from 'rxjs/operators';

getHeroes (): Observable<Hero[]> {
  return this.http.get<Hero[]>(this.heroesUrl)
    .pipe(
      catchError(this.handleError('getHeroes', []))
    );
}

private handleError<T> (operation = 'operation', result?: T) {
  return (error: any): Observable<T> => {

    // TODO: send the error to remote logging infrastructure
    console.error(error); // log to console instead

    // TODO: better job of transforming error for user consumption
    this.log(`${operation} failed: ${error.message}`);

    // Let the app keep running by returning an empty result.
    return of(result as T);
  };
}
```

## Tap into the *Observable*

`hero.service.ts`

```ts
/** GET heroes from the server */
getHeroes (): Observable<Hero[]> {
  return this.http.get<Hero[]>(this.heroesUrl)
    .pipe(
      tap(heroes => this.log(`fetched heroes`)),
      catchError(this.handleError('getHeroes', []))
    );
}
```

## Get hero by id
`/hero.service.ts`
Most web APIs support a get by id request in the form api/hero/:id (such as api/hero/11). Add a HeroService.getHero() method to make that request:
```ts
/** GET hero by id. Will 404 if id not found */
getHero(id: number): Observable<Hero> {
  const url = `${this.heroesUrl}/${id}`;
  return this.http.get<Hero>(url).pipe(
    tap(_ => this.log(`fetched hero id=${id}`)),
    catchError(this.handleError<Hero>(`getHero id=${id}`))
  );
}
```
There are three significant differences from **getHeroes( )**.

- it constructs a request URL with the desired hero's id.
- the server should respond with a single hero rather than an array of heroes.
- therefore, `getHero` returns an `Observable<Hero>` ("an observable of Hero objects") rather than an observable of hero arrays .

## Update heroes
`hero-detail.component.html` (save)
```html
<button (click)="save()">save</button>
```

`hero-detail.component.ts`
```ts
save(): void {
   this.heroService.updateHero(this.hero)
     .subscribe(() => this.goBack());
 }
```

## Add *HeroService.updateHero()*

`hero.service.ts`
```ts 
/** PUT: update the hero on the server */
updateHero (hero: Hero): Observable<any> {
  return this.http.put(this.heroesUrl, hero, httpOptions).pipe(
    tap(_ => this.log(`updated hero id=${hero.id}`)),
    catchError(this.handleError<any>('updateHero'))
  );
}

const httpOptions = {
  headers: new HttpHeaders({ 'Content-Type': 'application/json' })
};
```

### <u>The HttpClient.put() method takes three parameters</u>

- The URL.
- The data to update (the modified hero in this case).
- Options.

## Add a new hero
`heroes/heroes.component.html` (Add)
```html 
<div>
  <label>Hero name:
    <input #heroName />
  </label>
  <!-- (click) passes input value to add() and then clears the input -->
  <button (click)="add(heroName.value); heroName.value=''">
    add
  </button>
</div>
```

`heroes/heroes.component.ts` (Add)
```ts
add(name: string): void {
  name = name.trim();
  if (!name) { return; }
  this.heroService.addHero({ name } as Hero)
    .subscribe(hero => {
      this.heroes.push(hero);
    });
}
```

## Add *HeroService.addHero( )*
`hero.service.ts` (AddHero)

```ts
/** POST: add a new hero to the server */
addHero (hero: Hero): Observable<Hero> {
  return this.http.post<Hero>(this.heroesUrl, hero, httpOptions).pipe(
    tap((hero: Hero) => this.log(`added hero w/ id=${hero.id}`)),
    catchError(this.handleError<Hero>('addHero'))
  );
}
```

## Delete a hero

`heroes/heroes.component.html` (Delete)
```html
<ul class="heroes">
  <li *ngFor="let hero of heroes">
    <a routerLink="/detail/{{hero.id}}">
      <span class="badge">{{hero.id}}</span> {{hero.name}}
    </a>
    <button class="delete" title="delete hero"
    (click)="delete(hero)">x</button>
  </li>
</ul>
```

`heroes.component.ts`
```ts 
delete(hero: Hero): void {
  this.heroes = this.heroes.filter(h => h !== hero);
  this.heroService.deleteHero(hero).subscribe();
}
```

<u>**`If you neglect to subscribe(), the service will not send the delete request to the server! As a rule, an Observable does nothing until something subscribes!`**

**`Confirm this for yourself by temporarily removing the subscribe(), clicking "Dashboard", then clicking "Heroes". You'll see the full list of heroes again.`**</u>

## Add *HeroService.deleteHero( )*
`hero.service.ts` (Delete)
```ts
/** DELETE: delete the hero from the server */
deleteHero (hero: Hero | number): Observable<Hero> {
  const id = typeof hero === 'number' ? hero : hero.id;
  const url = `${this.heroesUrl}/${id}`;

  return this.http.delete<Hero>(url, httpOptions).pipe(
    tap(_ => this.log(`deleted hero id=${id}`)),
    catchError(this.handleError<Hero>('deleteHero'))
  );
}
```

<u>Note that :</u>

- It calls HttpClient.delete.
- The URL is the heroes resource URL plus the id of the hero to delete
- You don't send data as you did with put and post.
- You still send the httpOptions

## Search by name

### *HeroService.searchHeroes*

`hero.service.ts`
```ts
/* GET heroes whose name contains search term */
searchHeroes(term: string): Observable<Hero[]> {
  if (!term.trim()) {
    // if not search term, return empty hero array.
    return of([]);
  }
  return this.http.get<Hero[]>(`api/heroes/?name=${term}`).pipe(
    tap(_ => this.log(`found heroes matching "${term}"`)),
    catchError(this.handleError<Hero[]>('searchHeroes', []))
  );
}
```

## Add search to the Dashboard

`dashboard.component.html`
```html
<h3>Top Heroes</h3>
<div class="grid grid-pad">
  <a *ngFor="let hero of heroes" class="col-1-4"
      routerLink="/detail/{{hero.id}}">
    <div class="module hero">
      <h4>{{hero.name}}</h4>
    </div>
  </a>
</div>

<app-hero-search></app-hero-search>
```

## Create *HeroSearchComponent*
`Console`
```sh
ng generate component hero-search
```

`hero-search.component.html`
```html
<div id="search-component">
  <h4>Hero Search</h4>

  <input #searchBox id="search-box" (keyup)="search(searchBox.value)" />

  <ul class="search-result">
    <li *ngFor="let hero of heroes$ | async" >
      <a routerLink="/detail/{{hero.id}}">
        {{hero.name}}
      </a>
    </li>
  </ul>
</div>
```

## Fix the *HeroSearchComponent* class
`hero-search.component.ts`'
```ts
import { Component, OnInit } from '@angular/core';

import { Observable } from 'rxjs/Observable';
import { Subject }    from 'rxjs/Subject';
import { of }         from 'rxjs/observable/of';

import {
   debounceTime, distinctUntilChanged, switchMap
 } from 'rxjs/operators';

import { Hero } from '../hero';
import { HeroService } from '../hero.service';

@Component({
  selector: 'app-hero-search',
  templateUrl: './hero-search.component.html',
  styleUrls: [ './hero-search.component.css' ]
})
export class HeroSearchComponent implements OnInit {
  heroes$: Observable<Hero[]>;
  private searchTerms = new Subject<string>();

  constructor(private heroService: HeroService) {}

  // Push a search term into the observable stream.
  search(term: string): void {
    this.searchTerms.next(term);
  }

  ngOnInit(): void {
    this.heroes$ = this.searchTerms.pipe(
      // wait 300ms after each keystroke before considering the term
      debounceTime(300),

      // ignore new term if same as previous term
      distinctUntilChanged(),

      // switch to new search observable each time the term changes
      switchMap((term: string) => this.heroService.searchHeroes(term)),
    );
  }
}
```

## Chaining RxJS operators

Passing a new search term directly to the searchHeroes() after every user keystroke would create an excessive amount of HTTP requests, taxing server resources and burning through the cellular network data plan.

Instead, the ngOnInit() method pipes the searchTerms observable through a sequence of RxJS operators that reduce the number of calls to the searchHeroes(), ultimately returning an observable of timely hero search results (each a Hero[]).

Here's the code :
``` ts
this.heroes$ = this.searchTerms.pipe(
  // wait 300ms after each keystroke before considering the term
  debounceTime(300),

  // ignore new term if same as previous term
  distinctUntilChanged(),

  // switch to new search observable each time the term changes
  switchMap((term: string) => this.heroService.searchHeroes(term)),
);
```
- debounceTime(300) waits until the flow of new string events pauses for 300 milliseconds before passing along the latest string. You'll never make requests more frequently than 300ms.
- distinctUntilChanged ensures that a request is sent only if the filter text changed.
- switchMap() calls the search service for each search term that makes it through debounce and distinctUntilChanged. It cancels and discards previous search observables, returning only the latest search service observable.

# Summary
- You added the necessary dependencies to use HTTP in the app.
- You refactored HeroService to load heroes from a web API.
- You extended HeroService to support post(), put(), and delete() methods.
- You updated the components to allow adding, editing, and deleting of heroes.
- You configured an in-memory web API.
- You learned how to use Observables.