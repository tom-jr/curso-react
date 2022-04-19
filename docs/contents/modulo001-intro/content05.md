# Componentes e Props

Components permitem que dividimos a UI em partes independentes, reutilizáveis e pensar em cada
parte isoladamente.

Conceitualmente, componentes são como **funções JS**. Eles aceitam entradas arbitrarias (chamadas "props") e retornam
elementos React que descrevem o que deve aparecer na tela.

## Componentes de Função e classe

A maneira mais simples de definir um component é escrever uma função JS

~~~ jsx
function Welcome(props){
return <h1>Olá, {props.name}</h1>
}
~~~

Esta função é um component React valido por que aceita um único argumento de objeto ***props*** e
retorna um elemento React. Chamamos esses componentes de component Function. Por que literalmente são 
funções JS


É possível utilizar uma class ES6 para definir um component:


~~~ jsx
class Welcome extends React.Component{

    reder(){
        return <h1>Ola, {this.props.name}</h1>
    }
}
~~~

Os dois components são equivalentes do ponto de vista do React.


## Renderizando um component

Anteriormente, nos encontramos apenas elementos React que representam tags do DOM:

~~~ jsx
const element = </div>
~~~

No entanto elementos também podem representar components definidos pelo usuário

~~~ jsx
cont element = (<Welcome name = "Sara"/>);
~~~

Quando o React ver um elemento representando um componente criado pelo usuário, ele
passa atributos JSX e components filhos para esse componente como um único objeto. chamamos 
esse objeto de ***props***
ex:

~~~ jsx
function Welcome(props){
    return (<h1>Olá, {props.name}</h1>);
}

const element = (<Welcome name="Sara"/>);
ReactDOM.render(element, document.getElementById('root'));
~~~

**Importante**: Sempre criar component React com Capital letter, o React sabe diferencia
tags DOM de ReactComponents através disto.

## Compondo componentes

Components podem se referir a outros componentes em suas saídas. Isso nos permite usar a mesma abstração de
componente para qualquer nível de detalhe. Um botão, um formulário, uma caixa de dialogo, uma tela: em apps
React, todos esses são normalmente expressos como componentes.

Por exemplo, nós podemos criar um componente App que renderiza Welcome muitas vezes:

~~~ jsx
function Welcome(props){
    return (<h1>Ola, {props.name}</h1>);
}

function App(){
    return(
        <div>
        <Welcome name = "Samantha"/>
        <Welcome name = "Cynthia"/>
        <Welcome name = "Joyce"/>
        </div>
    );
}

ReactDOM.render(<App/>,document.getElementById('root'));
~~~

## Extraindo Componentes

Não tenha medo de dividir componentes em components menores.

Por exemplo, considere esse componente Comment:

~~~ jsx
function Comment(props){
    return(
        <div className = "Comment">
            <div className ="UserInfo">
                <img className = "Avatar"
                src = {props.author.avatarUrl}
                alt = {props.author.name}
                />
                
                <div className = "UserInfo-name">
                 {props.author.name}
                </div>
            </div>

            <div className = "Comment-text">
                {props.text}
            </div>

            <div className = "Comment-date">
                {formatDate(props.date)}
            </div>
        </div>
        </div>
    );
}
~~~

A propriedade props pode receber um objeto que contenha outros objetos:

~~~ jsx
const comment = {
  date: new Date(),
  text: 'I hope you enjoy learning React!',
  author: {
    name: 'Hello Kitty',
    avatarUrl: 'http://placekitten.com/g/64/64'
  }
~~~

Este componente Comment pode ser divido em partes menores para diminuir seu 
acoplamento. O primeiro que podemos fazer isso é com o Avatar, onde criaremos um componente
para definir o avatar(img)

~~~ jsx
function Avatar(props){
    return (
        <img ClassName = "Avatar"
         src = {props.user.avatarUrl}
         alt ={props.user.name}
         />
    );
}
~~~

Deixando o component Comment assim:

~~~ jsx
function Comment(props){
    return (
        <div className="Comment">

            <div className="UserInfo">
                <Avatar user={props.author} />
                <div className="UserInfo-name">
                    {props.author.name}
                </div>
            </div>

            <div className="Comment-text">
                {props.text}
            </div>

            <div className="Comment-date">
                {formatDate(props.date)}
            </div>
        </div>
    );
}
~~~


## Props são somente leituras

Independente se declaramos um componente como uma class ou function, ele
nunca deve modificar seus próprios props.

considerando a function **sum**:

~~~ jsx
function sum(a, b){
    return a + b;
}
~~~

Tais funções são chamadas de puras, pois não tenta alterar suas entradas e sempre
retornam o mesmo resultado par as mesmas entradas.

Em contraste essa função sera impura devido alterar sua própria entrada

~~~ jsx
function withdraw(account, amount) {
  account.total -= amount;
}
~~~

    Todos os componentes React tem que agir como funções puras em relação ao seus props.

As UIs de aplicativos são dinâmicas e mudam com o tempo. Mas com os **states** podemos permitir
aos componentes React alterar sua Saida ao longo do tempo em resposta a ações do usuário, resposta de redes
e quaisquer outras coisas, sem violar essa regra.