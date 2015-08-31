# Front-end Guidelines
Esse guia tem como objetivo:

* Tornar o código mais consistente;
* Facilitar a manutenção;
* Modularizar os componentes;
* Melhorar a performance das aplicações.

# Índice do conteúdo

* Introdução
* CSS Orientado a Objeto
  * Aplicando os princípios de SOLID
      * Responsabilidade Única
      * Aberto / Fechado
      * Substituição de Liskov
      * Segregação de Interfaces
      * Inversão de Dependência
  * Separar estrutura do estilo
  * Separar recipiente do conteúdo
* DRY: Don't Repeat Yourself
* Convenção de nomenclaturas
  * Criando abstrações
  * Metodologia BEM
  * Classes de nomes compostos
* Padrões de escrita do CSS
  * Indenteção
  * Seletores
  * Propriedades
    * Ordenação
* Preprocessadores
  * Aninhamento
  * Variáveis
  * Funções
  * Mixins
  * Extends
  * Operadores
* Arquitetura da aplicação
  * Metodologias aplicadas
  * Organização dos arquivos  
* Componentes
  * Namespacing
  * Arquitetura dos componentes
  * Pontos de entrada

## Introdução

Esse guia de estilo tem como base [uma série de referências](#refer%C3%AAncias), com algumas modificações que serão feitas de acordo com as necessidades dos projetos do Ministério da Saúde.

## CSS Orientado a Objeto

O _**CSS Orientado a Objeto**_ parte do conceito de que o _"objeto"_ CSS é um padrão visual de repetição que pode ser abstraído de forma independente. Esse objeto pode então ser reutilizado em diferentes partes do projeto e ainda possuir a mesma aparência.

### Aplicando os princípios de SOLID

**SOLID** é um acrônimo para _**Single responsibility, Open-closed, Liskov substitution, Interface segregation**_ e _**Dependency inversion**_. Este termo foi criado por [Robert C. Martin](http://pt.wikipedia.org/wiki/Robert_Cecil_Martin) para unir alguns padrões muito utilizados no design de software, que visam facilitar a manutenção, legibilidade e vida do nosso código.

1. #### Responsabilidade Única

  Uma classe deve **fazer apenas uma coisa, deve fazê-la bem e deve fazer somente ela**. No exemplo abaixo temos o objeto que define os padrões de um botão na nossa aplicação.

2. #### Aberto / Fechado

  Um objeto estará sempre **aberto para extensões** e **fechado para modificações**. Isso significa que quando um objeto é definido, não deverá mais ser alterado. Se em algum momento você precisar alterar seu objeto, é o caso de rever toda a estrutura do seu código. Ao invés de alterar seu objeto base, deve-se criar modificadores.

3. #### Substituição de Liskov

  O princípio de substituição de Liskov define que **os objetos de uma determinada hierarquia podem ser substituídos por qualquer um dos seus subtipos**. Sendo assim a noção de subtipo passa a ser determinada pela noção de substituição.

4. #### Segregação de Interfaces

  **Classes específicas são melhores do que uma classes genéricas**. Isso faz com que nosso código tenha mais coesão e o torna fácil de dar manutenção e de ser refatorado ou alterado.

5. #### Inversão de Dependência

  * **Módulos de alto nível não devem depender de módulos de baixo nível**. Ambos devem depender de **abstrações**.
  * **Abstrações não deve depender de detalhes**. Os detalhes que devem depender de abstrações.

### Separar estrutura do estilo

Separar estrutura do estilo significa definir padrões visuais que se repetem, como `color` e `margin`, e atribuir a um objeto que terá como única responsabilidade cuidar daquela tarefa. Por exmplo, podemos criar uma classe `btn` que será responsável por cuidar da estrutura padrão de um botão:

``` scss
// objects/_buttons.scss

.btn {
  border: 1px solid;
  display: inline-block;
  font-size: 1.2em;
  margin: 1em 1.5em;
  padding: 1em;
}
```

<sup>_**Princípio SOLID:** Responsabilidade Única_</sup>

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

<sup>_**Princípios SOLID:** Aberto/Fechado e Substituição de Liskov_</sup>

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

<sup>_**Princípio SOLID:** Segregação de Interfaces_</sup>

Isso nos dá a garantia que:

1. Todos `<h2>` sem classe terão a mesma aparência;
2. Todos os elementos com a classe `post-tile` terão a mesma aparência;
3. Você não vai precisar criar outra regra caso você precise que, em algum momento, `.my-object h2` tenha a mesma aparência que um `<h2>` normal.

## DRY: Don't Repeat Yourself

_**Não repita a si mesmo**_ ou também conhecido pelo acrônimo **DRY**, é um conceito que busca reduzir a duplicação de código e os problemas de manutenção resultantes. **DRY** tem como base o **Princípio da Responsabilidade Única**.

Com a chegada dos preprocessadores esse conceito se tornou muito mais fácil de ser aplicado no front-end. Falaremos mais sobre DRY em _preprocessadores_.

## Convenção de Nomenclaturas

Criamos uma classe para definir os estilo de uma lista de links:

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

### Criando abstrações

Pensando nisso o ideal é sempre melhor dar nome a algo pela natureza **do que é**, ao invés do **que ele parece**.

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

<sup>_**Princípio SOLID:** Inversão de Dependência_</sup>

É importante usar uma convenção de nomenclatura consistente nos seletores para manter o código mais organizado, fácil de ler e de dar manutenção.

Para isso, utilizaremos a [Metodologia BEM](http://bem.info).

### BEM: Block, Element, Modifier

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

* `Modifiers` são variações de um `Block` ou `Element`;
* Eles alteram a aparência e/ou comportamento dos `Block` ou `Element` em que são aplicados;
* Cada `Modifier` tem um nome e um valor;
* Um `Modifier` tem por padrão **dois _hiféns_** `--` que o separam do seu `Block` ou `Element`;
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

#### TL;DR

Para alguns pode parecer um pouco feio e estranho utilizar **BEM**, mas vamos otimizar essa prática quando falarmos sobre **preprocessadores**.

***

## Padrões de escrita do CSS

Com os conceitos definidos, agora é preciso definir um padrão consistente de escrita para tonar a manutenção do código o menos complicado possível.

### Comentários

Para tornar ainda mais fácil a legibilidade e entendimento do nosso CSS, é essencial manter cada seção identificada e comentada. Abaixo seguem os padrões de comentários que devem ser utilizados.

* Todo arquivo CSS deve ser identificado com um **cabeçalho**;

``` scss
//=====================================
//
//  Título: Descrição
//
//=====================================



```

* Cada **seção** de um arquivo também deve ser identificado com um título
* Títulos de uma seção devem ser escritos em caixa alta e com um `#` no primeiro caractere;
* Deve haver uma linha em branco entre o título e o conteúdo;

``` scss
//-------------------------------------
//  #NAVIGATION
//-------------------------------------

.nav { ... }
.nav__item { ... }
```

* Entre cada uma das seções deve haver **três linhas de separação**;

``` scss
//-------------------------------------
//  #HEADER
//-------------------------------------

.header { ... }
.header__logo { ... }



//-------------------------------------
//  #NAVIGATION
//-------------------------------------

.nav { ... }
.nav__item { ... }
```

### Indenteção

* Indentar sempre utilizando 2 espaços.

``` scss
// Ruim
.my-selector {
    property: value;

    .sub-selector {
        property: value;
    }
}

// Bom
.my-selector {
  property: value;

  .sub-selector {
    property: value;
  }
}
```

### Seletores

* Apenas um seletor por linha quando houver uma regra para multi-seletores;

``` scss
// Ruim
html, body, div { ... }

// Bom
html,
body,
div { ... }
```

* Atributos de seletores sempre com **àspas duplas**. Ex: `input[type="checkbox"]`;
* Ao fechar a chave `}` de um seletor, alinhe com a primeira coluna do seletor seguinte;

``` scss
// Ruim
.selector {
  property: value;
  }

.other-selector {
  property: value;}

// Bom
.selector {
  property: value;
}

.other-selector {
  property: value;
}
```

* Separar cada grupo de regras com uma linha em braco;

``` scss
// Ruim
.nav {
  ...
}
.nav__item {
  ...
}

// Bom
.nav {
  ...
}

.nav__item {
  ...
}
```

### Propriedades

* Manter propriedades relacionadas agrupadas;
* Os grupos de propriedades devem estar em ordem alfabética;

``` scss
// Ruim
.selector {
  margin-top: 15px;
  text-align: center;
  width: auto;
  font-size: 14px;
  padding: 10px 20px;
  margin-bottom: 20px;
  text-decoration: none;
  height: 20px;
}

// Bom
.selector {
  font-size: 14px;
  text-align: center;
  text-decoration: none;
  margin-bottom: 20px;
  margin-top: 15px;
  padding: 10px 20px;
  height: 20px;
  width: auto;
}
```

* Utilizar espaços em branco para facilitar a leitura;

``` scss
.selector {
  font-size: 14px;
  margin:    20px;
  padding:   10px 20px;
  height:    20px;
  width:     auto;
}
```

***

## Preprocessadores CSS

Preprocessadores CSS são scripts que recebem um CSS com uma semântica específica e efetua sua conversão para o CSS tradicional. Com ele é possível escrever o CSS com muito mais performance e manutenabilidade. 

### SASS

Utilizaremos o Sass como preprocessador. Sass é o mais maduro, estável, completo e profissional preprocessador CSS que existe. Alguns de seus benefícios:

* Compatível com CSS tradicional;
* Maior comunidade;
* Baixa curva de aprendizado;
* Recursos:
  * Nesting (aninhamento);
  * Variables (variáveis);
  * Functions (funções);
  * Placeholders (substituidores);
  * Extends (extensões);
  * Mixins (comportamentos);
  * Operators (operadores)
  * Partials (fragmentos);
  * Imports (importações).

**Aninhamento (Nesting)**

Forma clara de definir a hierarquia entre elementos CSS.

``` scss
// SCSS
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}

// CSS
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}

nav li {
  display: inline-block;
}

nav a {
  display: block;
  padding: 6px 12px;
  text-decoration: none;
}
```

**Variáveis (variables)**

Variáveis são uma forma de armazenar informações e reutilizar ao longo do projeto.

``` scss
// SCSS
$font-stack:    Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}

// CSS
body {
  font: 100% Helvetica, sans-serif;
  color: #333;
}
```

**Funções (functions)**

Funções são utilizadas para realizar uma lógica e retornar um valor específico.

``` scss
// _functions.scss
$grid-columns: 12;

@function column($i) {
  $col: percentage(1 / $grid-columns * $i);
  @return $col;
}

// _grid.scss
.col-6 {
  width: column(6);
}

// main.css
.col-6 {
  width: 50%;
}
```

**Mixins (comportamentos)**

Mixins são utilizadas para definir blocos de código reutilizaveis. Também podemos passar parâmetros através de Mixins e definir lógicas como if, else, while, for, each, etc.

``` scss
// SCSS
@mixin border-radius($radius) {
  -webkit-border-radius: $radius;
     -moz-border-radius: $radius;
      -ms-border-radius: $radius;
          border-radius: $radius;
}

.box { @include border-radius(10px); }

// CSS
.box {
  -webkit-border-radius: 10px;
  -moz-border-radius: 10px;
  -ms-border-radius: 10px;
  border-radius: 10px;
}
```

**Placeholders (substituidores)**

São seletores que não são compilados e dependem do @extend para serem utilizados.

``` scss
// SCSS
%clearfix {
  &:before,
  &:after {
    content: " ";
    display: table;
  }

  &:after { clear: both; }
}

.wrapper {
  @extend %clearfix;
  margin: 0 auto;
  width: 960px;
}

```

**Extends (extensões)**

Extenções são utilizadas para extender uma classe à outra e herdar suas propriedades.

``` scss
// SCSS
.message {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.success {
  @extend .message;
  border-color: green;
}

.error {
  @extend .message;
  border-color: red;
}
```

Mas cuidado, o uso excessivo do @extend pode acabar com a semântica do código:

``` scss
.product .single_add_to_cart_button, .cart 
.button, input.checkout-button.alt.button, 
.shipping-calculator-form .button, 
.multistep_step .button, #place_order.button, 
.single-product 
.single_add_to_cart_button.button.alt, 
.woocommerce a.button, .woocommerce 
button.button, .woocommerce input.button, 
.woocommerce #respond input#submit, 
.woocommerce #content input.button, 
.woocommerce-page a.button, 
.woocommerce-page button.button, 
.woocommerce-page input.button, 
.woocommerce-page #respond input#submit, 
.woocommerce-page #content input.button {
  background-color: #605f5e;
}
```

**Operadores**

Efetuar operações matemáticas em CSS é muito útil. O Sass conta com operadores matemáticos padrões como +, -, *, / e %. 

``` scss
//SASS
.container { width: 100%; }

article[role="main"] {
  float: left;
  width: (600px / 960px) * 100%;
}

aside[role="complimentary"] {
  float: right;
  width: (300px / 960px) * 100%;
}

//CSS
.container {
  width: 100%;
}

article[role="main"] {
  float: left;
  width: 62.5%;
}

aside[role="complimentary"] {
  float: right;
  width: 31.25%;
}
```

### Partials e Imports

**Partials (fragmentos)**

São arquivos prefixados com _ e são compilados junto com o arquivo principal.

```
styles
|
|–– utilities
|   |–– _variables.scss
|   |–– _functions.scss
|   |–– _mixins.scss
|
|–– components
|   |–– _buttons.scss
|   |–– _carousel.scss
|   |–– _navigation.scss
|
|–– main.scss
```

**Imports (importações)**

O Sass entende quando um arquivo é uma partial e não gera um arquivo separado.

```
// main.scss

@import "utilities/variables";
@import "utilities/functions";
@import "utilities/mixins";

@import "components/buttons";
@import "components/carousel";
@import "components/navigation";
```

***

## Arquitetura da Aplicação

Além de organizado, o código criado para o `Style Guide` precisará ser eficiente. Conheça algumas curiosidades que podem motivar qualquer engenheiro frontend a ser louco por performance:

* Usuários esperam páginas carregarem dois segundos. Depois do 3º há `40% de taxa de abandono`;
* A Amazon notou que `100 milissegundos` de espera representam `1% de declínio das vendas`;
* O Google perdeu 20% de lucratividade e tráfego de usuários por casa de um aumento de meio segundo no tempo de carregamento dos resultados;
* O Google Maps ao reduzir `de 100kb para 80kb aumentou o tráfego em 10%` na primeira semana e `25% nas duas semanas seguintes`;
* O time da Etsy percebeu um `aumento na taxa de rejeição em 12%` em dispositivos móveis quando adicionaram `160kb na página`;

<sup>_UX Design - Casa do Código de Fabrício Teixeira_</sup>

### ITCSS

Escrever CSS é muito fácil, mas escrever CSS escalável, reutilizável, manutenível e de fácil compreensão não é assim tão fácil. 

A ideia por trás do ITCSS é organizar seu CSS como se ele fosse um [triângulo invertido](https://hackpad-attachments.s3.amazonaws.com/hackpad.com_PIPWVUMv6rB_p.296099_1420807134466_itcss.jpg), formado por diversas camadas. 

* O código deve ser organizado em **camadas**, da menos específica para a mais específica. Da base para o topo. Uma boa organização então seria:
  * Configurações
  * Ferramentas
  * Genéricos
  * Base
  * Objetos
  * Componentes
  * “Trumps” ou Hacks

**ITCSS - SETTINGS - Configurações (se usar pré-processador)**

Estas configurações podem ser variáveis globais de cor e espaçamento ou então variáveis que ativam módulos do seu Style Guide.

``` scss
//-------------------------------------
//  #SETTINGS - Configurações (se usar pré-processador)
//-------------------------------------

    $base-font-size: 14px;
    $base-line-height: 24px;
    $color-gray: #CCC;
    $color-red: #FF0000;

    $color-text: $color-gray;
    $color-danger: $color-red;
```

**ITCSS - TOOLS - Ferramentas (se usar pré-processador)**

O ITCSS também sugere uma camada para ferramentas, caso você use um pré-processador. Nela você colocará todos os seus mixins e funções. Coisas como px-to-rem ou font-face.
``` scss
//-------------------------------------
//  #TOOLS - Ferramentas (se usar pré-processador)
//-------------------------------------

.exemplo {...};
```
**ITCSS - GENERICS - Estilos genéricos**

É nesta camada que fica o código que deve definir regras muito genéricas. Além de usá-la para colocar um reset ou um normalize, você também pode escrever seletores bem abrangentes. Exemplo: Normalize.

``` scss
//-------------------------------------
//  #GENERICS - Estilos genéricos
//-------------------------------------

* {
    box-sizing: border-box;
    outline-color: $color-brand;
}
```

**ITCSS - BASE (base)**
Seguindo para baixo do triângulo invertido temos a camada base, uma casa para seletores para estilização básica destes elementos da nossa página. Aqui definimos a aparência de `<a>`, `<blockquote>`, `<ul>`, `<h1>` e todos os outros elementos HTML desejados.

``` scss
//-------------------------------------
//  #BASE (base)
//-------------------------------------

a {
    color: $color-attention;
}

mark {
    background-color: $color-highlight;
    padding: 2px 4px;
}
```

**ITCSS - OBJECTS (objetos)**

Baseado nos princípios de CSS orientado a objetos, o ITCSS sugere uma camada para objetos.

Objetos são pequenos pedaços de uma interface, normalmente padrões de design, que se repetem em todo o seu site. Objetos media, .button e .ui-list são alguns que se encaixam nesta camada.

O ITCSS estabelece que devemos utilizar apenas classes a partir daqui. Um exemplo utilizando o padrão de nomenclatura BEM seria:

``` scss
//-------------------------------------
//  #OBJECTS (objetos)
//-------------------------------------

.ui-list{
    margin: 0;
    padding: 0;
    list-style: none;
}

    .ui-list__item {
        padding: $spacing-unit;
    }
```
**ITCSS - COMPONENTS (componentes)**

Partes de uma interface com estilos mais definidos e específicos. É aqui que estilos para uma “lista de produtos” ou “cabeçalho principal” seriam encontrados.

``` scss
//-------------------------------------
//  #COMPONENTS (componentes)
//-------------------------------------

.products-list {
    border-top: 1px dashed $color-brand;
}

    .products-list__item {
        border-bottom: 1px solid $color-brand;
    }
```
**ITCSS - HACKS (ou trumps)**

Um ótimo exemplo é uma classe como .hidden. Você a usaria quando quisesse fazer algo desaparecer. Segundo o ITCSS, aqui é perfeitamente aceitável que você use !important, já que estas classes devem ser utilizadas ativamente, não em reação a um problema de especificidade.

``` scss
//-------------------------------------
//  #HACKS (ou trumps)
//-------------------------------------

.hidden {
    display: none !important;
}

```

***

## Componentes

### Namespacing

Em breve

### Arquitetura dos componentes

Em breve

### Pontos de entrada

Em breve

***

## Referências

Referências utilizadas na escrita desta documentação:

#### Metodologias

* [SOLID: Object Oriented Design](http://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29)
* [OOCSS: Object Oriented CSS](https://github.com/stubbornella/oocss/wiki)
* [DRY: Don't Repeat Yourself](http://pt.wikipedia.org/wiki/Don't_repeat_yourself)

#### Padrões de escrita

* [Idiomatic CSS](https://github.com/necolas/idiomatic-css/)
* [CSS Guidelines](http://cssguidelin.es/)
* [Sass Guidelines](http://sass-guidelin.es/)

#### Padrões de nomenclatura

* [BEM Methodology](http://bem.info)

#### Arquitetura CSS

* [ITCSS](http://itcss.io/)