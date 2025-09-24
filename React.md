# Guia Completo de React

> Um guia prático e abrangente para aprender React — conceitos, exemplos e boas práticas.

---

## Sumário

1. [Introdução ao React](#introdução-ao-react)
2. [Ambiente de desenvolvimento e ferramentas](#ambiente-de-desenvolvimento-e-ferramentas)
3. [JSX](#jsx)
4. [Componentes](#componentes)
5. [Props e State](#props-e-state)
6. [Hooks principais](#hooks-principais)
7. [Efeitos colaterais e ciclo de vida](#efeitos-colaterais-e-ciclo-de-vida)
8. [Context API](#context-api)
9. [Refs e manipulação direta do DOM](#refs-e-manipulação-direta-do-dom)
10. [Formulários e validação](#formulários-e-validação)
11. [Rotas com React Router](#rotas-com-react-router)
12. [Gerenciamento de estado avançado](#gerenciamento-de-estado-avançado)
13. [Otimização de performance](#otimização-de-performance)
14. [Testes](#testes)
15. [TypeScript com React](#typescript-com-react)
16. [Styling em React](#styling-em-react)
17. [Acessibilidade (a11y)](#acessibilidade-a11y)
18. [Build e deploy](#build-e-deploy)
19. [Padrões arquiteturais e boas práticas](#padrões-arquiteturais-e-boas-práticas)
20. [Recursos e próximos passos](#recursos-e-próximos-passos)

---

# Introdução ao React

React é uma biblioteca JavaScript para construir interfaces de usuário declarativas, mantida pelo Facebook (Meta) e uma ampla comunidade. Alguns pontos centrais:

* Componentes reutilizáveis.
* UI declarativa usando JSX.
* Estado local e global (Hooks, Context, Redux etc.).
* Foco em performance via reconciliação (virtual DOM).

Quando usar React: aplicações com interfaces interativas, SPAs, dashboards, aplicações que precisam de componentes reutilizáveis.

---

# Ambiente de desenvolvimento e ferramentas

### Pré-requisitos

* Node.js (versão LTS recomendada)
* npm ou yarn
* Editor: VS Code (recomendado)

### Criação de projeto

* `create-react-app` (boa para iniciar projetos rapidamente):

```bash
npx create-react-app minha-app
cd minha-app
npm start
```

* Alternativas: Vite (`npm create vite@latest`), Next.js (para SSR/SSG e recursos avançados).

### Ferramentas úteis

* ESLint + Prettier (formatação e linting)
* TypeScript
* React DevTools (extensão do navegador)
* Storybook (documentação de componentes)
* Testing Library + Jest

---

# JSX

JSX é uma sintaxe que mistura JavaScript com XML/HTML-like. Exemplo:

```jsx
function Ola({ nome }) {
  return <h1>Olá, {nome}!</h1>;
}
```

Regras importantes:

* Expreşões JavaScript dentro de `{}`.
* `className` em vez de `class`.
* `htmlFor` em vez de `for`.
* Tudo que estiver dentro do componente deve ser encapsulado em um elemento (ou usar fragmentos `<>...</>`).

---

# Componentes

### Componentes Funcionais (recomendados)

```jsx
function MeuComponente(props) {
  return <div>{props.children}</div>;
}
```

### Componentes de Classe (antigo)

```jsx
class MeuComponente extends React.Component {
  render() {
    return <div>{this.props.children}</div>;
  }
}
```

Hoje em dia, com Hooks, componentes funcionais cobrem quase todos os casos.

### Composição vs Herança

Preferir composição (children, props que recebem componentes) em vez de herança.

---

# Props e State

### Props

* Dados passados do pai para o filho.
* Imutáveis dentro do componente.

```jsx
<Botao label="Salvar" />
```

### State

* Estado local do componente (mutável via API do React).

```jsx
import { useState } from 'react';

function Contador() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(c => c + 1)}>+</button>
    </div>
  );
}
```

Dicas:

* Mantenha o mínimo de estado necessário.
* Normalize dados quando apropriado.

---

# Hooks principais

### `useState`

Gerencia estado local.

### `useEffect`

Efeitos colaterais (fetch de dados, subscriptions, timers). Recebe função de efeito e array de dependências.

```jsx
useEffect(() => {
  // efeito
  return () => { /* limpeza */ };
}, [dependencias]);
```

### `useRef`

Referências mutáveis que persistem entre renders (por exemplo, ref de DOM ou contador sem re-render).

### `useMemo` e `useCallback`

Memoização de valores e funções para evitar recomputações ou re-renderizações desnecessárias.

### `useContext`

Acessa o Contexto criado por `React.createContext()`.

### Hooks customizados

Crie hooks para encapsular lógica reutilizável:

```jsx
function useContador(inicial = 0) {
  const [c, setC] = useState(inicial);
  const increment = () => setC(x => x + 1);
  return { c, increment };
}
```

---

# Efeitos colaterais e ciclo de vida

* `useEffect` substitui `componentDidMount`, `componentDidUpdate` e `componentWillUnmount` quando usado com dependências apropriadas.
* Evite efeitos sem dependências (executam a cada render). Use arrays de dependências corretamente.
* Use `useEffect` para:

  * Buscar dados (fetch/axios).
  * Assinar/dessinscrever eventos.
  * Interagir com APIs do navegador.

---

# Context API

Context fornece um modo de passar dados profundamente sem props drilling.

```jsx
const TemaContext = React.createContext('claro');

function App() {
  return (
    <TemaContext.Provider value={tema}>
      <ComponenteA />
    </TemaContext.Provider>
  );
}
```

Considere usar Context para temas, idioma, usuário autenticado. Para estado complexo ou alto volume de atualizações, prefira soluções dedicadas (Redux,zustand).

---

# Refs e manipulação direta do DOM

* `useRef` para acessar elementos DOM.
* Evite manipular DOM diretamente; prefira modo declarativo.

```jsx
function InputFoco() {
  const inputRef = useRef();
  useEffect(() => { inputRef.current.focus(); }, []);
  return <input ref={inputRef} />;
}
```

---

# Formulários e validação

* Controle de formulários: componentes controlados (valor no state) vs não controlados (refs).
* Bibliotecas úteis: Formik, React Hook Form (altamente performático), Yup (validação).

Exemplo com React Hook Form:

```jsx
import { useForm } from 'react-hook-form';

function Form() {
  const { register, handleSubmit, formState } = useForm();
  const onSubmit = data => console.log(data);
  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('nome', { required: true })} />
      <button type="submit">Enviar</button>
    </form>
  );
}
```

---

# Rotas com React Router

Instalação: `npm install react-router-dom`.

```jsx
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav><Link to="/">Home</Link></nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/sobre" element={<Sobre />} />
      </Routes>
    </BrowserRouter>
  );
}
```

Dicas:

* Use lazy + Suspense para carregamento sob demanda de rotas.
* Proteja rotas com componentes de autenticação.

---

# Gerenciamento de estado avançado

### Context + useReducer

Boa para casos médios sem necessidade de biblioteca externa.

### Redux

* Fluxo previsível, ótimo para grandes aplicações.
* Hoje com Redux Toolkit: API simplificada e melhores práticas embutidas.

Instalação e padrão básico:

```bash
npm install @reduxjs/toolkit react-redux
```

### Outras alternativas

* Zustand (simples e performático)
* Recoil
* MobX

Escolha com base na complexidade e no time.

---

# Otimização de performance

* Use `React.memo` para componentes puros.
* `useMemo` / `useCallback` para evitar recomputações caras.
* Evite passar objetos/arrays literais como props sem memoização.
* Virtualize listas grandes (react-window / react-virtualized).
* Code-splitting com `React.lazy` e `Suspense`.

Mensure antes de otimizar (React DevTools, Lighthouse).

---

# Testes

* Unitários: Jest + Testing Library (React Testing Library) — focar no comportamento do usuário.
* Testes de integração: testar fluxos entre componentes.
* Testes E2E: Cypress, Playwright.

Exemplo simples com Testing Library:

```jsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import App from './App';

test('incrementa contador', async () => {
  render(<App />);
  const button = screen.getByRole('button', { name: /incrementar/i });
  await userEvent.click(button);
  expect(screen.getByText('1')).toBeInTheDocument();
});
```

---

# TypeScript com React

* Tipos para props, state, refs e hooks.
* Crie projetos com `create-react-app my-app --template typescript` ou Vite.

Exemplo de componente com props tipadas:

```tsx
type Props = { nome: string };
function Ola({ nome }: Props) {
  return <div>Olá {nome}</div>;
}
```

Dicas:

* Tipar corretamente `useRef` e `useState` quando necessário.
* Use `React.FC` com cuidado (opcional).

---

# Styling em React

Opções comuns:

* CSS tradicional / SASS
* CSS Modules
* styled-components / emotion
* Tailwind CSS (utility-first)
* Inline styles

Escolha baseada em equipe e projeto. Para componentes isolados, styled-components ou CSS Modules são convenientes.

---

# Acessibilidade (a11y)

* Use elementos semânticos (`button`, `nav`, `main`, `header`).
* Labels para inputs (`<label htmlFor>`).
* Atributos ARIA quando necessário.
* Verifique contraste, foco por teclado e leitores de tela.
* Ferramentas: axe, Lighthouse.

---

# Build e deploy

* `npm run build` gera build otimizado.
* Hospedagem: Vercel, Netlify, AWS S3 + CloudFront, Surge, Firebase Hosting.
* Para SSR/SSG, considere Next.js (Vercel facilita o deploy).

---

# Padrões arquiteturais e boas práticas

* Componentes pequenos e coesos.
* Separar UI (apresentação) e lógica (contêineres ou hooks).
* Reutilizar hooks customizados.
* Estratégias de pasta: `components/`, `hooks/`, `pages/`, `services/`, `store/`.
* Documente componentes com Storybook.

---

# Exemplos e receitas rápidas

## Fetch com efeito e loading

```jsx
function Usuarios() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('/api/users')
      .then(r => r.json())
      .then(data => setUsers(data))
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <div>Carregando...</div>;
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

## Debounce em input com hook customizado

```jsx
function useDebounce(value, delay = 300) {
  const [debounced, setDebounced] = useState(value);
  useEffect(() => {
    const id = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(id);
  }, [value, delay]);
  return debounced;
}
```

---

# Recursos e próximos passos

* Documentação oficial: [https://reactjs.org](https://reactjs.org)
* React Router: [https://reactrouter.com](https://reactrouter.com)
* Redux Toolkit: [https://redux-toolkit.js.org](https://redux-toolkit.js.org)
* Vite: [https://vitejs.dev](https://vitejs.dev)
* Next.js: [https://nextjs.org](https://nextjs.org)
* Testing Library: [https://testing-library.com](https://testing-library.com)

---

# Cheatsheet rápido (comandos)

* Criar app CRA: `npx create-react-app minha-app`
* Rodar: `npm start`
* Build: `npm run build`
* Testar: `npm test`

---

