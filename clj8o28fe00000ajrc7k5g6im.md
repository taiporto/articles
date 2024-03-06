---
title: "React - Renderize componentes condicionalmente com a técnica da Referência Capitalizada"
datePublished: Fri Jun 23 2023 14:27:34 GMT+0000 (Coordinated Universal Time)
cuid: clj8o28fe00000ajrc7k5g6im
slug: react-renderize-componentes-condicionalmente-com-a-tecnica-da-referencia-capitalizada
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/L57L1mlKoM8/upload/dddee1ce3f3b4b43f9faa1f299470a0c.jpeg
tags: reactjs, typescript, frontend-development, capitalized-reference

---

Conforme os componentes em React vão ficando mais complexos, pode ser se que você se depare com um caso parecido com o que enfrentei esses dias no trabalho. Para exemplificar, vou substituir os dados e alterar um pouco a estrutura.

## O problema

Eu precisava renderizar, em uma tabela dinamicamente construída, um componente específico relativo a um valor que eu recebia como *prop* da tabela.

Para este exemplo, o objetivo é renderizar uma tabela de bebidas em um componente chamado `Tabela`, que:

* Recebe como *prop* uma *string* `tipoDeBebida`;
    
* Puxa os dados de todas as bebidas desse tipo e guarda em uma *array*;
    
* Itera a *array* de bebidas e renderiza uma linha de tabela para cada item, com nome e exemplo da bebida;
    

O componente que me gerou dúvidas era o componente do exemplo da bebida. Eu tinha um componente para o tipo de bebida 1 e um componente para o tipo de bebida 2, em que ambos recebiam uma propriedade `nomeDaBebida`, mas eram totalmente diferentes entre si, de forma que não podiam ser unidos em um único componente comum que contivesse a lógica de alternância dentro dele.

Também não queria passar o *prop* `tipoDeBebida` para baixo mais uma vez, e precisaria fazer isso com a solução de componente único.

Eu precisava renderizar um ou outro componente dependendo do valor `tipoDeBebida` e garantir também que essa solução fosse escalável, porque eu sabia que entrariam mais tipos de bebida no futuro.

## 1ª abordagem - Pouco eficiente, recalculando a cada item iterado e não levando em conta a escalabilidade

Minha primeira abordagem, só para que a tabela funcionasse, foi a seguinte:

```typescript
import { ExemploCha } from '../ExemploCha';
import { ExemploCafe } from '../ExemploCafe';

const Tabela = (tipoDeBebida:string) => {
    const bebidasEspecificas = useBebidas(tipoDeBebida);
    
    return (
        <table>
            <tr>
                <th>Nome da bebida</th>
                <th>Exemplo</th>
            </tr>
            {bebidasEspecificas.map((bebida: Bebida) => {
                return (
                    <tr>
                        <td>{bebida.nome}</td>    
                         <td>
                           {
                            tipoDeBebida === 'chá' ?
                            <ExemploCha nome={nome}/>
                            :
                            <ExemploCafe nome={nome}/>
                            }
                        </td>    
                    </tr>
                );
            }
        </table>
    );
};
```

Simples e funcional, mas estava verificando o valor da variável `tipoDeBebida` em **todas** as iterações. Seria muito mais simples checar uma vez só e dizer qual componente seria renderizado para todos os elementos da *array*, certo?

Sem falar que se adicionarmos mais uma bebida aos tipos possíveis, já aumentamos a complexidade.

## 2ª abordagem - Extraindo a lógica para fora do loop

Se o meu componente de exemplo não renderizasse nada específico do item sendo iterado pelo `map` (no caso, o nome da bebida) eu poderia fazer algo como:

```typescript
import { ExemploCha } from '../ExemploCha';
import { ExemploCafe } from '../ExemploCafe';

const Tabela = (tipoDeBebida: string) => {
    const bebidasEspecificas = useBebidas(tipoDeBebida);

    const componenteParaRenderizar = tipoDeBebida === 'chá' ?
                                                <ExemploCha />
                                                :
                                                <ExemploCafe />;
    return (
        <table>
           {/* Omitido por clareza */}
            {bebidasEspecificas.map((bebida: Bebida) => {
                return (
                    <tr>
                        <td>{bebida.nome}</td>    
                         <td>
                           {componenteParaRenderizar}
                        </td>    
                    </tr>
                );
            }
        </table>
    );
};
```

Só que eu precisava passar para os exemplos o nome de cada item para que ele fosse usado na construção do exemplo.

Como eu precisava passar **parâmetros**, decidi construir uma função que recebesse o tipo da bebida e o nome e retornasse o componente certo com o nome passado como propriedade:

```typescript
const selecionaExemplo = (tipoDeBebida: string, nomeDaBebida: string) => {
    return tipoDeBebida === 'cha' ?
                            <ExemploCha nome={nomeDaBebida} />
                            :
                            <ExemploCafe nome={nomeDaBebida} />                            
}
```

