# Introduction
[Official Angular Cheatsheet](https://angular.io/guide/cheatsheet)

Install the [*Angular CLI*](https://angular.io/cli) globally on your computer (`npm install -g @angular/cli`).

Also install [*Nx*](https://nx.dev/latest/angular/getting-started/nx-setup) globally on your computer (`npm install -g nx`).
# Creating an Angular App
## Create the App

1. Create Angular workspace with an initial project using [`ng new`](https://angular.io/cli/new):

   ```bash
   ng new <app-name> --skip-git --skip-tests --strict --style scss --routing
   ```

2. Change directory into your new workspace: `cd <app-name>`

3. Delete auto-generated demo code in *app.component.html*

## Add Linter

1. Add [*ESLint*](https://eslint.org/) using [`ng add`](https://github.com/angular-eslint/angular-eslint#quick-start-with-angular-v12-and-later):

   ```bash
   ng add @angular-eslint/schematics --skip-confirmation
   ```

2. Add [TypeScript ESLint](https://github.com/typescript-eslint/typescript-eslint#readme):

   ```bash
   npm install --save-dev typescript @typescript-eslint/parser @typescript-eslint/eslint-plugin
   ```

   Next, find the */overrides/extends* section in *.eslintrc.json* and add two lines regarding TypeScript linting. The following code snippets shows how it should look like *after* your change:

   ```json
   {
        ...,
        "overrides": [
            ...,
            "extends": [
                "plugin:@angular-eslint/recommended",
                "plugin:@angular-eslint/template/process-inline-templates",
                "eslint:recommended",
                "plugin:@typescript-eslint/recommended"
            ],
            ...
        ],
        ...
   }
   ```

## Add Angular Material

1. You can choose any color scheme you like. Choose *yes* for all other questions.

    ```bash
    ng add @angular/material --skip-confirmation
    ```

2. For building responsive designs add [*Angular Flex Layout*](https://github.com/angular/flex-layout):

    ```bash
    npm install --save-dev @angular/flex-layout
    ```

3. Import `FlexLayoutModule` in *app.module.ts*

4. Try the following steps to ensure that your app works properly:

    | Step                                     | Command         |
    | ---------------------------------------- | --------------- |
    | Run linter (quality check for your code) | `npm run lint`  |
    | Build your app                           | `npm run build` |
    | Start debugging your app                 | `npm start`     |

# Code
## Data Binding
* **Interpolation**: ```{{ data }}```
* **Event Binding**: 
	* In .html: ```(click)="onClick()"```
	* In .ts: ```public onClick(){}```
* **One-Way**: ```[ data ]```
	* For input: ```<input type="number" [value]="data" />```
* **Two-Way** (banana-in-a-box):
	* Add *FormsModule* to *app.module.ts*
	* ```[( data )]```
* **Structural**: 
	* **If-Statement**: ```<p *ngIf="data > 50">This is a large number!</p>```
	* **For-Loop**: 
		```	
		<ul>
			<li *ngfor="let a of answers">
				{{ a }}
			</li>
		</ul>
		```