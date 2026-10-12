---
name: typescript-discriminated-unions
description: Modelagem explícita de estados com discriminated unions em TypeScript, especialmente para componentes React e operações assíncronas. Use esta skill ao criar, refatorar ou revisar tipos de estado com múltiplos booleanos ou propriedades opcionais — por exemplo, "modele o estado da API", "tipa loading, erro e sucesso", "refatora esse estado", "evita estados impossíveis", "cria variantes de modal", ou ao trabalhar com estados de autenticação, pagamento, formulário, notificações e outras operações assíncronas.
---

# TypeScript discriminated unions

Modele cada estado válido como uma variante explícita de uma união discriminada. Descreva os estados que realmente podem existir, em vez de reunir em um único objeto tudo o que talvez exista.

## 1. Evite combinações de propriedades que criam estados ambíguos

Não represente variantes mutuamente exclusivas com booleanos e propriedades opcionais independentes:

```ts
type State = {
  loading: boolean;
  error?: string;
  data?: User[];
};
```

Esse tipo aceita combinações cujo significado não está claro:

```ts
const state: State = {
  loading: false,
  error: "Something went wrong",
  data: users,
};
```

Nesse caso, o tipo não informa se a requisição falhou ou teve sucesso. Ele permite que `error` e `data` existam ao mesmo tempo.

## 2. Represente cada estado com um discriminante literal

Use uma propriedade compartilhada, como `status`, com um valor literal diferente em cada variante. Inclua em cada variante somente os dados válidos para aquele estado:

```ts
type State =
  | { status: "loading" }
  | { status: "error"; message: string }
  | { status: "success"; data: User[] };
```

Essa modelagem impede combinações inválidas: `message` pertence ao estado de erro, enquanto `data` pertence ao estado de sucesso.

## 3. Use narrowing para acessar dados específicos com segurança

Verifique o discriminante antes de usar as propriedades de uma variante. Em React, um `switch` torna os caminhos de renderização explícitos:

```tsx
function UserList({ state }: { state: State }) {
  switch (state.status) {
    case "loading":
      return <Spinner />;
    case "error":
      return <ErrorMessage message={state.message} />;
    case "success":
      return <Users users={state.data} />;
  }
}
```

Quando `state.status === "success"`, o TypeScript restringe o tipo para a variante que contém `data`. Quando `state.status === "error"`, restringe para a variante que contém `message`. Esse processo é chamado de **narrowing**.

## Onde aplicar

Use discriminated unions sempre que um valor puder assumir um conjunto conhecido de estados ou variantes, especialmente em:

- estados de API;
- estados de autenticação;
- estados de pagamento;
- estados de formulário;
- variantes de modal;
- notificações;
- operações assíncronas.

## Aplicando o padrão

Ao criar ou revisar um estado:

1. Liste os estados que podem realmente existir.
2. Escolha uma propriedade discriminante, como `status` ou `type`.
3. Crie uma variante para cada estado e mantenha nela apenas os dados válidos naquele caso.
4. Substitua combinações de booleanos e propriedades opcionais que representem variantes mutuamente exclusivas.
5. Faça narrowing pelo discriminante antes de acessar dados específicos da variante.
6. Em componentes React, mantenha a renderização alinhada às variantes, usando `switch` ou verificações equivalentes.

Prefira tipos que tornem estados inválidos impossíveis de representar e que permitam ao TypeScript verificar cada caminho de uso.
