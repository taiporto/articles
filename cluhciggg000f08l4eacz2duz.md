---
title: "Qual a diferença entre "needs" e "dependencies" no Gitlab CI? - Criando pipelines no Gitlab"
seoTitle: "Gitlab CI - "needs" versus "dependencies""
seoDescription: "Entenda a diferença entre as palavras-chave "needs" e "dependencies" e descubra quando usar cada uma delas com exemplos práticos."
datePublished: Mon Apr 01 2024 19:30:57 GMT+0000 (Coordinated Universal Time)
cuid: cluhciggg000f08l4eacz2duz
slug: qual-a-diferenca-entre-needs-e-dependencies-no-gitlab-ci-criando-pipelines-no-gitlab
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/BW0vK-FA3eg/upload/42902b0e1bd6fc7042ed43a7b6cfadf5.jpeg
tags: gitlab, yaml, gitlab-ci, pipeline, ci-cd

---

Tenho aprendido a ler e escrever arquivos de configuração de pipeline com um membro mais sênior do meu time, e na última sessão conversamos sobre dependências entre `jobs`, artefatos e compartilhamento de arquivos.

Tentando montar um exemplo funcional, ficamos na dúvida de como especificar que um `job` dependia do outro, visto que quando os dois são criados no mesmo estágio, se a dependência não for especificada eles rodam em paralelo.

## A situação

Se temos uma base de código em TypeScript, precisamos (pensando no mais básico) de pelo menos duas etapas para levar ela para produção:

1. Converter TypeScript para JavaScript
    
2. Fazer deploy do JavaScript para o ambiente de produção
    

Ambos os passos poderiam ser considerados dentro da mesma etapa de `build`. Mas se só designarmos eles de forma pura pelo `gitlab-ci.yml`, eles vão rodar paralelamente, e não é isso que queremos. Afinal, o deploy do JavaScript não pode acontecer sem que antes o código seja transpilado.

## A solução

Para isso, precisamos designar que o `job` 2 depende do `job` 1, mesmo que rodem durante o mesmo estágio. Ao pesquisar na documentação do Gitlab, encontramos duas palavras-chave usadas dentro de `jobs` que podem ser a nossa solução: `needs` e `dependencies`. Mas qual a diferença entre elas?

### `needs`

É a mais básica e, provavelmente, a solução para a maioria dos problemas semelhantes. `needs` é usada para **especificar a ordem em que os jobs vão rodar**. Ela não tem a ver com artefatos nem nada do tipo. Só diz que um job roda depois que o outro (ou outros) estiver completo.

Pode ser usada assim:

```yaml
stages:
  - build

generate_ts:
  stage: build
  script:
    - tsc
  # rest of the job

deploy:
  stage: build
  needs: ["generate_ts"]
  script:
    - yarn deploy
```

Dessa forma, o `job` `deploy` só vai rodar depois que o `job` `generate_ts` for concluído.

### `dependencies`

#### Artefatos

Os `jobs` não rodam num vácuo. O `generate_ts`, por exemplo, tem como principal função **gerar código transpilado**. Isso significa que a saída dele precisa ser passada como entrada para o próximo `job`. No gitlab CI, fazemos isso por meio da geração de **artefatos**, ou `artifacts`.

Para gerar artefatos em um `job`, dizemos a ele onde na base de código procurar pelos arquivos que o Gitlab precisa guardar como artefato para serem usados mais na frente em outros passos da *pipeline*:

```yaml
generate_ts:
  stage: build
  script:
    - tsc
  artifacts:
    when: on_success
    paths:
      - build
  # rest of the job
```

O trecho de configuração acima diz:

1. Configure artefatos para esse job:
    
    1. Quando o comando especificado (`tsc`) for concluído com sucesso
        
    2. E procure pelos arquivos que serão artefatos dentro da pasta `build` (Isso presume que o código transpilado será colocado na pasta `build`)
        

#### O comando `dependencies`

E o que `dependencies` tem a ver com isso? Bom, muitos jobs no Gitlab podem gerar artefatos. **Por padrão, todos os artefatos de todos os jobs que vieram antes são baixados pelo job atual**. Mas nós podemos manipular isso para melhorar a visibilidade e a performance das *pipelines*. E é aí que entra o `dependencies`.

Com a palavra-chave `dependencies` você consegue especificar de quais `jobs` em específico aquele `job` depende. Assim, você não precisa baixar **todos** os artefatos de **todos** os jobs quando, às vezes, você só precisa dos artefatos de um `job` específico. Isso significa que para o nosso caso também podemos usar `dependencies`:

```yaml
generate_ts:
  stage: build
  script:
    - tsc
  artifacts:
    when: on_success
    paths:
      - build
  # rest of the job

deploy:
  needs: ["generate_ts"]
  dependencies: generate_ts
  stage: build
  script:
    - yarn deploy
  # rest of the job
```

Assim, especificamos que `deploy` só pode rodar depois que `generate_ts` acabar e que ele só precisa baixar artefatos do `generate_ts` para fazer o que precisa fazer.

Faz sentido?

## Fontes

* [https://docs.gitlab.com/ee/ci/yaml/#needs](https://docs.gitlab.com/ee/ci/yaml/#needs)
    
* [https://docs.gitlab.com/ee/ci/yaml/#dependencies](https://docs.gitlab.com/ee/ci/yaml/#dependencies)