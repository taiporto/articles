---
title: "O que são os tipos customizados do TypeScript"
datePublished: Tue Feb 14 2023 20:41:55 GMT+0000 (Coordinated Universal Time)
cuid: cle4pmr7o000109ju9xizc3d4
slug: o-que-sao-os-tipos-customizados-do-typescript
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ZTqaeeoBBl8/upload/eacb395ff8e4097a2e8583a85fa729da.jpeg
tags: frontend, typescript, frontend-development

---

TypeScript é um *superset* de JavaScript que permite criar e dar tipos às variáveis e funções da linguagem, melhorando a experiência do desenvolvedor e reduzindo a chance de erros e bugs nos softwares em produção.

Os tipos de TypeScript trabalham em cima dos tipos básicos do JavaScript, mas podem ser customizados de diversas formas.

## Tipos customizados

Em desenvolvimento web é muito comum trabalharmos com objetos. Eles são, na maior parte das vezes, a estrutura de dados que comporta da melhor forma as informações sobre um determinado elemento da aplicação, e são muito flexíveis e extensíveis.

Embora essa flexibilidade seja boa, em alguns casos precisamos determinar muito bem quais atributos o objeto deve ter para evitar bugs e confusão dos próprios desenvolvedores.

### Uma loja de camisetas

Por exemplo, imagine que você tem uma loja de camisetas em que cada camiseta tem sua própria página de produto. Essa página de produto puxa os dados da camiseta de uma API que te retorna um JSON com essas informações:

```json
"camiseta": {
    "nome": "Camiseta do Super-homem",
    "preco": 75,
    "cores": ["preta", "azul", "branca", "mescla"],
    "tamanhos_disponiveis": ["P", "M", "G"]
}
```

Esse JSON é válido e perfeitamente manipulável. Com ele você consegue expor os dados da camiseta para o seu usuário normalmente. Mas o que acontece se você tentar expor um dado que não está lá?

Em JavaScript, tentar acessar, por exemplo, `camiseta.imagem` não te retornaria um erro imediato na IDE, mas geraria um problema para o usuário. Isso parece fácil de resolver, porque nesse exemplo você fez todo o código, consegue ver o retorno da API e o retorno é pequeno, mas e se for uma nova pessoa mexendo no código, ou um objeto enorme com várias propriedades?

Além disso e essa nova pessoa, sem saber que `imagem` realmente não é um atributo, achar estranho que não existe `camiseta.imagem` e adicionar uma por conta própria, aumentando o objeto? Isso não deveria acontecer, certo?

Ainda, e se você não souber que `preco`, nessa API, retorna um número e não uma string formatada no padrão monetário brasileiro, e tentar manipular a variável de alguma forma não compatível com o tipo `number`?

### A palavra-chave `type`

É para isso (e para muitas outras coisas) que no TypeScript existe a palavra chave `type`. Ao invés de te restringir aos tipos básicos (`string`, `number`, `boolean`, `Array`, `object`, etc), o TypeScript permite que você "apelide" eles com outro nome e também que crie novos tipos baseados em objetos.

No caso da nossa loja de camisetas, criar um novo tipo ajudaria todos a saberem exatamente qual é a forma da estrutura de dados com a qual estão lidando:

```typescript
type Camiseta = {
    nome: string,
    preco: number,
    cores: Array<string>,
    tamanhos_disponiveis: Array<string>
}
```

Note que além de especificar que `Camiseta` possui as propriedades `nome`, `preco`, `cores` e `tamanhos_disponiveis`, também podemos especificar o tipo de cada uma dessas propriedades. Assim, você enquanto desenvolvedor consegue prever e manter controle sobre o que deve vir da API em cada um dos campos.

