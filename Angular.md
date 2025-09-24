# 🚀 Guia Básico e Exemplo de Projeto Angular

Este documento explica como instalar, criar e rodar projetos em **Angular**, além de fornecer um exemplo completo com rotas, componentes e serviços.

---

## 📌 Pré-requisitos

Antes de começar, instale:

- [Node.js (LTS recomendado)](https://nodejs.org/)  
- [npm (já vem junto com Node.js)]  

Verifique se estão instalados:

```bash
node -v
npm -v
```

---

## ⚡ Instalar Angular CLI

O Angular utiliza a **CLI (Command Line Interface)** para facilitar o desenvolvimento.

Instale globalmente:

```bash
npm install -g @angular/cli
```

Verifique a instalação:

```bash
ng version
```

---

## 🛠 Criar um novo projeto Angular

No terminal/prompt, rode:

```bash
ng new minha-app --routing --style=css
cd minha-app
```

A CLI irá perguntar:
- Se deseja adicionar **Angular routing** (rotas).  
- Qual tipo de **estilo** usar (CSS, SCSS, etc).  

---

## ▶️ Rodar o servidor de desenvolvimento

Para iniciar o projeto:

```bash
ng serve
```

Ou especificar a porta:

```bash
ng serve --port 4200
```

Acesse no navegador:

```
http://localhost:4200/
```

---

## 📂 Estrutura básica do projeto

- `src/app/app.component.ts` → Componente principal.  
- `src/app/app.module.ts` → Módulo raiz da aplicação.  
- `src/app/app.component.html` → Template (HTML) principal.  
- `src/app/app.component.css` → Estilos do componente.  

---

## 📦 Criar componentes, serviços e módulos

Criar um **componente**:
```bash
ng generate component pages/home
```

Criar outro **componente**:
```bash
ng generate component pages/about
```

Criar um **serviço**:
```bash
ng generate service services/data
```

---

## 📘 Exemplo Completo de Código

Abaixo está um exemplo com **duas páginas (Home e About)** e um **serviço DataService** que consome uma API pública.

---

### `src/app/app.module.ts`
```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';
import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { HomeComponent } from './pages/home/home.component';
import { AboutComponent } from './pages/about/about.component';

@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
    AboutComponent
  ],
  imports: [
    BrowserModule,
    HttpClientModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

---

### `src/app/app-routing.module.ts`
```ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './pages/home/home.component';
import { AboutComponent } from './pages/about/about.component';

const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: '**', redirectTo: '' } // rota coringa
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

---

### `src/app/app.component.ts`
```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'minha-app';
}
```

#### `src/app/app.component.html`
```html
<nav>
  <a routerLink="/">Home</a> |
  <a routerLink="/about">About</a>
</nav>

<main>
  <router-outlet></router-outlet>
</main>
```

#### `src/app/app.component.css`
```css
nav {
  padding: 1rem;
  background: #f3f3f3;
}
nav a {
  margin-right: 0.5rem;
  text-decoration: none;
}
main {
  padding: 1rem;
}
```

---

### `src/app/pages/home/home.component.ts`
```ts
import { Component, OnInit } from '@angular/core';
import { DataService } from '../../services/data.service';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.css']
})
export class HomeComponent implements OnInit {
  items: any[] = [];
  loading = false;
  error: string | null = null;

  constructor(private dataService: DataService) {}

  ngOnInit(): void {
    this.fetchItems();
  }

  fetchItems() {
    this.loading = true;
    this.error = null;
    this.dataService.getItems().subscribe({
      next: data => {
        this.items = data;
        this.loading = false;
      },
      error: err => {
        this.error = 'Erro ao carregar itens';
        this.loading = false;
      }
    });
  }
}
```

#### `src/app/pages/home/home.component.html`
```html
<h1>Home</h1>

<button (click)="fetchItems()">Carregar itens</button>

<div *ngIf="loading">Carregando...</div>
<div *ngIf="error">{{ error }}</div>

<ul *ngIf="!loading && items.length">
  <li *ngFor="let it of items">{{ it.name || it }}</li>
</ul>

<div *ngIf="!loading && !items.length">Nenhum item</div>
```

#### `src/app/pages/home/home.component.css`
```css
h1 { margin-bottom: 0.5rem; }
button { margin-bottom: 1rem; }
```

---

### `src/app/pages/about/about.component.ts`
```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-about',
  templateUrl: './about.component.html',
  styleUrls: ['./about.component.css']
})
export class AboutComponent {}
```

#### `src/app/pages/about/about.component.html`
```html
<h1>About</h1>
<p>Exemplo de aplicação Angular — página About.</p>
```

#### `src/app/pages/about/about.component.css`
```css
p { line-height: 1.6; }
```

---

### `src/app/services/data.service.ts`
```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable, of } from 'rxjs';
import { catchError } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class DataService {
  private apiUrl = 'https://jsonplaceholder.typicode.com/users';

  constructor(private http: HttpClient) { }

  getItems(): Observable<any[]> {
    return this.http.get<any[]>(this.apiUrl).pipe(
      catchError(err => {
        console.error('Erro no getItems', err);
        return of([]);
      })
    );
  }
}
```

---

## 🛠 Build para produção

Gerar versão otimizada:

```bash
ng build --configuration production
```

Os arquivos finais ficarão em `dist/`.

---

## 📖 Documentação oficial

👉 [Angular Docs](https://angular.io/docs)  
👉 [Angular CLI Reference](https://angular.io/cli)

---

💡 Agora você já tem um **guia completo** e um **exemplo funcional** para começar com Angular!
