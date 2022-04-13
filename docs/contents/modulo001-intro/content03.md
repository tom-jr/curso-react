# Criando um componente

Para criar um componente novo precisamos apenas criar um novo documento
que tenha extensão apropriada para retorna ***jrx***. Como estamos utilizando
typescript, ao invés de utilizar um arquivo com extensão ***ts*** utilizaremos o 
arquivo com extensão ***tsx***.

Vamos estar criando durante o curso componentes funcionais, que se trata de ser cada
component uma função que retorna um **JRX**.

Modelo de um componente simples:

~~~ javascript
export default function HelloWorld(){
    return <h1>Ola Mundo</h1>
}
~~~
Então para que esse componente seja utilizado no ***App.tsx***
basta declararmos o nome da function entre tags no retorno da function App.

~~~ javascript
function App() {
  return (
    <div className="App">
      <header className="App-header">
        <HelloWorld></HelloWorld> // componente que fora criado.
       ...
}
~~~