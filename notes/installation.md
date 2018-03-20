# Installing Angular

## Install Angular CLI

`TODO: describe the steps to install angular cli`
```sh
npm install -g @angular/cli
```
(-g) installs @angular/cli globally on you machine

npm is a package manager for javaScript

## Create a new Angular application

`TODO: describe the steps to create a new application`

```sh
ng new angular-tour-of-heroes
```
Wait for files to be installed

## Serve the application for development

`TODO: describe the steps to serve the application using the ng serve command`

cd into application 
```sh
cd angular-tour-of-heroes
```
Run this line of code to serve the application and open it
```
ng serve --open
```


## Build the application for production

`TODO: describe the steps to build the application using the ng build command`

Run 
```sh
ng build 
```
- Compiles the application into an output directory

- It builds the application for production use other than hosting it in memory that serve does

## Summary

- You created the initial application structure using the Angular CLI.

- You learned that Angular components display data.

- You used the double curly braces of interpolation to display the app title.

**Files Discussed On This Page**

/src/app/app.components.ts -> typeScript (title = 'Tour of Heroes')

/src/styles.css -> Application-wide Styles


