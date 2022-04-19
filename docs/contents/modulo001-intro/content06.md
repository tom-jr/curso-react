# State e ciclo de vida
Em um exemplo anterior aprendermos uma maneira de atualizar o a UI com o ReactDOM.render()

~~~ jsx
function tick(){
    const element = (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
    );
}
 
ReactDOM.render(element, document.getElementById('root));

setInterval(tick(),1000);
~~~

Agora aprenderemos como torna o component realmente reutilizável e encapsulado
Ele ira configurar seu próprio temporizador e se atualizar a cada segundo

Podemos começar encapsulando como o relógio parece:

~~~ jsx
function Clock(props){
    return(
        <div>
            <h1>Hello, world!</h1>
            <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
    );
}

function tick(){
    ReactDOM.render(<Clock date = {new Date()}/>, document.getElementById('root'));
}
setInterval(tick, 1000);
~~~

No entanto, falta um requisito crucial, o fato de que o Clock configura um temporizador e atualiza a UI a cada
segundo deve ser um detalhe de implementação do Clock

Idealmente queremos escrever isso uma vez e ter o Clock se atualizando:

~~~ jsx
ReactDOM.render(
    <Clock/>,
    document.getElementById('root')
);
~~~

Para implementa-lo, precisamos de um ***state*** ao component Clock.
O state do component é similar as props, mas é privado e totalmente controlado pelo componente.

## Convertendo uma função para uma classe

Voce pode converter um component de função como Clock em uma class em 5 etapas:

- Criar uma Class ES6, com o mesmo nome estendendo ***React.Component***
- Adicionar um único método vazio chamado ***render()***
- Mova o corpo da função para o método ***render()***
- Substitua props por this.props no corpo de ***render()***
- Exclua a declaração de função vazia restante


~~~ jsx
class Clock extends React.Component{
    render(){
        return(
            <div>
                <h1>Hello, world!</h1>
                <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
            </div> 
        );
    }
}
~~~

O método render sera chamado toda vez que uma atualização acontecer, mas enquanto renderizamos <Clock> no mesmo
node DOM, apenas uma única instancia de da class Clock será utilizada. Isso nos permite usar recursos adicionais,
como o estado local e os métodos de ciclo de vida.

## Adicionando Estado local a um Classe

Vamos mover a date da props para o state nos seguintes passos:

- Substituir ***this.props.date*** por ***this.state.date*** nó método ***render()***;

~~~ jsx
class Clock extends React.Component{
    render(){
        return(
             <div>
                <h1>Hello, world!</h1>
                <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
            </div> 
        );
    }
}
~~~

- Adicione um construtor na class que atribui a data inicial no **this.state**:

~~~ jsx
class Clock extends React.Component{
   constructor(props){
       super(props);
       this.state = {date: new Date()};
   }

   render(){
       return(
           <div>
                <h1>Hello, world!</h1>
                <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
            </div> 
       );
   }
}
~~~

Note como passamos  props para o construtor

~~~ jsx
constructor(props){
    super(props);
    this.state = {date: new Date()};
}
~~~

Componentes de classes devem sempre chamar o construto com **props**

- Remove a props date do elemento <Clock/>

~~~ jsx
ReactDOM.render(<Clock />, document.getElementById('root'));
~~~

O Resultado se parece com:

~~~ jsx
class Clock extends React.Component{
    constructor(props){
        super(props);
        this.state = {date: new Date()};
    }

    render(){
        return(
            <div>
                <h1>Hello, world!</h1>
                <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
            </div> 
        );
    }
}
ReactDOM.render(<Clock />, document.getElementById('root'));
~~~

## Adicionando métodos de ciclo de vida na classe

Em aplicações com muitos componente, é muito importante limpar os recursos utilizados
pelos componentes quando são destruídos

Queremos configurar um temporizado sempre que o Clock é renderizado para o DOM pela
primeira vez. Isso é chamado de "mounting" no REact

Nós também queremos limpar o temporizador sempre que o DOM produzido pelo Clock for
removido. Isso é chamado de "unmounting" no React.

Podemos declarar métodos especiais no componente de classe para executar algum código
quando um component é montado e desmontado:

~~~ jsx
class Clock extends React.Component{
    constructor(props){
        super(props);
        this.state = {date: new Date()};
    }

    componentDidMount(){}
    componentWillUnmount(){}

    render(){
        return(
        <div>
            <h1>Hello, world!</h1>
            <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
        </div>
        );
    }
}
~~~

Estes métodos são chamados de "métodos de ciclo de vida"

O método ***componentDidMount()*** é executado depois que a Saida do componente é renderizada 
no DOM. Este é um bom lugar para configurar um temporizador

~~~ jsx
componentDidMount(){
    this.timerID = setInterval(()=>{this.tick();},1000);
}
~~~

Nós salvamos o ID do temporizador em **this** ***(this.timerID).***

Enquanto o this.props é configurado pelo próprio React e this.state  tem um significado especial
, voce esta livre para adicionar campos adicionais á classe manualmente se precisar armazenar algo
que não participe do fluxo de dados (como um ID do temporizador)

Vamos destruir o temporizador no método de ciclo de vida ***componentWillUnmount()***;

~~~ jsx
componentWillUnmount(){
    clearInterval(this.timerID);
}
~~~

Finalmente podemos implantar o método **tick()** para que o Clock o execute a cada segundo.

Ele usara o ***this.setState()*** para agendar atualizações para o estado local do componente:

~~~ jsx
class Clock extends React.Component{
    constructor(props){
        super(props);
        this.state = {date: new Date()};
    }

    componentDidMount(){
        this.timerID = setInterval(()=> tick(),1000);
    }

    componentWillUnmount(){
        clearInterval(this.timerID);
    }

    tick(){
        this.setState(
            {date: new Date()};
        );
    }

    render(){
        return(
        <div>
            <h1>Hello, world!</h1>
            <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
        </div>
        );
    }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
~~~
Agora o relógio atualiza a cada seg


## Recapitulação 

Quando o Clock é passado para o ReactDOM.render(), o React chama o construtor do componente Clock.
Como Clock precisa exibir o horário atual ele inicializa this,state com um objeto incluindo a hora atual. Mais
tarde atualizaremos este state

React Chama então o método render() do component Clock. É assim que o React aprende o que deve ser exibido na tela
React em seguida atualiza o DOM para coincidir com a saída de renderização do Clock.

Quando a Saida do clock é inserida no DOM, o React chama o método do ciclo de vida componentDidMount().
Dentro dele, o componente Clock pede ao navegador para configurar um temporizador para chamar o método tick
do componente uma vez por segundo

A cada segundo o navegador chama o método tick(). Dentro dele, o componente Clock agenda uma atualização
de UI chamando setState() com um objeto contendo a hora atual. Graças à chamada setState(),
o método render() será diferente, e portanto, a saida de renderização incluirá a hora atualizada. React
atualiza o DOM de acordo

Se o componente Clock for removido do DOM, o React chama o método do ciclo de vida componentWillUnmount()
para que o temporizador seja removido.

## Utilizando State corretamente

- Não modifique o state Diretamente.
Por exemplo, isso não renderizará novamente o componente

~~~ jsx
this.state.comment = 'Hello';
~~~
Em vez disso, use o ***setState()***

~~~ jsx
this.setState({comment: 'Hello'});
~~~

O único lugar onde podemos atribuir this.state é o constructor.


- Atualizações de State Podem ser Assíncronas

O React pode agrupar várias chamadas ***setState()*** em uma única atualização para 
desempenho.

Como **this.props** e **this.state**  podem ser atualizados de forma assíncrona, voce não deve confiar
em seus valores para calcular o proximo state.

Por exemplo, esse código pode falhar ao atualizar o contador

~~~ jsx
this.setState({counter: this.state.counter + this.props.increment});
~~~

Para concerta-los use uma segunda forma de setState() que aceite uma função ao invés de 
um objeto. Essa função recebera o state anterior como o primeiro argumento e as props
no momento em que a atualização for aplicada como o segundo argumento:

~~~ jsx
this.setState((state,props)=>({counter: state.counter + props.increment}));
~~~

- Atualizações de States são mescladas

Quando chamamos ***setState()***, o React mescla o objeto que  fornecemos ao state
atual

Por exemplo: os states podem conter varias variáveis independentes:

~~~ jsx
constructor(props){
    super(props);
    this.state = {post:[],comments:[]};
}
~~~

Então podemos atualiza-los independentemente com chamadas separadas do setState();

~~~ jsx
componentDidMount(){
    fetchPosts().then((response)=>{
        this.setState({posts: response.posts});
    });

    fetchComments().the((response)=>{
        this.setState({comments: response.comments});
    });
}
~~~

O merge é superficial, então this.setState({comments}) deixa this.setState({posts}) 
intacto, mas substitui completamente this.state.comments

- Os dados fluem para baixo.
Nem components pai ou filho podem saber s um determinado component é stateful ou stateless
e não deve se importar se ele é definido por uma função ou classe

É por isso que o state geralmente é chamado de local ou encapsulado. Não é acessível a nenhum
component que não seja o que o possui e o define.

Um componente pode escolher passar seu state como props para seus children:

~~~ jsx
<FormattedDate date = {this.state.date} />
~~~

O component FormattedDate receberia o date em sues objetos e não saberia se ele veio
do state de Clock, das props, ou se foi manualmente declarado.

~~~ jsx
function FormattedDate(){
    return (
        <h2> It is {props.date.toLocaleTimeString()}. </h2>
    );
}
~~~
Isso é comumente chamado de fluxo ***top-down*** ou ***unidirecional***. Qualquer state 
é sempre de propriedade de algum componente especifico, e qualquer dado ou interface do usuário
derivado desse state só pode afetar os componentes abaixo deles na árvore.

Se imaginar uma árvore de componentes como uma cascata de props, o state de cada componente é como
uma fonte de água adicional que une em um ponto arbitrário mas também flui para baixo.

Para mostrar que todos os components estão isolados, podemos criar um componente App que renderiza
três <Clocks/>s:

~~~ jsx
function App(){
    return(
    <div>
        <Clocks/>
        <Clocks/>
        <Clocks/>
    </div>
    );
}

ReactDOM.render(
<App/>,
document.getElementById('root')
);
~~~

Cada Clock renderiza seu temporizador e atualiza de forma independente.

Nos apps React , se um component é stateful ou stateless é considerado um detalhe de implementação
do componente que pode mudar com o tempo. Voce pode usar components sem state dentro de componente
com state e vice-versa.