> 💡 Um tipo "Array" em TypeScript pode ser declarado de duas formas:
> 
> 1. A primeira é usando o [tipo genérico](https://www.typescriptlang.org/docs/handbook/2/generics.html) `Array<>` e especificando entre os `<>` o tipo dos elementos desse Array, como em `Array<string>`
>     
> 2. A segunda, mais simples de escrever porém menos explícita, é dizer **primeiro o tipo dos elementos** e finalizar com dois colchetes (`[]`), como em `string[]`
>     
> 
> Uma array pode ser composta de elementos de qualquer tipo, inclusive de tipos customizados. (`Array<Camiseta>` seria uma sintaxe válida, desde que `Camiseta` esteja definido)

#### Formas mais simples de usar o `type`

A palavra-chave `type` é muito utilizada para definir a estrutura de objetos, mas como comentei antes também pode ser usada para "apelidar" tipos. Por exemplo, se você quisesse, poderia dizer que:

```typescript
type Camiseta = {
    nome: string,
    preco: ValorMonetario,
    cores: Cores,
    tamanhos_disponiveis: Array<string>
}
```

E isso funcionaria normalmente desde que você definisse os tipo `Cores` e `ValorMonetario` como:

```typescript
type Cores = Array<string>;
type ValorMonetario = number;
```

Essa estrutura é bastante útil quando você compartilha tipos entre diversos pontos de uma aplicação. Algumas vezes você pode já ter um tipo definido que se encaixa dentro de um novo tipo. Para manter a coerência, você pode usar esse formato e referenciar o tipo pelo nome dele ao invés de copiar a sua definição.

No caso do preço, definir as coisas dessa forma pode ser útil para deixar claro que qualquer valor monetário vindo da API será sempre um número, e nunca uma string.

### Acesso com colchetes

Uma coisa muito legal dos tipos customizados de objetos é que, como são objetos, seus itens podem ser acessados com a sintaxe de colchetes e representam também um tipo específico.

Por exemplo, caso você precise usar o dado dos tamanhos disponíveis de camiseta em alguma função da sua aplicação, mas você não saiba exatamente qual o tipo desse dado, ou, já que é uma Array, qual o tipo dos itens dela, você pode criar a sua função da seguinte forma:

```typescript
function AtueSobreTamanhos(tamanhos:Camiseta['tamanhos_disponiveis']){
    //... faça algo
}
```

Essa sintaxe com os `:` após o nome do argumento da função significa que estamos determinando um tipo para aquele argumento. Como não sabemos exatamente o tipo dos itens da Array `tamanhos`, não poderíamos fazer o que foi feito a seguir tendo 100% de certeza de que iria funcionar:

```typescript
function AtueSobreTamanhos(tamanhos:Array<string>)
```

Você pode também ficar tentado a usar o any, dessa forma:

```typescript
function AtueSobreTamanhos(tamanhos:Array<any>)
```

> 💡 Fuja dos `any` o máximo possível. Usar `any` à toa é jogar fora tudo que o TypeScript traz em relação a segurança e rapidez na experiência de desenvolvimento porque volta a tornar o código imprevisível como o JavaScript

Para evitar fazer essas duas construções usamos a estrutura `Camiseta["tamanhos_disponiveis"]`. Com isso estamos dizendo que o que quer que essa função receba tem que ser do tipo determinado para `tamanhos_disponiveis` dentro do tipo customizado `Camiseta`.

Se em algum momento for necessário mudar a Array de tamanhos disponíveis para que seja, ao invés de uma Array de strings, uma Array de objetos, isso só vai precisar ser mudado diretamente no objeto `Camiseta` original, e não em todos os lugares que recebem esse tipo de dado como parâmetro.

```typescript
// 🛑 Não é o ideal. Precisamos mudar a definição em mais de um lugar, aumentando a carga cognitiva e dificultando a manutenção do código.

type Tamanho = { 
    nome: string,
    letra: string
}

type Camiseta = {
    nome: string,
    preco: ValorMonetario,
    cores: Cores,
    tamanhos_disponiveis: Array<Tamanho> // Mudamos aqui
}

function AtueSobreTamanhos(tamanhos:Array<Tamanho>){ // Também mudamos aqui
}
```

```typescript
// ✅ Melhor. Só mudamos em um lugar a definição, facilitando a manutenção do código no futuro

type Tamanho = { 
    nome: string,
    letra: string
}

type Camiseta = {
    nome: string,
    preco: ValorMonetario,
    cores: Cores,
    tamanhos_disponiveis: Array<Tamanho> // Só mudamos aqui
}

function AtueSobreTamanhos(tamanhos:Camiseta['tamanhos_disponiveis']){
}
```

E aí? Entendeu como funcionam os tipos customizados em TypeScript? Se tiver ficado alguma dúvida, não hesite em entrar em contato comigo lá no [Twitter](http://twitter.com/moonkoala_) ou no [LinkedIn](http://linkedin.com/in/taiporto). Até!