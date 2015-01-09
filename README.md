
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
* [Padrões de escrita do CSS](#padroes-de-escrita-do-css)
  * [Seletores](#seletores)
     * [Propriedades](#propriedades)
        * [Ordenação](#ordenacao)
     * [Aninhamento](#aninhamento)
     * [Indenteção](#indentacao)
* [Estrutura de arquivos](#estrutura-de-arquivos)
* [Preprocessadores](#preprocessadores)
   * [Funções](#funções)
   * [Mixins](#mixins)
* [Componentes](#componentes)
   * [Estrutura de arquivos](#estrutura-de-arquivos-1)
   * [Namespacing](#namespacing)
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

## Seletores
* Use one discrete selector per line in multi-selector rulesets.
* Quote attribute values in selectors, e.g., input[type="checkbox"].
* Place the closing brace of a ruleset in the same column as the first character of the ruleset.
* Separate each ruleset by a blank line.
* Include a single space before the opening brace of a ruleset.

## Properties
* Include one declaration per line in a declaration block.
* Use one level of indentation for each declaration.
* Use lowercase and shorthand hex values, e.g., #aaa.
* Use single or double quotes consistently. Preference is for double quotes, e.g., content: "".
* Where allowed, avoid specifying units for zero-values, e.g., margin: 0.
* Include a semi-colon at the end of the last declaration in a declaration block.
* Include a space after each comma in comma-separated property or function values.

### Ordering
1. `$variable` should **always** appear at the top.
2. `@extend` should always appear before properties. It's like extending a class in Ruby.
3. `@include` should appear second. This allows the properties to override the mixins.
4. Properties should appear after this, optionally grouped by type or sorted alphabetically.
5. Mixins with content blocks should appear next. `@include someMixin { properties }`
6. Selectors that target itself. `&.modifier`
7. Child selectors appear last.

The basic rule of thumb is at-rules, properties, then blocks.

Here is an example of a well-formed selector:

``` scss
.selector-1,
.selector-2,
.selector-3[type="text"]  {
  $bg: blue;
  $fallback: green;

  @extend .clearfix;
  @include border-box;

  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  display: block;
  font-family: helvetica, arial, sans-serif;
  color: #333;
  background: #fff;
  background: linear-gradient(#fff, rgba(0, 0, 0, 0.8));

  @include after {
    position: absolute;
  }

  &.selector--modifier {
    background: red;
  }

  .selector__child {
    display: none;
  }
}
```

## Nesting
* Avoid nesting more than 2 deep. This is a sign of bad CSS as selectors become too specific.
* Rulesets within selectors should be separated by a single line and follow the same rules as any other selector.

## Indentation
* Indentation should be 2 spaces

## File Structure
* Each logical module of code should belong in its own file. Avoiding putting multiple objects in the same file. This allows you to use the filesystem to navigate your Sass rather than relying on comment blocks.
* Mixins/placeholders/functions should, if possible, belong in their own file.
* Files should be named for the component they are housing. A `block-list` object will live in a `block-list.scss` file.

## Functions
* Functions should be prefix with a dash and a namespace: `-rg-columns`
* The namespace can be dropped if it is a private function: `columns`
* Functions should be documented using DocBlock or similar.

## Mixins
* Mixins should only be used when there are dynamic properties, otherwise use `@extend`
* Mixins that output selectors should be capital-case: `@mixin GridBuilder`
* Mixins that output only properties should be camel-case: `@mixin borderBox`
* Mixins should be prefixed if they are part of a public module: `@mixin as-GridBuilder`
* In general, mixins with logic should not be longer than ~50 lines just like any other programming language
* Private mixins that are not used outside of the current file should be prefixed with a dash: `@mixin -gridHelper`
* Avoid using more than 4 parameters. It is a sign that a mixin is too complex. When Sass adds hashes life will be easier.
* Mixins should be documented

``` scss
// Loop through each breakpoint and build
// classes for each using the breakpoint mixins
// First breakpoint is no media query — mobile-first.
//
// @param {List} $breakpoints List of column breakpoints
// @param {Boolean} $spacing Include spacing classes?
// @param {Boolean} $visibility Include visibilty classes?
// @param {Boolean} $layout Include layout classes?
// @api private
@mixin -rg-Breakpoints($breakpoints, $spacing: true, $visibility: true, $layout: true) {
  @each $columns in $breakpoints {
    @if index($breakpoints, $columns) == 1 {
      @include -rg-BreakpointClasses($columns, $spacing, $visibility, $layout);
    }
    @else {
      @include rg-from($columns) {
        @include -rg-BreakpointClasses($columns, $spacing, $visibility, $layout);
      }
    }
  }
}
```

## Modules/Packages

Sharing Sass code is becoming more important. Without the use of a proper module system in Sass
we need to establish a few rules so that sharing code is consistent and behaviour is predictable.
Sass packages are popping up in Bower and Github but there is no consistency in the way they
are implemented.

A few general rules:

* Every module *must* have a namespace
* Private functions and mixins should be prefixed with a dash: `@mixin -rg-gridUnit`
* Importing a module should not render any selectors
* Mixins/placholders/functions should be able to be imported separately `@import "rg-Grid/mixins"`
* Avoid relying on global variables.
* Use placeholder selectors whenever possible.
* All global variables must be namespaced.

### File Structure

* Third-party, installed modules should always be placed in a `components` directory.
* Local modules should live in a `local` directory adjacent to the `components` directory.
* All images, fonts and other assets should live in an `assets` directory

Example structure:

```
/module-name
  /assets
    /fonts
    /images
  /components
    /responsive-grid
    /clearfix
    /animation
  /local
    /homepage
  /lib
    /mixins
    /functions
  bower.json
  index.scss
```

### Namespacing

* Every selector, placeholder, mixin and function that is imported should be namespaced
* Namespaces should be short (2-5 characters) and suffixed with a dash: `rg-Grid`

### Module Entry Point

* Each module should have an `index.scss` file as the entry point: `@import "module-name/index"`.
* The entry point file does not require an underscore in the file name as each module should be able to be compiled and used individually.
* Importing this entry point file should not render anything in the output
* There must be an **entry-point mixin named for that module**. eg. a `rg-Grid` module would have a `rg-Grid` mixin

### Package Management

* The preferred package manager for Sass packages at the moment is Bower.
* Avoid registering packages in Bower whenever possible and instead rely on the Github shorthand syntax - `user/project` eg. `fonzie/responsive-grid`
* All packages are installed into the `components` directory

### Load Paths

* A load path to the `components` directory is assumed.

### Dependencies

As every module does not output anything just by being imported, packages can safely import other packages
without the fear that a file has already been imported. Because of this, dependencies can safely require
their own dependencies.

For example, a `Grid` package depends on a `Clearfix` package, but so does the `LayoutHelpers` package. Both
of these packages can `@import "clearfix/index"` without fear that there will be two `.clearfix` classes in the output.

It is assumed that the `components` directory is added as a load path, so packages can easily require their dependencies.


## Definitions

#### Object

A single piece of the design, usually fairly small. This could be things like `.message`, `.block`, `.list`, `.post`.
Objects should be independent. Think of them as lego blocks. Objects have "modifiers" and "children".

#### Children

If an "object" is the parent, any sub-parts of that object are considered its children. Children are only ever
controlled by the object it belongs to. A `.message` object may have a title that it styles, `.message__title`,
this would be referred to as a child element.

#### Module

A single piece of functionality that can be composed of CSS, mixins, functions and assets (such as images or fonts).
A module has a single entry point and a single purpose and role. eg. A grid framework could be a module.

#### Package

When a module is shared with others via a package manager like Bower it will generally be referred to as a package.
This means that the term "module" and "packages" are fairly interchangable.

#### Block

This is another term for the concept of an "object".

#### Element

When referring to "objects" and "blocks", the word "element" is interchangable with the word "children".

#### Modifier

"Objects" may be modified in a way that changes their style in small ways, think of them as themes or alternative
styles. For example, a `.list` object may have a `.list--small` modifier to make the text smaller.
