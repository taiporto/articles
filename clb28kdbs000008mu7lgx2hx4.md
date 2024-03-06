---
title: "Post coelho: Para que serve o atributo "lang" do HTML"
seoDescription: "Entenda em poucas palavras a importância do atributo "lang""
datePublished: Tue Nov 29 2022 13:09:31 GMT+0000 (Coordinated Universal Time)
cuid: clb28kdbs000008mu7lgx2hx4
slug: para-que-serve-o-atributo-lang-do-html
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/_t-l5FFH8VA/upload/v1669727289539/YIQJTfVF6.jpeg
tags: html, accessibility, html5, frontend-development

---

O atributo `lang` pode ser usado no elemento raiz de uma página, o `<html>`, ou em qualquer elemento de layout filho do body. Ele é utilizado para dizer ao leitor de tela em qual língua e pronúncia aquela página ou aquele trecho deve ser lido. Apesar disso, alguns leitores de tela aparentemente não têm suporte padrão a essa variação, como o Orca, no Linux.

Esse atributo tem suporte a códigos de língua com duas letras ("en", Inglês) e três letras ("grc", Grego Antigo), assim como variantes das línguas. O seu valor deve ser um código válido sob as diretrizes da [RFC 5646](https://datatracker.ietf.org/doc/html/rfc5646) (Em inglês), como "en-US" (Inglês americano), "en-GB" (Inglês britânico), "de" (Alemão), entre [outros](https://datatracker.ietf.org/doc/html/rfc5646#appendix-A). [Aqui](https://accessibility.psu.edu/foreignlanguages/langtaghtml/) (Em inglês) estão mais alguns exemplos de códigos de língua e suas variantes.

Sua aparência é a seguinte:

```html
<html lang="pt-BR">
<!-- O resto do código do seu site -->
</html>
```

[Esse ótimo post](https://www.matuzo.at/blog/lang-attribute/) do Manuel Matuzovic detalha um pouco mais sobre as particularidades do atributo `lang` e sobre como utilizá-lo da melhor forma.