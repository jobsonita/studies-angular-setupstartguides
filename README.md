# studies-angular-setupstartguides

Angular Setup Guide: https://angular.io/guide/setup-local  
Angular Start Guide: https://angular.io/start

## Angular structure

Basic: Components, Templates, Dependency Injection  
https://angular.io/guide/what-is-angular

In Depth: Modules, Components, Directives, Text Interpolation, Data Binding, Pipes, Services, Routing, Dependency Injection, and more...  
https://angular.io/guide/understanding-angular-overview  
https://angular.io/guide/architecture

### Components

#### Routing

A router is a module that decides which Component to render on certain outlets by matching against url paths. Read more about it at https://angular.io/guide/router

In this project, [`src/app/app.module.ts`](src/app/app.module.ts) creates a router module by using the [`RouterModule.forRoot()`](https://angular.io/start/start-routing#associate-a-url-path-with-a-component) function with instructions on how to handle our routes:

```typescript
@NgModule({
  imports: [
    BrowserModule,
    ReactiveFormsModule,
    RouterModule.forRoot([
      { path: '', component: ProductListComponent },
      { path: 'products/:productId', component: ProductDetailsComponent },
    ])
  ],
  declarations: [
    AppComponent,
    TopBarComponent,
    ProductListComponent,
    ProductAlertsComponent,
    ProductDetailsComponent,
  ],
  bootstrap: [
    AppComponent
  ]
})
export class AppModule { }
```

And `src/app/app.component.html` defines the [`RouterOutlet`](https://angular.io/api/router/RouterOutlet) where our content will be rendered:

```html
<app-top-bar></app-top-bar>

<div class="container">
  <router-outlet></router-outlet>
</div>
```

Once a router is configured to handle the url paths of our site, we can use the [`RouterLink`](https://angular.io/api/router/RouterLink) directive in our anchor tags to generate the correct urls to reach each of our pages:

```html
<a
  [title]="product.name + ' details'"
  [routerLink]="['/products', product.id]">
  {{ product.name }}
</a>
```

And when we navigate to the specified link, we can use the [`ActivatedRoute`](https://angular.io/api/router/ActivatedRoute) service to retrieve information about the [current route snapshot](https://angular.io/api/router/ActivatedRouteSnapshot):

```js
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

import { Product, products } from '../products';

@Component({
  selector: 'app-product-details',
  templateUrl: './product-details.component.html',
  styleUrls: ['./product-details.component.css']
})
export class ProductDetailsComponent implements OnInit {

  product: Product | undefined;

  constructor(private route: ActivatedRoute) { }

  ngOnInit(): void {
    // First get the product id from the current route.
    const routeParams = this.route.snapshot.paramMap;
    const productIdFromRoute = Number(routeParams.get('productId'));

    // Find the product that correspond with the id provided in route.
    this.product = products.find(product => product.id === productIdFromRoute);
  }

}

```

### Templates

#### Structural Directives

##### [NgFor](https://angular.io/api/common/NgFor)

```html
<p *ngFor="let item of list">
  This paragraph will be replicated as many times as the number of items in list.
</p>
```

Behind the scenes:

```html
<ng-template ngFor let-item [ngForOf]="list">
  <p>This paragraph will be replicated as many times as the number of items in list.</p>
</ng-template>
```

> **Note**: NgFor passes other received attributes down to its children

> **Note**: NgFor has various local variables that can be used as described at https://angular.io/api/common/NgFor#local-variables

##### [NgIf](https://angular.io/api/common/NgIf)

```html
<p *ngIf="condition">
  This will be displayed when condition is true.
</p>
```

Behind the scenes:

```html
<ng-template [ngIf]="condition">
  <p>This will be displayed when condition is true.</p>
</ng-template>
```

If-else:

```html
<p *ngIf="condition; else elseBlock">This will be displayed when condition is true.</p>
<ng-template #elseBlock>This will be displayed when condition is false.</ng-template>
```

Waiting for asynchronous data:

```html
<button (click)="nextUser()">Next User</button>

<div *ngIf="userObservable | async as user; else loading">
  Hello {{user.last}}, {{user.first}}!
</div>
<ng-template #loading let-user>Waiting... (user is {{user|json}})
```

```typescript
userObservable = new Subject<{first: string, last: string}>();
first = ['John', 'Mike', 'Mary', 'Bob'];
firstIndex = 0;
last = ['Smith', 'Novotny', 'Angular'];
lastIndex = 0;

nextUser() {
  let first = this.first[this.firstIndex++];
  if (this.firstIndex >= this.first.length) this.firstIndex = 0;
  let last = this.last[this.lastIndex++];
  if (this.lastIndex >= this.last.length) this.lastIndex = 0;
  this.userObservable.next({first, last});
}
```

#### Data Binding

##### [Text Interpolation](https://angular.io/guide/interpolation)

```html
<p>{{title}}</p>
<div><img alt="item.description" src="{{item.imageUrl}}"></div>
```

##### [Property Binding](https://angular.io/guide/property-binding)

```html
<img alt="The image description is static" [src]="aBoundImageUrlThatIsDynamic">
```

##### [Event Binding](https://angular.io/guide/event-binding)

```html
<button (click)="onSave()">Save</button>
```

Binding to keyboard events:

```html
<input (keydown.shift.t)="onKeydown($event)" />
```

> **Note**: each OS handles key codes in different ways, so make sure to check this link before using them: https://angular.io/guide/event-binding#binding-to-keyboard-events

#### Parent and Children Communication

https://angular.io/guide/component-interaction

##### [@Input](https://angular.io/api/core/Input)

##### [@Output](https://angular.io/api/core/Output)

#####  [EventEmitter](https://angular.io/api/core/EventEmitter)

```typescript
export class ProductAlertsComponent {
  @Input() product: Product | undefined;
  @Output() notify = new EventEmitter();
}
```

```html
<!-- product-alerts.component.html -->
<button type="button" (click)="notify.emit()">Notify Me</button>
```

```html
<!-- product-list.component.html -->
<app-product-alerts [product]="product" (notify)="onNotify()" />
```

```typescript
export class ProductListComponent {
  products = [...products];
  onNotify() {
    window.alert('You will be notified when the product goes on sale');
  }
}
```

### Pipes

https://angular.io/guide/pipes

#### [Currency](https://angular.io/api/common/CurrencyPipe)

```html
<span>{{ product.price | currency }}</span>
```

Example of Brazilian Real output with 4 integer digits and min of 2 fractional and max of 2 fractional digits in pt-br locale:

```html
<!--output 'R$ 1.234,56' -->
<span>{{ '1234.56' | currency:'BRL':'symbol':'4.2-2':'pt-BR'}}</span>
```

If you run into problems using a locale, refer to the [locale related errors](#locale-related-errors) section down below.

#### [Async](https://angular.io/api/common/AsyncPipe)

```html
<button (click)="updateObservableList()">Refresh</button>

<div *ngFor="let item of observableList | async">
  <span>{{ item.description }}</span>
</div>
```

```typescript
import { Observable } from 'rxjs'

interface Item {
  description: string;
}

export class MyComponent implements OnInit {
  observableList!: Observable<Item[]>;

  constructor(private someService: SomeService) { }

  ngOnInit(): void {
    // retrieves the list when the component first shows up
    this.observableList = this.someService.getObservableList();
  }

  updateObservableList() {
    // used to refresh the list when we press the refresh button
    this.observableList = this.someService.getObservableList();
  }
}
```

### Services

A service is any value, function or feature that we can make available to any part of our application through Angular's dependency injection system. Read more at https://angular.io/guide/architecture-services

Global service (available to any part of our app):

```typescript
import { Product } from './products';
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class CartService {
  items: Product[] = [];

  addToCart(product: Product) {
    this.items.push(product);
  }

  getItems() {
    return this.items;
  }

  clearCart() {
    this.items = [];
    return this.items;
  }
}
```

Using our global service:

```typescript
/* ... */
import { Product, products } from '../products';
import { CartService } from '../cart.service';

export class ProductDetailsComponent implements OnInit {
  /* ... */
  constructor(
    private route: ActivatedRoute,
    private cartService: CartService
  ) { }
  /* ... */
  addToCart(product: Product) {
    this.cartService.addToCart(product);
    window.alert('Your product has been added to the cart!');
  }
}
```

```html
<h2>Product Details</h2>

<div *ngIf="product">
  <h3>{{ product.name }}</h3>
  <h4>{{ product.price | currency }}</h4>
  <p>{{ product.description }}</p>
  <button type="button" (click)="addToCart(product)">Buy</button>
</div>
```

### Dependency Injection

A mechanism through which parts of our app (dependencies) are provided to other parts of our app that require them. Read more at https://angular.io/guide/dependency-injection

#### [Injector](https://angular.io/guide/glossary#injector)

#### [Injectable](https://angular.io/guide/glossary#injectable)

@Injectable annotation: https://angular.io/api/core/Injectable

#### [Provider](https://angular.io/guide/glossary#provider)

#### [DI Token](https://angular.io/guide/glossary#di-token)

## Checking NPM and Node installation

Run the following commands on the terminal to see whether NPM and Node are installed:

```bash
npm --version
```

```bash
node --version
```

## Angular CLI

We need to install Angular CLI globally to perform the various scripts that come with it:

```bash
npm install -g @angular/cli
```

Once installed, the `ng` command will be available to us in the terminal. For more info:

```bash
ng --help
ng new --help
```

CLI command overview: https://angular.io/cli#command-overview

## Creating an Angular Project

To create an Angular project, run the command:

> ng new <project_name>

`<project_name>` can be any name. Also, we'll pass an additional argument, `--directory .`, to force Angular to create the project at the root of the repository, since we won't have any other projects in it (though we could, since Angular allows it, as explained on https://angular.io/cli#workspaces-and-project-files and https://angular.io/guide/file-structure#multiple-projects).

```bash
ng new SetupAndStartGuides --directory .
```

The newly created project has many files. The exact project structure is explained on https://angular.io/guide/file-structure#workspace-configuration-files and https://angular.io/guide/file-structure#application-project-files

## Sharing current project with other developers

On a new machine, after cloning the project, install its dependencies by running the following command:

```bash
npm install
```

## Running the project

To run the project, use the following command:

```bash
ng serve
```

Alternatively, you can use `npm start` which calls the same command above.

When running on Codespaces, it is necessary to pass the `--host=0.0.0.0` option. I recommend editting the `start` script on `package.json` to include that option and using `npm start` to run the project.

## Creating a Component

To create a new component, use the following command:

> ng generate component <component_name>

In this project, we create the component `product-alerts` that goes into the `src/app/product-alerts` folder. Angular automatically creates the folder and its files, and also updates `app.module.ts` to include our component in its declarations list.

```bash
ng generate component product-alerts
```

## Creating a Service

To create a new service, use the following command:

> ng generate service <service_name>

In this project, we create the service `CartService` that goes into the `src/app/cart-service.ts` file.

```bash
ng generate service cart
```

## Special Modules and Services

### HttpClient and HttpClientModule

#### [HttpClientModule](https://angular.io/api/common/http/HttpClientModule)

```typescript
import { HttpClientModule } from '@angular/common/http';
/*...*/
@NgModule({
  imports: [
    BrowserModule,
    HttpClientModule,
    /*...*/
  ],
  /*...*/
})
export class AppModule { }
```

#### [HttpClient](https://angular.io/api/common/http/HttpClient)

```typescript
import { HttpClient } from '@angular/common/http';
/*...*/
export class MyService {
  /*...*/
  constructor(private http: HttpClient) {}
  /*...*/
  getDataFromTheInternet() {
    return this.http.get<DataType[]>(myurl);
  }
}
```

## Common problems

### webpack-dev-server Invalid Host/Origin header

If webpack keeps complaining about the host configuration, pass the `--disable-host-check` argument to `ng serve` to make it skip that check.

### Locale-related errors

Before using locales other than en-US, we need to configure our app by either setting its default locale or importing and registering them in our app. Here's the import version:

```typescript
import { registerLocaleData } from '@angular/common';
import { LOCALE_ID, NgModule } from '@angular/core';
...
import localePt from '@angular/common/locales/pt';

registerLocaleData(localePt);

@NgModule({
  ...
  providers: [
    {provide: LOCALE_ID, useValue: 'pt-BR' }
  ],
  ...
})
export class AppModule { }
```
