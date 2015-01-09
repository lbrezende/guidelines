
# Princípios de escrita para Sass

Embora muitas pessoas pensem em Sass como apenas CSS, existem muitos desafios que precisam ser abordados se quisermos ser capazes de escrever o melhor código possível. Neste documento, vamos passar por alguns estilos de codificação que ajudarão você a compartilhar o código com os outros e evitar armadilhas comuns.

Esse guia de estilo de escrita não sugiu do nada, ele é tem como base o [Idiomatic SASS](https://github.com/anthonyshort/idiomatic-sass), que é uma adaptação do [Idiomatic CSS](https://github.com/necolas/idiomatic-css/tree/master/translations/pt-BR) originalmente escrito por [Nicolas Gallagher](https://github.com/necolas), mas sofreu algumas modificações para que apliquemos esse guia nos projetos do Ministério da Saúde.

## Índice de conteúdo

* [Princípio da Responsabilidade Única](#principio-da-responsabilidade-unica)
* [DRY: Don't Repeat Yourself](#dry-dont-repeat-yourself)
* [CSS Orientado a Objeto](#css-orientado-a-objeto)
   * [Separar estrutura do estilo](#separar-estrutura-do-estilo)
   * [Separar recipiente do conteúdo](#separar-recipiente-do-conteudo)
* [Convenção de nomenclaturas](#convencao-de-nomenclaturas)

## Princípio da Responsabilidade Única
O **Princípio da Responsabilidade Única** é um dos princípios SOLID, que são uma série de cinco princípios introduzidos por Robert C. Martin, como boas práticas de design de software. O **Princípio da Responsabilidade Única**, foca na preocupação de que _uma classe tenha seu papel e venha desempenhar **somente ele** de forma eficiente_. ([SOLID](http://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29))

## DRY: Don't Repeat Yourself
_Don't repeat yourself_ (Não repita a si mesmo) ou também conhecido pelo acrônimo **DRY**, é um conceito de programação que propõe que cada pedaço de conhecimento deve ter uma única representação livre de ambiguidades, e autoritária dentro de um sistema. ([DRY](http://pt.wikipedia.org/wiki/Don't_repeat_yourself))

## CSS Orientado a Objeto
Um "objeto" CSS é um padrão visual de repetição que pode ser abstraído independentemente em um trecho de HTML, CSS e JavaScript. Esse objeto pode então ser reutilizado em diferentes partes do projeto e ainda possuir a mesma aparência. ([OOCSS](https://github.com/stubbornella/oocss/wiki))

### Separar estrutura do estilo
Separar estrutura do estilo significa definir padrões visuais que se repetem, como `background` e `color`, e atribuir a um objeto que terá como única responsabilidade cuidar daquela tarefa. Por exmplo, podemos criar uma classe `btn` que será responsável por cuidar da estrutura padrão de um botão:

``` scss
// objects/_buttons.scss

.btn {
  border: 1px solid;
  display: inline-block;
  margin: 1em 1.5em;
  padding: 1em;
}
```

Em seguida criamos os modificadores para tratar o estilo desse botão:

``` scss
// components/_buttons.scss

.btn--large {
  font-size: 1.5em;
  margin: 1.5em 2em;
  padding: 1.5em;
}

.btn--primary {
  background: #1abc9c;
  border-color: rgba(0,0,0, .2);
  color: #fff;
}

.btn--secondary {
  background: #d6dddd;
  border-color: rgba(0,0,0, .1);
  color: #828B91;
}

```

E aplicando no nosso markup fica:

``` html
<button class="btn btn--primary">Button Primary</button>
<button class="btn btn--secondary">Button Secondary</button>
```

### Separar recipiente do conteúdo
Isso significa que um objeto deve ter a mesma aparência, não importa onde você o colocar. Então, ao invés de estilizar um `<h2>` específico com `.my-object h2`, devemos criar uma classe específica que descreva esse `<h2>`, por exemplo:

``` scss
// Ruim
.my-object { ... }
.my-object h2 { ... }

// Bom
.my-object { ... }
.post-title { ... }
```

``` html
<!-- Ruim -->
<div class="my-object">
  <h2> ... </h2>
</div>

<!-- Bom -->
<div class="my-object">
  <h2 class="post-title"> ... </h2>
</div>
```

Isso nos dá a garantia que:

1. Todos `<h2>` sem classe terão a mesma aparência;
2. Todos os elementos com a classe `post-tile` terão a mesma aparência;
3. Você não vai precisar criar outra regra caso você precise que, em algum momento, `.my-object h2` tenha a mesma aparência que um `<h2>` normal.

## Convenção de Nomenclaturas
Vamos criar uma classe para definir os estilo de uma lista de links:
``` scss
.link-blue {
  color: blue;
}
```
Mas e se no decorrer do projeto esse link tiver que ser de outra cor?
``` scss
.link-blue {
  color: red;
}
```
O nome dessa classe passa a não fazer mais sentido, concorda? Além disso, no caso dessa mudança, você vai precisar trocar não só o nome da classe no seu CSS, como também terá que alterar todo o markup do seu HTML.

Pensando nisso o ideal é sempre dar nome a algo pela natureza **do que é**, ao invés do **que ele parece**.
``` scss
.link-primary {
  color: blue;
}
```
Dessa forma, se precisarmos alterar a cor para vermelho, o nome da classe continua fazendo sentido:
``` scss
.link-primary {
  color: red;
}
```
É importante usar uma convenção de nomenclatura consistente nos seletores para manter o código mais organizado, fácil de ler e de dar manutenção.

Para isso, utilizaremos [Metodologia BEM](http://bem.info).

### BEM
A sigla **BEM** significa _Block_ (Bloco), _Element_ (Elemento), _Modifier_ (Modificador) e segue essas propriedades para definir uma metodologia de criação de nomes para classes.
``` scss
.block { ... }
.block__element { ... }
.block--modifier { ... }
```

#### Block
* `Block` é uma entidade independente, é um "bloco de construção" de um aplicativo;
* Os `Block` podem ser usados sozinhos ou como parte de outros `Block`.

``` scss
.search-form { ... }
```

``` html
<form action="" class="search-form">
  ...
</form>
```

#### Element
* `Element` é a parte de um `Block` que executa uma certa função;
* O `Element` de um `Block` só pode aparecer dentro de seus respectivos `Block`;
* Os `Element` podem ser obrigatórios ou opicionais.


``` scss
.search-form { ... }
.search-form__input { ... }
.search-form__button { ... }
```

``` html
<form action="" class="search-form">
  <input class="search-form__input" type="text" value="Text">
  <button class="search-form__button">Submit</button>
</form>
```

#### Modifier
* `Modifiers` são variações de um `Block` e/ou `Element`;
* Eles alteram a aparência e/ou comportamento dos `Block` ou `Element` em que são aplicados;
* Cada `Modifier` tem um nome e um valor;
* Vários `Modifier` podem ser aplicados simultaneamente.

``` scss
.search-form { ... }
.search-form--small { ... }
.search-form--dark { ... }
```

``` html
<form class="search-form search-form--small">
  <input class="search-form__input" type="text" value="Text">
  <button class="search-form__button">Submit</button>
</form>
```
ou
``` html
<form class="search-form search-form--dark search-form--small">
  <input class="search-form__input" type="text" value="Text">
  <button class="search-form__button">Submit</button>
</form>
```

