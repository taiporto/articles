---
title: "Como remover o redirecionamento automático do WordPress após mudar a slug do post"
seoTitle: "Como remover o redirecionamento automático de slugs no WordPress"
seoDescription: "Descubra como desativar os redirecionamentos automáticos do WordPress e tenha mais controle sobre como os usuários irão visualizar o conteúdo do seu site."
datePublished: Mon Aug 15 2022 11:58:39 GMT+0000 (Coordinated Universal Time)
cuid: cl6upcxvx043ts0nv9vju5w7q
slug: como-remover-redirecionamento-automatico-wordpress
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/G9l9ejbuHlI/upload/v1656363210172/6dAtF-1go.jpeg
tags: wordpress

---

Você já reparou que o WordPress redireciona automaticamente as suas URLs depois que você muda a *slug* de um post?

Por exemplo, se eu estou escrevendo esse meu post e a *slug* dele é, vamos dizer, **como-impedir-redirecionamento-wordpress**, mas eu decido mudar para **como-impedir-redirecionamento-slug**, o WordPress garante que a conexão entre uma URL e a outra seja feita automaticamente.

Nesse caso, a URL que era, por exemplo, `https://moonk23.hashnode.dev/como-impedir-redirecionamento-wordpress` passa a ser `https://moonk23.hashnode.dev/como-remover-redirecionamento-automatico-wordpress`, mas, por causa do WordPress, para o usuário final essa mudança não faz diferença.

Isso porque ele guarda, por padrão, a *slug* antiga do seu post e cria uma regra de redirecionamento interna. Assim, quando você tenta acessar `https://moonk23.hashnode.dev/como-impedir-redirecionamento-wordpress` a página não mostra um erro 404, mas é direcionada para `https://moonk23.hashnode.dev/como-remover-redirecionamento-automatico-wordpress`.

## Redirecionamentos automáticos

Mas esse não é o único redirecionamento que o WordPress implementa por padrão. Alguns redirecionamentos básicos já são previamente definidos pela ferramenta, com o objetivo de melhorar a experiência dos usuários e reduzir o trabalho para o gerenciador do conteúdo.

A maioria é adicionada (e pode ser desativada) através do hook [template\_redirect](https://developer.wordpress.org/reference/hooks/template_redirect/):

### wp\_old\_slug\_redirect()

É a função responsável pelo comportamento de redirecionamento de *slugs* explicado até agora. Ela busca nos metadados do post (que podem ser encontrados na tabela wp\_postmeta) pela informação com a chave `_wp_old_slug`, que guarda a antiga *slug* do post. Feito isso, ela cria um redirecionamento da `_wp_old_slug` para a *slug* atual.

Ela é implementada como uma *action* no hook `template_redirect`, o que significa que também pode ser removida como se remove uma *action*. Você pode, por exemplo, adicionar esse código ao arquivo `functions.php` do seu tema:

```plaintext
remove_action( 'template_redirect', 'wp_old_slug_redirect' );
```

### redirect\_canonical()

Essa é a função responsável pela maioria dos redirecionamentos automáticos feitos pelo WordPress. [Seu principal objetivo](https://developer.wordpress.org/reference/functions/redirect_canonical/) é proteger o SEO do seu site prevenindo que URLs apenas ligeiramente diferentes (como meudominio.com/blog e meudominio.com/blog1) apontem para páginas diferentes que podem ter conteúdo duplicado.

Ela faz diversas verificações para redirecionar URLs escritas errado ou com o caminho errado para a equivalência mais próxima. Por exemplo: Se o meu site não tem uma página com a URL `/pt-br/blog`, mas sim uma com a URL `/blog`, o WordPress faz o trabalho de redirecionar o usuário que acessa `/pt-br/blog` para a URL "canônica".

Essa função pode ser encontrada no arquivo `canonical.php` que fica dentro de `wp-includes` e, assim como no redirecionamento anterior, para remover essa ação em todo o site é possível usar o `remove_action`.

Mas nesse caso **o ideal não é desativar toda a** `redirect_canonical`, porque ela cuida de redirecionamentos importantes que caso desativados podem afetar o SEO do seu site.

Por isso, para desativar esse comportamento do WordPress de tentar "adivinhar" a sua URL, você pode desativar uma função que é usada **dentro** de `redirect_canonical()`: a `redirect_guess_404_permalink()`

### redirect\_guess\_404\_permalink()

A `redirect_guess_404_permalink()` é chamada dentro da `redirect_canonical()`. Retirada diretamente da [documentação do WordPress](https://developer.wordpress.org/reference/functions/redirect_guess_404_permalink/), sua função é:

> Tentar adivinhar a URL correta de uma requisição 404 baseada em query vars.

Basicamente, se o usuário tenta acessar uma página que retorna um erro 404 - Not Found, o WordPress intercepta esse acesso e procura descobrir para onde a pessoa poderia estar querendo ir.

Essa função está associada ao hook `do_redirect_guess_404_permalink` e pode ser desativada ao retornar um valor falso de um filtro adicionado a [esse hook](https://developer.wordpress.org/reference/hooks/do_redirect_guess_404_permalink/):

```plaintext
add_filter( 'do_redirect_guess_404_permalink', '__return_false' );
```

Assim como no exemplo anterior, você pode adicionar esse trecho de código ao `functions.php` do seu tema, por exemplo, e o comportamento vai ser aplicado em todo o site.

## Por que escrever sobre isso?

A inspiração para esse post veio de um bug em que um dos desenvolvedores do meu *squad* trabalhou recentemente. Depois de algum tempo debugando, procurando redirecionamentos que não estavam configurados em nenhuma parte do WordPress e em nenhum plugin, descobrimos esse comportamento único e muitas vezes não óbvio do WordPress. Por isso, achei interessante compartilhar!

Sabe de mais algum redirecionamento do WordPress que vale a pena comentar? Viu algo no meu texto que pode melhorar? Me dá um feedback, vamos bater um papo! Você pode me encontrar no [LinkedIn](https://linkedin.com/in/taiporto).