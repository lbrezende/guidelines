# Guia de estilos do Ministério da Saúde
Esse guia foi escrito para definir padrões de escrita e arquitetura na hora de desenvolver o front-end das aplicações do Ministério da Saúde. Esses novos padrões tem como objetivo:

* Tornar o código mais consistente;
* Facilitar a manutenção;
* Modularizar os componentes;
* Aumentar a performance das aplicações.

# Índice do conteúdo

* [Introdução](#introducao)
* [CSS Orientado a Objeto](#css-orientado-a-objeto)
  * [Aplicando os princípios de SOLID](#aplicando-os-principios-de-solid)
      01. [Responsabilidade Única](#responsabilidade-unica)
      02. [Aberto / Fechado](#aberto-fechado)
      03. [Substituição de Liskov](#substituicao-de-liskov-e-segregação-de-interfaces)
      04. [Segregação de Interfaces](#segregação-de-interfaces)
      05. [Inversão de Dependência](#inversao-de-dependencia)
  * [Separar estrutura do estilo](#separar-estrutura-do-estilo)
  * [Separar recipiente do conteúdo](#separar-recipiente-do-conteudo)
* [DRY: Don't Repeat Yourself](#dry-dont-repeat-yourself)
* [Convenção de nomenclaturas](#convencao-de-nomenclaturas)
  * [Criando abstrações](#criando-abstracoes)
  * [Metodologia BEM](#bem-block-element-modifier)
  * [Classes de nomes compostos](#classes-de-nomes-compostos)
* [Padrões de escrita do CSS](#padroes-de-escrita-do-css)
  * [Indenteção](#indentacao)
  * [Seletores](#seletores)
  * [Propriedades](#propriedades)
    * [Ordenação](#ordenacao)
* [Arquitetura da aplicação](#arquitetura-da-aplicacao)
  * [Metodologias aplicadas](#metodologias-aplicadas)
  * [Organização dos arquivos](#organizacao-dos-arquivos)
* [Preprocessadores](#preprocessadores)
  * [Aninhamento](#aninhamento)
  * [Variáveis](#variaveis)
  * [Funções](#funcoes)
  * [Mixins](#mixins)
  * [Extends](#extends)
  * [Operadores](#operadores)
* [Componentes](#componentes)
  * [Namespacing](#namespacing)
  * [Arquitetura dos componentes](#arquitetura-dos-componentes)
  * [Pontos de entrada](#pontos-de-entrada)

## Introdução

Esse guia de estilo não sugiu do nada, ele tem como base o [Idiomatic Sass](https://github.com/anthonyshort/idiomatic-sass), que é uma adaptação do [Idiomatic CSS](https://github.com/necolas/idiomatic-css/tree/master/translations/pt-BR) originalmente escrito por [Nicolas Gallagher](https://github.com/necolas), mas com algumas modificações que serão aplicadas nos projetos do Ministério da Saúde.

## CSS Orientado a Objeto

O _**CSS Orientado a Objeto**_ parte do conceito de que o _"objeto"_ CSS é um padrão visual de repetição que pode ser abstraído de forma independente. Esse objeto pode então ser reutilizado em diferentes partes do projeto e ainda possuir a mesma aparência.

_Referência: [OOCSS: Object Oriented CSS](https://github.com/stubbornella/oocss/wiki)_

### Aplicando os princípios de SOLID

**SOLID** é um acrônimo para _**Single responsibility, Open-closed, Liskov substitution, Interface segregation**_ e _**Dependency inversion**_. Este termo foi criado por [Robert C. Martin](http://pt.wikipedia.org/wiki/Robert_Cecil_Martin) para unir alguns padrões muito utilizados no design de software, que visam facilitar a manutenção, legibilidade e vida do nosso código.

_Referência: [SOLID: Object Oriented Design](http://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29)_

1. #### Responsabilidade Única
 
  Uma classe deve **fazer apenas uma coisa, deve fazê-la bem e deve fazer somente ela**. No exemplo abaixo temos o objeto que define os padrões de um botão na nossa aplicação.

2. #### Aberto / Fechado

  Um objeto estará sempre **aberto para extensões** e **fechado para modificações**. Isso significa que quando um objeto é definido, não deverá mais ser alterado. Se em algum momento você precisar alterar seu objeto, é o caso de rever toda a estrutura do seu código. Ao invés de alterar seu objeto base, deve-se criar modificadores.

3. #### Substituição de Liskov
  
  O princípio de substituição de Liskov define que **os objetos de uma determinada hierarquia podem ser substituídos por qualquer um dos seus subtipos**. Sendo assim a noção de subtipo passa a ser determinada pela noção de substituição. 

4. #### Segregação de Interfaces

  **Classes específicas são melhores do que uma classes genéricas**. Isso faz com que nosso código tenha mais coesão e o torna fácil de dar manutenção e de ser refatorado ou alterado.

5. #### Inversão de Dependência

  1. **Módulos de alto nível não devem depender de módulos de baixo nível**. Ambos devem depender de [abstrações](#criando-abstracoes).
  2. **Abstrações não deve depender de detalhes**. Os detalhes que devem depender de abstrações.

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

<sup>_**Princípio SOLID:** [Responsabilidade Única](#responsabilidade-unica)_</sup>

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

<sup>_**Princípios SOLID:** [Aberto/Fechado](#aberto-fechado) e [Substituição de Liskov](#substituicao-de-liskov-e-segregação-de-interfaces)_</sup>

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

<sup>_**Princípios SOLID:** [Segregação de Interfaces](#segregação-de-interfaces)_</sup>

Isso nos dá a garantia que:

1. Todos `<h2>` sem classe terão a mesma aparência;
2. Todos os elementos com a classe `post-tile` terão a mesma aparência;
3. Você não vai precisar criar outra regra caso você precise que, em algum momento, `.my-object h2` tenha a mesma aparência que um `<h2>` normal.

## DRY: Don't Repeat Yourself

_**Não repita a si mesmo**_ ou também conhecido pelo acrônimo **DRY**, é um conceito que busca reduzir a duplicação de código e os problemas de manutenção resultantes. **DRY** tem como base o **Princípio da Responsabilidade Única**.

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

<sup>_**Princípio SOLID:** [Inversão de Dependência](#inversao-de-dependencia)_</sup>

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

#### TL;DR

Para alguns pode parecer um pouco feio e estranho utilizar **BEM**, mas vamos otimizar essa prática quando falarmos sobre [preprocessadores](#preprocessadores).
