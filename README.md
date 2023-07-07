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

## Common problems

### webpack-dev-server Invalid Host/Origin header

If webpack keeps complaining about the host configuration, pass the `--disable-host-check` argument to `ng serve` to make it skip that check.