Lindo. Funcionou. Mas eu sabia que **mais tipos de bebida** iam existir nessa aplicação no futuro. E eu sabia que essa solução era muito frágil para sustentar as novas bebidas, já que ia me exigir criar ternários dentro de ternários e aumentar a complexidade do meu código.

## 3ª abordagem - O problema do `switch-case`

Então eu mudei a implementação da função para usar a primeira estrutura de decisão que me veio a cabeça e que permitia lidar com várias opções de retorno para um valor de parâmetro: o `switch-case`.

```typescript
const selecionaExemplo = (tipoDeBebida: string, nomeDaBebida: string) => {
    switch(tipoDeBebida) {
        case 'cha':
            return <ExemploCha nome={nomeDaBebida} />;
        case 'cafe':
            return <ExemploCafe nome={nomeDaBebida} />;
        default:
            return null;
        // Com essa estrutura, se eu precisasse de um novo tipo de bebida eu só precisaria fazer:
        /**
        case 'suco':
            return <ExemploSuco nome={nomeDaBebida} />
        **/
    }                        
}
```

Eu não gostei muito de usar `switch-case` e queria uma solução menos verbosa e mais elegante, mas não consegui pensar em nada. Então, foi assim que enviei o código para review.

## 4ª abordagem - Referência Capitalizada, removendo o `switch-case` e tornando o código mais simples e extensível

Foi aí que uma pessoa do meu time avaliou meu código e me sugeriu usar a técnica de Referência Capitalizada, me enviando [este artigo](https://j5bot.medium.com/react-dynamically-rendering-different-components-without-switch-the-capitalized-reference-e668d89e460b).

### Definindo Referência Capitalizada

Referência Capitalizada ("*Capitalized Reference*" em inglês) é o nome que Jonathan Cook dá para uma técnica descrita na [antiga documentação do React sobre JSX](https://legacy.reactjs.org/docs/jsx-in-depth.html#choosing-the-type-at-runtime).

É uma alternativa menos verbosa que o `switch-case` e mais "nativa" do React e do JSX para renderizar componentes condicionalmente. Na documentação, o use case descrito é exatamente o que precisamos resolver no nosso problema: renderizar componentes diferentes baseando-se no valor de uma *prop* recebida pelo componente pai.

### Como utilizar a técnica

Jonathan Cook descreve quatro passos para usar a Referência Capitalizada:

1. Importar os componentes que precisaremos usar
    
2. Adicionar as referências a esses componentes a um objeto
    
3. Criar uma referência para o componente dinâmico que queremos renderizar, da seguinte forma:
    
    1. Crie uma nova variável, a referência, com a primeira letra maiúscula (capitalizada)
        
    2. Use o tipo do componente como a chave para puxar o valor dele do objeto criado no passo 2
        
    3. Designe o valor do objeto, que é uma referência direta ao componente, à referência capitalizada criada no passo a)
        
4. Renderize a referência capitalizada como um componente comum dentro do retorno do seu componente
    

Para implementar essa solução no nosso caso, faríamos:

```typescript
// 1) Importar os componentes
import { ExemploCha } from '../ExemploCha';
import { ExemploCafe } from '../ExemploCafe';

// 2) Criar um objeto com as referências aos componentes
// (Fora do componente principal para que não seja recriado em toda re-renderização)
const exemplosDeBebidas = {
    "chá": ExemploCha,
    "café": ExemploCafe,
};

const Tabela = (tipoDeBebida: string) => {
    const bebidasEspecificas = useBebidas(tipoDeBebida);

    // 3) Criar uma nova variável para guardar a referência ao componente
    const Exemplo = exemplosDeBebidas[tipoDeBebida];   

    return (
        <table>
           {/* Omitido por clareza */}
            {bebidasEspecificas.map((bebida: Bebida) => {
                return (
                    <tr>
                        <td>{bebida.nome}</td>    
                         <td>
                            {/* 4) Renderizar a referência capitalizada como um componente comum
                                dentro do retorno do seu componente */}
                           <Exemplo nome={nomeDaBebida} />
                        </td>    
                    </tr>
                );
            }
        </table>
    );
};
```

Agora temos uma solução mais limpa e simples que não exige a repetição de detalhes como a passagem da *prop* `nome`, já que ela será sempre a mesma, e reduz a quantidade de código a ser adicionada a cada novo tipo de bebida incluído.

Para o nosso caso, essa solução é suficiente. Mas caso você precise passar mais de uma *prop* para os componentes, ou implementar a técnica de forma mais complexa, cheque [o artigo original de Jonathan Cook](https://hashnode.com/draft/6463e42f0256de000f63d889) para mais informações.

E aí? Gostou da técnica? Quer conversar mais? Me dá um oi lá pelo [Twitter](https://twitter.com/moonkoala_) ou pelo [LinkedIn](https://www.linkedin.com/in/taiporto/) :)