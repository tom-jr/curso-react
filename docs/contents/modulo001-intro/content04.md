# Introdução JSX

~~~ javascript
cont element =  <h1>Hello, World</h1>;
~~~

Essa sintaxe é chamada de JSX. JSX pode lembrar uma linguagem de template, mas que vem com todo 
o poder do JS

JSX produz "elementos" do React.

## Por que JSX?

O React não reque o uso JSX. Porem a maioria das pessoas acha pratico como uma ajuda visual quando
se esta trabalhando com uma UI dentro do código em JS. Permitindo ao React mostrar mensagens de erro mais uteis


## Incorporando Expressões em JSX

No exemplo, declaramos uma variável e usamos a mesma dentro do JSX a envolvendo em chaves

~~~ javascript
const name = 'Joshua Perez';
cont element = <h1>Hello, {name}</h1>

ReactDOM.render(element, document.getElementById('root'));
~~~

É possivel inserir qualquer expressão JS valida dentro das chaves em JSX
ex:
2+2, user.firstName, formatName(user).


~~~ javascript
function formatName(user){
    return `${user.firstName} ${user.lastName}`
}

cont user = {
    firstName: 'Brolly',
    lastName: 'Strong'
};

const element = (
    <h1>
    Hello, {formatName(user)}!
    </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
~~~

    Separamos o JSX em múltiplas linhas para melhorar a legibilidade. Mesmo que não seja obrigatório, quando fizer isso, também recomendamos colocar dentro de parênteses para evitar as armadilhas da inserção automática de ponto-e-vírgula.


## JSX também é uma expressão

Depois da compilação, as expressões em JSX se transformam em chamadas normais de funções que retornam objetos Javascript.
Isto significa que podemos utilizar JSX dentro de condições if e laços for, atribui-las a variáveis, aceita-lo como 
argumentos e retorna-los de funções:

~~~ javascript
function getGreeting(user){
    if(user){
        return (<h1>Hello, {formatName(user)}!</h1>);
    }

    return (<h1>Hello, Stranger.</h1>);
}

ReactDOM.render(
  element,
  document.getElementById('root')
);
~~~

# Especificando Atributos com JSX

Podemos utilizar aspas para especificar strings literais como atributos:

~~~ javascript
const element = <a href = "https://www.reactjs.org"></a>
~~~

Também é possível utilizar chaves para incorporar uma expressão JavaScript em um atributo:


~~~ javascript
const element = <img href = {user.UrlAvatar}></img>
~~~
Não envolva chaves com aspas quando estiver incorporando uma expressão JS em um atributo. 
Devemos usar aspas(valores em strings) ou chaves(expressões), mas não ambos no mesmo atributo

    O ReactDOM usa camelCase como conversão para nomes de propriedades ao invés dos nomes de atributos do HTML


## Renderizando Elementos

Elementos são os menores blocos de construção de aplicativos React
Um elemento descreve o que voce quer ver na tela:

~~~ javascript
const element = <h1>Hello World</h1>
~~~

Diferente dos Elementos DOM do navegador, elementos React são Objetos simples e utilizam menos 
recursos. O React DOM é responsável por atualizar o DOM para exibir os elementos React.

    Elementos compões componentes

## Renderizando um elemento no DOM
Suponhamos que existe uma div em algum lugar do seu código HTML

~~~ html
<div id = "root"></div>
~~~

Nos chamamos de node root do DOM por que tudo dentro dela sera gerenciado pelo ReactDOM.

Aplicações geradas apenas com React geralmente tem apenas um único node root no DOM. Se 
desejar integrar o React a uma aplicação existente, voce poder ter quantas nodeRoot precisar.

Para renderizar um elemento React em um nodeRoot, passe ambos para ReactDOM,render();:

~~~ jsx
const element = (<h1>Hello World</h1>);
ReactDOM.render(element.document.getElementById('root'));
~~~

## Atualizando Elemento renderizado

Elementos React são Imutáveis. Uma vez criados, voce não pode alterar seus elementos filhos ou atributos

Com o que aprendemos até agora a única forma de atualizara interface pe criar um novo elemento e passa-lo para
***ReactDOM.render()*** 

Exemplo de um relógio:

~~~ jsx
function tick(){
    const element = (
        <div>
        <h1>Hello, World!</h1>
        <h2>It is {new Date().toLocaleTimeString()}.</h2>
        </div>
    );
    ReactDOM.render(element,document.getElementById('root'));
}
setInterval(tick,1000);
~~~

## React somente Atualiza o necessário

O React compara o elemento novo e sues filhos com os anteriores e somente
aplica as modificações necessárias no DOM para leva-lo ao estado desejado.