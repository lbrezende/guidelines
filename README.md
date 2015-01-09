
# Guia de estilos do Ministério da Saúde
Esse guia foi escrito para definir padrões de escrita e arquitetura na hora de desenvolver o front-end das aplicações do Ministério da Saúde. Esses novos padrões tem como objetivo:

* Tornar o código mais consistente;
* Facilitar a manutenção;
* Modularizar os componentes;
* Aumentar a performance das aplicações.

# Índice do conteúdo

* [Introdução](#introducao)
* [SRP: Single Responsibility Principle](#srp-single-responsibility-principle)
* [DRY: Don't Repeat Yourself](#dry-dont-repeat-yourself)
* [OOCSS: Object Oriented CSS](#oocss-object-oriented-css)
   * [Separar estrutura do estilo](#separar-estrutura-do-estilo)
   * [Separar recipiente do conteúdo](#separar-recipiente-do-conteudo)
* [Convenção de nomenclaturas](#convencao-de-nomenclaturas)
   * [Metodologia BEM](#bem)
   * [Classes de nomes compostos](#classes-de-nomes-compostos)
* [Padrões de escrita do CSS](#padroes-de-escrita-do-css)
  * [Seletores](#seletores)
     * [Propriedades](#propriedades)
        * [Ordenação](#ordenacao)
     * [Aninhamento](#aninhamento)
     * [Indenteção](#indentacao)
* [Arquitetura da aplicação](#arquitetura-da-aplicacao)
* [Preprocessadores](#preprocessadores)
   * [Funções](#funcoes)
   * [Mixins](#mixins)
* [Componentes](#componentes)
   * [Namespacing](#namespacing)
   * [Arquitetura dos componentes](#arquitetura-dos-componentes)
   * [Ponto de entrada](#ponto-de-entrada)
   * [Gerenciamento de pacotes](#gerenciamento-de-pacotes)
   * [Diretórios](#diretorios)
   * [Dependências](#dependencias)

## Introdução
Esse guia de estilo não sugiu do nada, ele tem como base o [Idiomatic Sass](https://github.com/anthonyshort/idiomatic-sass), que é uma adaptação do [Idiomatic CSS](https://github.com/necolas/idiomatic-css/tree/master/translations/pt-BR) originalmente escrito por [Nicolas Gallagher](https://github.com/necolas), mas com algumas modificações que serão aplicadas nos projetos do Ministério da Saúde.

## SRP: Single Responsibility Principle
O _**Princípio da Responsabilidade Única**_ é um dos cinco princípios SOLID, introduzidos por [Robert C. Martin](http://pt.wikipedia.org/wiki/Robert_Cecil_Martin), como boas práticas do design de software. O **SRP**, foca na preocupação de que _uma classe tenha seu papel e venha desempenhar **somente ele** de forma eficiente_. Ou seja: cada classe tem sua função. Se precisamos de outra função, precisamos de outra classe. Vejamos um exemplo simples em um sistema de grid:

``` scss
.row {
  float: none;
  margin-left: auto;
  margin-right: auto;
  max-width: 100%;
  width: 60em;
}

.row__col {
  box-sizing: border-box;
  float: left;
  padding-left: .9375em;
  padding-right: .9375em;
  position: relative;
  &:last-of-type {
    float: right;
  }
}

.row__col--1 { width: 8.3333%; }
...
.row__col--6 { width: 50%; }
...
.row__col--12 { width: 100%; }
```

Analisando o exemplo acima, temos três classes: `.row`, `.row__col` e `.row__col--$`. Cada uma dessas classes tem uma única responsabilidade:

* `.row` é responsável por dividir o layout em linhas e delimitar a largura da página;
* `.row__col` é responsável por determinar o comportamento padrão das colunas do grid, o posicionamento e espaçamento entre cada uma;
* `.row__col--$` aqui temos os modificadores da coluna. Nesse caso, os modificadores são responsáveis por determinar qual a largura de cada uma das colunas do grid.

_Referência: [SOLID: Object Oriented Design](http://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29)_

## DRY: Don't Repeat Yourself
_**Não repita a si mesmo**_ ou também conhecido pelo acrônimo **DRY**, é um conceito de programação que busca reduzir a duplicação de código e os problemas de manutenção resultantes. Também segue como base o **Princípio da Responsabilidade Única**.

Com a chegada dos preprocessadores esse conceito se tornou muito mais fácil de ser aplicado no front-end. Por exemplo:

``` scss
// Antes dos preprocessadores e DRY:
.form__input {
  border: 1px solid #333
  color: #333;
  padding: .5em;
}

// Aplicando Sass e DRY:
$form-input-color: #333;

.form__input {
  border: 1px solid $form-input-color
  color: $form-input-color;
  padding: .5em;
}
```

Dessa forma, podemos aplicar a variável `$form-input-color` em qualquer parte do projeto que terá exatamente o mesmo valor. E caso seja preciso manutenção, basta alterar o valor dessa variável.

_Referência: [DRY: Don't Repeat Yourself](http://pt.wikipedia.org/wiki/Don't_repeat_yourself)_

## OOCSS: Object Oriented CSS
O _**CSS Orientado a Objeto**_ parte do conceito de que o _"objeto"_ CSS é um padrão visual de repetição que pode ser abstraído independentemente. Esse objeto pode então ser reutilizado em diferentes partes do projeto e ainda possuir a mesma aparência.

_Referência: [OOCSS: Object Oriented CSS](https://github.com/stubbornella/oocss/wiki)_

### Separar estrutura do estilo
Separar estrutura do estilo significa definir padrões visuais que se repetem, como `color` e `margin`, e atribuir a um objeto que terá como única responsabilidade cuidar daquela tarefa. Por exmplo, podemos criar uma classe `btn` que será responsável por cuidar da estrutura padrão de um botão:

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
  border-color: rgba(#000, .2);
  color: #fff;
}
```

E aplicando no nosso markup fica:

``` html
<button class="btn btn--primary">Button Primary</button>
<button class="btn btn--large">Button Large</button>
```

Também podemos usar os dois modificadores no mesmo objeto:

``` html
<button class="btn btn--primary btn--large">Button Primary Large</button>
```

Dessa forma dividimos um elemento em blocos e montamos conforme for a necessidade.

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

Aumentando a dimensão desse problema, imagine uma aplicação onde se tem aproximadamente 200 casos de uso. Torna-se praticamente impossível dar manutenção em um problema tão simples.

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

Para isso, utilizaremos a [Metodologia BEM](http://bem.info).

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
* Os `Element` podem ser opicionais;
* Um `Element` tem por padrão dois _underscores_ `__` que o separam do seu `Block`.

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
* Um `Modifier` tem por padrão dois _hiféns_ `--` que o separam do seu `Block` ou `Element`;
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

#### Classes de nomes compostos

Todas as classes de nomes compostos devem ser separadas com um _hifén_ (`-`).

``` scss
// Ruim
.topNav { ... } // CamelCase
.top_nav { ... } // Undersocore

// Bom
.top-nav { ... } // Dash
```
