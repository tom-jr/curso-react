# Manipulando eventos

Manipular eventos em elementos React é muito semelhante a  manipular eventos em
elementos DOM. Existem algumas diferenças de sintaxe.

- Eventos em React são nomeados usando CamelCase
- Com JSX podemos passar uma função como manipulador de eventos ao invés de um texto

pro exemplo com HTML:

~~~ html
<button onclick = "activateLasers()">
    Ativar lasers 
</button>
~~~

É ligeiramente diferente com React

~~~ jsx
<button onClick = {activateLasers}>
    Ativar lasers 
</button>

~~~

Outra diferença é que não se pode retornar **false** para evitar comportamento padrão no React. 
Devemos chamar **preventDefault** explicitamente. Por exemplo, com o HTML simples, para evitar
comportamento padrão de formulário de evento podemos escrever:

~~~ html
<form onsubmit = "console.log('Voce clicou em enviar'); return false">
<button type  = "submit">Enviar</button>
</form>
~~~

No React seria da seguinte forma:

~~~ jsx
function Form(){
    function handleSubmit(e){
        e.preventDefault();
        console.log('Voce clicou em enviar');
    }

    return(
        <form onSubmit={handleSubmit} >
            <button type = "submit" > Enviar </button>
        </form>
    );
}
~~~

Aqui 'e' é um synthetic event. O React define esses eventos sintéticos de acordo com  a especificação
W3C. Então, não precisamos nos preocupar com a compatibilidade entre navegadores. Os eventos React não 
funcionam da mesma forma que os eventos nativos.

Ao usar o React, geralmente não precisa chamar **addEventListener** para adicionar ouvinte a um elemento no dom
Depois que ele é criado. Ao invés disso, voce pode apenas definir um ouvinte quando o elemento é inicialmente
renderizado.

 Quando voce define um componente usando uma **classe ES6**, um padrão comum é que um manipulador de eventos 
seja um método na classe:

~~~ jsx
class Toggle extends React.Component {
    constructor(props){
        super(props);
        this.state = {isToggleOn: true};

        this.handleClick = this.handleClick.bind(this);
    }

    handleClick(){
    this.setState((prevState)=>{
        isToggleOn: !isToggleOn
    });
}

render(){
    return(
        <button onClick={this.handleClick}>
            {this.state.isToggleOn?'ON':'OFF'}
        </button>
    );
}
}

ReactDOM.render(<Toggle/>, document.getElementById('root'))
~~~

Precisamos ter cuidado com o significado do **this** nos callbacks do JSX. Em JS, os métodos de class
não são vinculados por padrão. Se esquecemos de fazer o bind de this.handleClick e passa-lo para
um onClick, o this será undefined quando a função for realmente chamada.

 Este não é um comportamento especifico do React. É uma parte de como funcionam as funções em JS. Geral-
mente se voce referir a um método sem **()** depois dele, como
onClick = {this.handleClick}, voce deve fazer o bind manualmente desse método

Se ficar chamando o Bind incomodar, há duas maneiras de contornar isso. Se voce estiver usando a sintaxe
experimental do campo de classes publica, podemos usar campos de classe para vincular callbacks corretamente


## Passando Argumentos para manipuladores de Evento

Dentro de uma estrutura de repetição, é comum querer passar um parâmetro extra para um manipulador de 
evento. Por exemplo, se id é ID de identificação da linha, qualquer um dos dois a seguir funcionará:

~~~ jsx
<button onClick={(e) => this.deleteRow(id, e)}>Deletar linha</button>
<button onClick={this.deleteRow.bind(this, id)}>Deletar linha</button>
~~~

As duas linhas acima são equivalentes e usam arrow functions e Function.prototype.bind respectivamente.

Em ambos os casos, o argumento e representando o evento do React será passado como segundo argumento após o ID. Com uma arrow function, nós temos que passá-lo explicitamente. Mas com o bind outros argumentos adicionais serão automaticamente encaminhados.