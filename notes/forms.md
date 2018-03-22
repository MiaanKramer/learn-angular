# Angular Forms

`TODO: add notes here about Angular's forms module`

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