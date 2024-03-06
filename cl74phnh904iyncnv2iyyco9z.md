---
title: "Entendendo Big O Notation - O que é e como usá-la para avaliar a complexidade de execução de algoritmos"
seoTitle: "Como analisar a complexidade de algoritmos com Big O Notation"
seoDescription: "Entenda com exemplos o que é Big O e como ela pode ser usada para analisar a performance de qualquer algoritmo."
datePublished: Mon Aug 22 2022 12:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cl74phnh904iyncnv2iyyco9z
slug: entendendo-big-o-notation
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/pFJtmoDMSAo/upload/v1656363614932/93UhaYTL_.jpeg
tags: teoria

---

Quando comecei a aprender programação existiam muitos assuntos dos quais eu simplesmente fugia. O que fez mais sentido na minha cabeça foi primeiro aprender como *fazer* as coisas e depois como otimizá-las.

Por isso, deixei de lado alguns assuntos como Big O Notation e Design Patterns. Até que conseguisse o básico, para mim não fazia sentido procurar lidar com a complexidade desses temas.

Agora, voltando a esss temas, Big O Notation foi um dos assuntos que mais me trouxe receio. Primeiro, porque envolve matemática (pequenos traumas 😅). Segundo, porque as primeiras introduções que tive ao tema foram, embora excelentes em detalhamento, muito complexas. Com isso, quase me desanimei de aprender. Mas recentemente comecei um curso na Udemy chamado [JavaScript Algorithms and Data Structures Masterclass](https://www.udemy.com/course/js-algorithms-and-data-structures-masterclass/) que tem uma introdução excelente ao assunto.

As explicações neste post serão todas baseadas nesse curso da Udemy, que recomendo bastante.

## Começando pelo começo - O que é Big O Notation?

Imagine que duas pessoas são desafiadas a resolver um mesmo problema:

> Crie um programa que some todos os números de 1 a N, incluindo N.

Uma das melhores partes da programação é que existem milhares de formas de resolver um mesmo problema. Essas duas pessoas podem ir por caminhos totalmente diferentes e chegar em soluções válidas.

Uma das pessoas pensou nessa solução:

```javascript
function somaAteN(numero){
	let soma = 0;
	for(let i = 1; i <= numero; i++){
		soma += i;
	}
	return soma;
}
```

E a outra pessoa pensou nessa solução aqui:

```javascript
function somaAteN(numero){
	return numero * (numero + 1) / 2;
}
```

No final, o que costuma importar é se a solução funciona. A princípio não existe como determinar se um código é "melhor" ou "pior" do que o outro se não determinamos antes qual o parâmetro de qualidade. O que determina um código "bom"?

Podemos pensar em alguns parâmetros. Um código pode ser bom se:

1. É legível;
    
2. **É rápido;**
    
3. **Consome menos recursos;**
    

Muitas vezes o que vai ser priorizado são os dois últimos itens, especialmente o número 2. Mas medir a rapidez de um código não é uma tarefa tão simples quanto pode parecer.

## Os problemas de medir rapidez

Resumindo em uma linha o maior problema ao tentar medir rapidez de execução: **máquinas não são todas iguais, e não rodam os mesmos processos sempre da mesma forma**.

Nós poderíamos criar um timer, ou marcar o tempo logo antes e logo após a execução de um algoritmo para comparar a diferença, mas esse tempo pode variar entre máquinas e até mesmo entre as vezes em que o algoritmo é executado em uma mesma máquina.

Além disso:

1. Usar um timer funciona apenas para algoritmos com entradas pequenas e que podem ser executados como teste.
    
2. Marcar o tempo dessa forma não é produtivo para analisar a performance de diferentes algoritmos **antes** de implementá-los, já que torna obrigatório executá-los.
    
3. Em um contexto de recursos e tempo limitados pode não ser muito vantajoso executar um algoritmo e gastar recursos só para conferir seu tempo de execução.
    
4. Com resultados variáveis não existe como estabelecer uma escala precisa ou qualquer padrão além de "esse algoritmo toma muito tempo" e "esse toma pouco tempo".
    

Por esses aspectos, medir a rapidez em segundos/minutos/horas torna-se um problema. Mas ainda assim, é preciso alguma forma de comparar algoritmos, certo?

O que a Big O Notation se propõe a fazer é apenas estabelecer uma **linguagem padronizada** para comparar não a rapidez, mas a **complexidade de execução** entre códigos diferentes que resolvem os mesmos problemas.

## Como calcular Big O Notation?

Se calcular a rapidez de um código com métodos como configurar um cronômetro leva a resultados pouco constantes, de que outra forma podemos fazer esse cálculo?

A resposta mais simples é: contando operações.

Um computador pode variar de outro no tempo que leva para executar um código. Mas, no final das contas, todos eles vão executar as mesmas operações determinadas pelo algoritmo. Algumas dessas operações serão, por definição, mais complexas, ou seja, exigirão mais tempo do que outras.

Trazendo de volta os exemplos anteriores, podemos testar esse raciocínio de contar operações.

```javascript
// Solução 1
function somaAteN(numero){
	let soma = 0;
	for(let i = 1; i <= numero; i++){
		soma += i;
	}
	return soma;
}
```

Se listarmos as operações realizadas pela função `somaAteN` na Solução 1, temos:

1. `let soma = 0;` -&gt; Criação de variável e designação de valor.
    
2. `let i = 1;` -&gt; Mais uma criação de variável e designação de valor. Primeira operação do loop de repetição.
    
3. `i <= numero;` -&gt; Comparação e segunda operação do loop.
    
4. `i++;` -&gt; Designação e adição. Terceira operação do loop.
    
5. `soma += i;` - Novamente, designação e adição. Quarta operação do loop.
    
6. `return soma;` - Nossa operação de saída.
    

As operações 4 e 5 são duplas – usam uma sintaxe reduzida para abreviar o que seriam duas operações. Portanto, podem contar como duas operações cada. Assim, temos, no total, mais ou menos 8 operações.

Mas poderíamos dizer com certeza que, toda vez que a Solução 1 rodar, o número **total** de operações vai ser 8?

![Um minion, personagem amarelo e baixinho do desenho animado Meu Malvado Favorito, falando ao telefone e dizendo "É... Não.”](https://media.giphy.com/media/gnE4FFhtFoLKM/giphy.gif align="center")

Pois é... Não.

Porque estamos esquecendo de uma coisa muito importante: O loop de repetição!

Com ele, as operações que são parte do loop e reavaliadas em cada iteração (ou seja, todas no loop sem contar a declaração inicial `let i = 1`) são contadas n vezes de acordo com o valor da entrada `n`.

E é aqui que a coisa começa a complicar para a Solução 1. Vamos supor que queremos somar todos os números de 1 até 10, incluindo o 10. Nesse caso, nossa **entrada**, nosso `n`, é 10, certo?

Então vamos ter como constantes as operações 1 e 2. Até aqui temos 2 operações. Beleza.

Depois, declaramos que o loop de repetição roda enquanto `i` for menor ou igual a `n`, ou seja, 10. Portanto, as outras 6 operações vão rodar **10 vezes**. Com isso, teríamos:

> `2 + (6 * 10)`

Em que `2` são as nossas duas operações constantes, `6` são as operações restantes que dependem do valor de `n` e `10` é o valor de `n`.

Ou

> O(2 + 6n)

Em que o `O()` é a sintaxe usada para mostrar que estamos falando de Big O. Nesse caso podemos "falar" a expressão acima da seguinte forma: "O Big O da solução 1 é igual a 2 mais 6n"

Mas não terminamos aqui. Antes de continuarmos, é importante destacar um fator dessa linguagem que estamos usando para falar de complexidade:

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Big O funciona em uma escala afastada, como se olhássemos todo o nosso algoritmo bem de cima. Nessa visão geral, certas operações e valores não importam tanto, visto que adicionariam um tempo de execução ínfimo ou pouco mensurável. Então, ao contabilizar as operações sempre buscamos <strong>simplificar ao máximo</strong>.</div>
</div>

Por isso, quando temos `O(2 + 6n)` esse valor pode ser simplificado para somente `O(n)`. Isso acontece porque se fôssemos comparar a diferença de performance da Solução 1 entre uma entrada `n` de valor 10 e uma de valor 10.000, a maior diferença no tempo de execução se daria pelo tamanho dessa entrada `n`, e não pelas constantes 2 e 6.

Assim, podemos dizer que o Big O da Solução 1 é `O(n)`, ou seja, seu tempo de execução tende a crescer linearmente conforme `n` cresce.

### Beleza... mas e a solução 2?

A solução 2! Vamos lá:

```javascript
// Solução 2
function somaAteN(numero){
	return numero * (numero + 1) / 2;
}
```

Já podemos ver de primeira que o número de operações é menor que o da solução anterior, mesmo se desconsiderarmos a quantidade extra de operações da Solução 1 por causa do loop. Temos:

1. numero \* ... - Multiplicação;
    
2. (numero + 1) - Operação de adição;
    
3. ... / 2 - Operação de divisão;
    

Como a Solução 2 não implementa nenhum loop, para qualquer valor de `n` o número de operações realizado vai ser o mesmo: 3.

Assim, temos que o Big O da Solução 2 poderia ser `O(3)`. Mas, novamente, Big O olha tudo bem de cima, em uma escala muito afastada. Além disso, a Big O Notation não se importa muito com constantes. O que importa é entender **como um algoritmo se comporta em relação à sua entrada, o** `n`.

Por isso, o nosso `O(3)` pode ser simplificado para `O(1)`, que é, finalmente, o Big O da nossa Solução 2.

Esse `O(1)` não significa nada mais do que o que já foi dito antes:

> Como a Solução 2 não implementa nenhum loop, para qualquer valor de `n` o número de operações realizado vai ser sempre o mesmo.

Por isso, dizemos que essa solução tem uma complexidade de execução constante.

> `O(1)` = Complexidade constante independentemente do valor de `n`

Essa é a forma padronizada de escrever e falar sobre o assunto. A ideia do Big O é fazer com que em uma conversa entre programadores todos cheguem ao mesmo raciocínio ao escutarem ou lerem `O(1)`, `O(n)`, e assim por diante.

## Como interpretar Big O Notation?

Então quando você encontrar coisas sobre Big O, vai ver notações como `O(n)`, `O(1)`, `O(n²)`, `O(n^c)`, etc. Mas... o que elas significam?

Olhando para os exemplos anteriores já sabemos que `O(1)` significa um algoritmo que mantém um tempo de execução constante independentemente do tamanho da entrada. Também sabemos que `O(n)` significa um algoritmo cujo tempo de execução cresce linearmente e proporcionalmente ao tamanho de sua entrada.

A Big O Notation busca calcular a complexidade de tempo que um algoritmo leva para ser executado e o quanto essa complexidade muda baseando-se na entrada oferecida a ele. Mas é importante saber que **a Big O vai considerar sempre o Pior Cenário Possível**. Não necessariamente um algoritmo vai sempre atingir esse teto de complexidade, seja ele O(n), O(n^2) ou qualquer outro. O importante é estabelecer o teto.

> 💡 Existem outros métodos de cálculo de complexidade que determinam limites diferentes do pior cenário possível:

* [Big Theta (Θ) Notation](https://www.khanacademy.org/computing/computer-science/algorithms/asymptotic-notation/a/big-big-theta-notation) - Considera **o pior cenário possível e o melhor cenário possível**, estabelecendo um tempo de execução que é a média entre esses dois limites.
    
* [Big Omega (Ω) Notation](https://www.khanacademy.org/computing/computer-science/algorithms/asymptotic-notation/a/big-big-omega-notation) - Considera **o melhor cenário possível**, estabelecendo um piso de complexidade. O tempo de execução dos algoritmos pode ser maior, mas nunca menor do que o Big Omega.
    

Existem alguns resultados básicos de Big O que vão aparecer frequentemente:

* `O(1)` - Significa que o algoritmo tem uma complexidade de execução constante. Seu tempo de execução vai seguir relativamente igual não importa o quanto a sua entrada cresça.
    
* `O(log n)` - A complexidade cresce um pouco conforme `n` cresce, mas de forma menos acelerada do que `O(n)`. **É considerado o "ponto ideal" da complexidade de algoritmos** e é a complexidade do algortimo chamado de ***Binary Search*** (Busca binária) ou "***divide-and-conquer***" (Divida e conquiste). [Este artigo](https://hackernoon.com/what-does-the-time-complexity-o-log-n-actually-mean-45f94bb5bfbf) no Hackernoon tem uma explicação bastante visual de como chegar em `O(log n)` analisando um algoritmo de busca binária.
    
* `O(n)` - A complexidade cresce proporcionalmente ao crescimento da entrada `n`, proporcionando um aumento linear ao tempo de execução.
    
* `O(n²)` - Chamamos essa complexidade de complexidade quadrática. Quando um algoritmo roda em `O(n²)`, seu tempo de execução cresce em uma curva ascendente conforme `n` aumenta. Um exemplo muito visto é aquele em que é necessário aninhar um loop de repetição dentro do outro:
    

```javascript
function manipulaArray2D(arr){
  for(let i = 0; i <= arr.length; i++) {
    for(let j = 0; j <= arr.length; j++){
      //... faça algo
    }
  }
}
```

* `O(n^c)` - E por que parar em uma complexidade quadrática? A complexidade O(n^c) é chamada de polinomial. Ela aumenta com base não apenas no valor da entrada `n`, mas também com base no número de repetições aninhadas baseadas em `n`. O número de repetições é o expoente `c`.
    

Além desses, existem outros especificados na imagem abaixo, como `O(2^n)`, `O(n!)` e `O(n log n)`.

![Gráfico demonstrando a complexidade dos Big-Os listados. O(1) e O(log n) estão em uma área boa, O(n) e O(n log n) estão em uma média e O(n²), O(2^n) e O(n!) estão em uma área ruim](https://cdn.hashnode.com/res/hashnode/image/upload/v1656370466510/TMc-cS7A9.png align="left")

## O poder das cheat sheets

É muita coisa pra lembrar, né? A parte boa é que desenvolvedores amam *cheat sheets*, aquela colinha que te lembra rapidamente do significado ou da aplicação de um termo específico. A imagem aqui em cima que ilustra o tempo de execução de cada um dos Big Os foi retirada da [Big-O Cheat Sheet](https://www.bigocheatsheet.com/), que também possui uma tabela de algoritmos comuns de manipulação de arrays e suas respectivas complexidades de execução.

Big O é um assunto super extenso, e exige muito mais pesquisa e aprofundamento do que esse artigo é capaz de proporcionar. Mas espero que ele sirva como uma boa introdução ao assunto e, como antes, sigo recomendando o curso [JavaScript Algorithms and Data Structures Masterclass](https://www.udemy.com/course/js-algorithms-and-data-structures-masterclass/), disponibilizado na Udemy. Até a próxima!