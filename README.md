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

### Dependency Injection

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

> ng generate component <component-name>

In this project, we create the component `product-alerts`, that goes into the `src/app/product-alerts` folder. Angular automatically creates the folder and its files, and also updates `app.module.ts` to include our component in its declarations list.

```bash
ng generate component product-alerts
```

## Common problems

### webpack-dev-server Invalid Host/Origin header

If webpack keeps complaining about the host configuration, pass the `--disable-host-check` argument to `ng serve` to make it skip that check.
