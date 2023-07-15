[Read this file in English](../../README.md)

# studies-angular-setupstartguides

Guia de Configuração do Angular: https://angular.io/guide/setup-local  
Guia de Iniciação no Angular: https://angular.io/start

## Estrutura do Angular

Básico: Componentes, Modelos de Layout, Injeção de Dependências  
https://angular.io/guide/what-is-angular

Avançado: Módulos, Componentes, Diretivas, Interpolação de Texto, Ligação de Dados, Pipes (Transformação de Dados), Serviços, Roteamento, Injeção de Dependências, etc...  
https://angular.io/guide/understanding-angular-overview  
https://angular.io/guide/architecture

### Componentes

https://angular.io/guide/component-overview

Avance para [Criando um Componente](#criando-um-componente) para maiores informações.

### Roteamento

Um roteador é um módulo que decide através do caminho da url qual Componente será exibido em áreas predeterminadas. Leia mais sobre isso em https://angular.io/guide/router

Neste projeto, [`src/app/app.module.ts`](src/app/app.module.ts) cria um roteador ao usar a função [`RouterModule.forRoot()`](https://angular.io/start/start-routing#associate-a-url-path-with-a-component) com instruções de como gerir as rotas:

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

E `src/app/app.component.html` define a área predeterminada [`RouterOutlet`](https://angular.io/api/router/RouterOutlet) onde o conteúdo será exibido:

```html
<app-top-bar></app-top-bar>

<div class="container">
  <router-outlet></router-outlet>
</div>
```

Após configurarmos um roteador para gerir os caminhos do nosso site, podemos usar a diretiva [`RouterLink`](https://angular.io/api/router/RouterLink) nos elementos âncora para gerar os endereços corretos para as páginas do nosso site:

```html
<a
  [title]="product.name + ' details'"
  [routerLink]="['/products', product.id]">
  {{ product.name }}
</a>
```

E quando navegarmos para a página indicada, podemos usar o serviço [`ActivatedRoute`](https://angular.io/api/router/ActivatedRoute) para recuperar informações sobre o [contexto da rota atual](https://angular.io/api/router/ActivatedRouteSnapshot):

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
    // Primeiro, pegamos o id do produto do contexto da rota.
    const routeParams = this.route.snapshot.paramMap;
    const productIdFromRoute = Number(routeParams.get('productId'));

    // E depois encontramos o produto correspondente ao id fornecido.
    this.product = products.find(product => product.id === productIdFromRoute);
  }

}

```

### Modelos de Layout

#### Diretivas Estruturais

##### [NgFor](https://angular.io/api/common/NgFor)

```html
<p *ngFor="let item of list">
  Este parágrafo será replicado tantas vezes quanto for o número de itens na lista.
</p>
```

Nos bastidores:

```html
<ng-template ngFor let-item [ngForOf]="list">
  <p>Este parágrafo será replicado tantas vezes quanto for o número de itens na lista.</p>
</ng-template>
```

> **Observação**: NgFor repassa os demais atributos recebidos para seu elemento filho

> **Observação**: NgFor possui diversas variáveis locais que podem ser utilizadas conforme descrito em https://angular.io/api/common/NgFor#local-variables

##### [NgIf](https://angular.io/api/common/NgIf)

```html
<p *ngIf="condition">
  Isto será exibido quando a condição for verdadeira.
</p>
```

Nos bastidores:

```html
<ng-template [ngIf]="condition">
  <p>Isto será exibido quando a condição for verdadeira.</p>
</ng-template>
```

If-else:

```html
<p *ngIf="condition; else elseBlock">Isto será exibido quando a condição for verdadeira.</p>
<ng-template #elseBlock>Isto será exibido quando a condição for falsa.</ng-template>
```

Aguardando por dados assíncronos:

```html
<button (click)="nextUser()">Próximo Usuário</button>

<div *ngIf="userObservable | async as user; else loading">
  Olá {{user.last}}, {{user.first}}!
</div>
<ng-template #loading let-user>Aguardando... (user is {{user|json}})
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

#### Ligação de Dados

##### [Interpolação de Texto](https://angular.io/guide/interpolation)

```html
<p>{{title}}</p>
<div><img alt="item.description" src="{{item.imageUrl}}"></div>
```

##### [Ligação de Propriedade](https://angular.io/guide/property-binding)

```html
<img alt="A descrição da imagem é estática" [src]="umaUrlDeImagemQueEhDinamica">
```

##### [Ligação de Evento](https://angular.io/guide/event-binding)

```html
<button (click)="onSave()">Salvar</button>
```

Ligando-se a eventos do teclado:

```html
<input (keydown.shift.t)="onKeydown($event)" />
```

> **Observação**: cada sistema operacional lida com códigos de tecla de modos diferentes, então certifique-se de checar esta página antes de usá-los: https://angular.io/guide/event-binding#binding-to-keyboard-events

#### Comunicação entre Elemento Pai e Filhos

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
<button type="button" (click)="notify.emit()">Notifique-me</button>
```

```html
<!-- product-list.component.html -->
<app-product-alerts [product]="product" (notify)="onNotify()" />
```

```typescript
export class ProductListComponent {
  products = [...products];
  onNotify() {
    window.alert('Você será notificado quando o produto estiver em promoção');
  }
}
```

### Pipes (Transformação de Dados)

https://angular.io/guide/pipes

#### [Currency](https://angular.io/api/common/CurrencyPipe) (Valor Monetário)

```html
<span>{{ product.price | currency }}</span>
```

Exemplo de exibição de valor em Real com 4 dígitos inteiros, mínimo e máximo de 2 dígitos fracionários, em Português do Brasil:

```html
<!--dado exibido 'R$ 1.234,56' -->
<span>{{ '1234.56' | currency:'BRL':'symbol':'4.2-2':'pt-BR'}}</span>
```

Se você encontrar problemas ao usar o código de local, verifique a seção de [erros de código de local](#erros-de-codigo-de-local) mais abaixo.

#### [Async](https://angular.io/api/common/AsyncPipe)

```html
<button (click)="updateObservableList()">Atualizar</button>

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
    // obtém a lista quando o componente é exibido pela primeira vez
    this.observableList = this.someService.getObservableList();
  }

  updateObservableList() {
    // usado para atualizar a lista quando apertamos o botão de atualizar
    this.observableList = this.someService.getObservableList();
  }
}
```

### Serviços

Um serviço é qualquer valor, função ou funcionalidade que desejamos disponibilizar para outras partes da nossa aplicação através do sistema de injeção de dependências do Angular. Leia mais em https://angular.io/guide/architecture-services

Serviço global (disponível para todas as partes da nossa aplicação):

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

Usando nosso serviço global:

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
    window.alert('Seu produto foi adicionado ao carrinho!');
  }
}
```

```html
<h2>Detalhes do Produto</h2>

<div *ngIf="product">
  <h3>{{ product.name }}</h3>
  <h4>{{ product.price | currency }}</h4>
  <p>{{ product.description }}</p>
  <button type="button" (click)="addToCart(product)">Comprar</button>
</div>
```

### Injeção de Dependências

Um mecanismo pelo qual partes da nossa aplicação (dependências) são disponibilizadas para outras partes que precisem delas. Leia mais em https://angular.io/guide/dependency-injection

#### [Injector](https://angular.io/guide/glossary#injector) (Injetor)

#### [Injectable](https://angular.io/guide/glossary#injectable) (Injetável)

Anotação @Injectable: https://angular.io/api/core/Injectable

#### [Provider](https://angular.io/guide/glossary#provider) (Provedor, "aquele que provê/disponibiliza algo")

#### [DI Token](https://angular.io/guide/glossary#di-token) (Símbolo de Injeção de Dependência)

## Checagem da instalação do NPM e do Node

Execute os comandos a seguir no terminal para checar se o NPM e o Node estão instalados:

```bash
npm --version
```

```bash
node --version
```

## Angular CLI (Interface de Linha de Comando do Angular)

Precisamos instalar a Angular CLI globalmente para poder executar os diversos comandos que ela disponibiliza:

```bash
npm install -g @angular/cli
```

Após instalada, o comando `ng` estará disponível no terminal. Para mais informações:

```bash
ng --help
ng new --help
```

Visão geral dos comandos da CLI: https://angular.io/cli#command-overview

## Criando um Projeto em Angular

Para criar um projeto em Angular, execute o comando:

> ng new <nome_do_projeto>

`<nome_do_projeto>` pode ser qualquer nome. Além disso, passamos um argumento adicional, `--directory .`, para forçar o Angular a criar o projeto na raiz do repositório, uma vez que não teremos outros projetos nele (embora seja possível, pois o Angular permite isso, como explicado em https://angular.io/cli#workspaces-and-project-files e https://angular.io/guide/file-structure#multiple-projects).

```bash
ng new SetupAndStartGuides --directory .
```

O projeto recém criado tem vários arquivos. Os detalhes dessa estrutura são explicados em https://angular.io/guide/file-structure#workspace-configuration-files e https://angular.io/guide/file-structure#application-project-files

## Compartilhando o projeto atual com outros desenvolvedores

Em outra máquina, após clonar o projeto, instale as dependências executando o seguinte comando:

```bash
npm install
```

## Executando o projeto

Para executar o projeto, use o seguinte comando:

```bash
ng serve
```

Alternativamente, podemos usar `npm start` que executa o mesmo comando acima.

Quando rodamos nosso projeto no Codespaces, é necessário utilizar o argumento `--host=0.0.0.0`. Recomendo alterar o comando `start` em `package.json` para incluir esse argumento e utilizar `npm start` para rodar o projeto.

## Criando um Componente

Para criar um novo componente, use o seguinte comando:

> ng generate component <nome_do_componente>

Neste projeto, criamos o componente `product-alerts` que é gerado no diretório `src/app/product-alerts`. O Angular cria automaticamente o diretório e seus arquivos, e também atualiza `app.module.ts` com a inclusão do nosso component na lista de declarações.

```bash
ng generate component product-alerts
```

## Criando um Serviço

Para criar um novo serviço, use o seguinte comando:

> ng generate service <nome_do_servico>

Neste projeto, criamos o serviço `CartService` que é gerado no arquivo `src/app/cart-service.ts`.

```bash
ng generate service cart
```

## Serviços e Módulos Especiais

### HttpClient e HttpClientModule

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
  obterDadosDaRede() {
    return this.http.get<DataType[]>(myurl);
  }
}
```

### FormBuilder e ReactiveFormsModule

#### [ReactiveFormsModule](https://angular.io/api/forms/ReactiveFormsModule)

```typescript
import { ReactiveFormsModule } from '@angular/forms';
/**/
@NgModule({
  imports: [
    BrowserModule,
    ReactiveFormsModule,
    /*...*/
  ],
  /*...*/
})
export class AppModule { }
```

**Como ele funciona:**

Em cada formulário dos nossos modelos de layout, ele inclui um Input `formGroup` e um Output `ngSubmit`. Nós ligamos o formGroup a um objeto criado por `FormBuilder.group()` que gerencia os inputs do nosso formulário (e também pode executar validações), e ligamos o ngSubmit a uma função que irá tratar do evento de envio do formulário. Também precisamos configurar o atributo [`formControlName`](https://angular.io/api/forms/FormControlName) dos nossos inputs para os respectivos nomes no formGroup, para que ele possa identificar quais inputs ele precisa controlar.

Alternativamente, podemos trabalhar com [FormControls](https://angular.io/api/forms/FormControl), uma forma potencialmente mais verbosa, mas que possibilita maior flexibilidade e extensibilidade.

```html
<form [formGroup]="loginForm" (ngSubmit)="onSubmit()">
  <input formControlName="username" placeholder="Digite seu nome de usuário" />
  <input formControlName="password" type="password" placeholder="Digite sua senha" />
  <button type="submit">Entrar</button>
</form>
```

#### [FormBuilder](https://angular.io/api/forms/FormBuilder)

```typescript
import { FormBuilder } from '@angular/forms';
/*...*/
export class MyComponent {
  loginForm = this.formBuilder.group({
    username: '',
    password: '',
  });

  constructor(private formBuilder: FormBuilder) { }

  onSubmit(): void {
    console.warn('Estes foram os valores dos campos do formulário:', this.loginForm.value);
    this.loginForm.reset();
  }
}
```

## Problemas comuns

### webpack-dev-server Invalid Host/Origin header

Se o webpack reclamar da configuração do host, passe o argumento `--disable-host-check` para o comando `ng serve` para desabilitar essa checagem.

### Erros de código de local

Antes de usar códigos de local diferentes de en-US, precisamos configurar a aplicação, seja configurando o código de local padrão, ou seja importando e registrando nos módulos da nossa aplicação. Abaixo apresento a versão de importação e registro em um módulo:

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
