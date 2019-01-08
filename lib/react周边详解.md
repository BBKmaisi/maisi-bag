### 前言

这不是一篇深度解析React工作原理的文章，而是帮你完善你可能忽略的React的知识点。

生活中我们频繁使用电脑、冰箱，但不一定需要知道电脑和冰箱是如何工作的。但是作为一个技术人员，从jQuery到React，你不仅需要熟悉使用他们，还需要了解他们的工作原理，以体现你的精通。然而如何体现你对它们的了解，无非是从大量的相关细节问题。相信绝大部分公司的技术栈都已经切换到了React，你也知道面试官最喜欢的面试套路是从你简历上的“知识点”顺藤摸瓜式的追问你，所以React的相关问题无法避免，而同时你也要问自己是否真的了解它，还只是会使用它而已。

所以在继续阅读之前，请尝试回答以下有关React的问题（其中有九成是我在面试中遇到的，另外一成是我自己认为有必要了解的），其中粗体字部分是我认为重点需要掌握的知识点，不仅是在面试过程中，在实际代码过程中需要运用到的：

### React解决了什么问题
如何设计一个好的组件？
组件的Render函数在何时被调用？
调用时DOM就一定会被更新吗？
组件的生命周期有哪些？
当某些第三方类库想对DOM初始化，或者进行远程数据加载时，应该在哪个周期中完成？
在哪些声明周期中可以修改组件的state？
不同父节点的组件需要对彼此的状态进行改变时应该实现？
如何设计出一个好的Flux架构
如何设计出一个好的React组件
如何进行优化？
组件中的key属性有什么用？
Component 与 Element 与 Instance 的区别
如果你使用过Redux与Vuex的话，聊聊他们的区别与你的心得
Vue.js 的双向绑定是如何实现的？
Webpack如何打包输出多个文件？
webpack打包时如何工作的？
如何解决循环引用的问题
在什么情况下需要打包输出多个文件？
loader和plugin的差别
你觉得使用过什么高级技巧吗？
（开放问题）React的生态你使用过哪些类库
如果以上问题都难不倒你，那么恭喜你，你的React技能树已满。如果有些问题不确定或者不了解的也没有关系，请继续阅读这篇文章。我将一一对这些问题做解答。

其中有些问题的答案比较长，可能会和一篇文章相当。所以关于React可能会拆成上中下三集来说。

重要的不是把这些问题的答案背下来，而应该重点去理解他们。如果你之前没有React的开发经验，可能对于其中的一些道理没有那么深的感触。所以建议边做，边学，边理解。

但是请注意有些问题可能并没有官方答案，是我个人通过经验得出的，所以仅作参考喔。如果有补充也可以留言给我。

### React解决了什么问题
这是基础但是又很重要的问题，如果只是回答公司要求或者赶上潮流，未免就显得格局太小和好奇心太弱。

这个问题的答案在我一年前写的一篇文章里已经有很详细的回答了：写给前端看的架构文章(1)：MVC VS Flux。重点分析了MVC与Flux的差异，MVC的弱势以及Flux弥补的不足（这么好的一篇文章竟然没人关注，伤心）。

总之一句话：MVC架构的双向绑定以及一对多的关系容易造成连级/联动（Cascading）修改，对于代码的调试和维护都成问题。

### 如何设计一个好的组件
这道题目中的“组件”不仅限于React组件，广义上看，前端代码模块，独立类库甚至函数在编写时都应该遵循良好的规则。

怎样的组件设计算的上“好”，要从几个层次来看这个问题。我们从宏观到微观依次来看。

首先你要知道组件的出现是为了解决怎样的问题——是为了更好的复用。然而怎样才能能其他的使用者更好的复用你的组件？API够烂肯定不行，这样的话其他人就没法调用；兼容性差也不行，因为同一个系统中可能存在不同版本React编写的组件，甚至还可能和Vue组件发生交互；内部实现差了也不行，这样的话你的下一任接替你职位的人修改起来会非常麻烦，结果不外乎重写。

### 高内聚，低耦合
我绝对相信这六个字你已经听到耳朵起茧。但我还是要重申，无论是什么语言编程，无论是前端还是后端，无论多耳熟能详的架构（Microervices），无论是多具体的设计原则（后面会说的SOLID），本质上都是对这个原则的实践。所以我们的设计也不例外。

顾名思义，在做组件设计时，甚至编写函数时，应该把相同功能的部分放在一起，而把不相干的部分尽可能的撇开关系。如果你想去反向验证你的设计是否符合这个原则的话，可以尝试去修改这个模块的一个功能，看看到底是否会牵连其他模块的修改；或者当你想复用这个组件时，是否会引入其他无关的组件。

接下来我们从SOLID原则看看对这六个字的具体实践。

### S.O.L.I.D
SOLID 原则是面向对象设计中的原则，但就我经验而言，其中的这些也同样适用于组件设计。例如单一职责(Single responsibility principle)，React组件设计推崇的是“组合”，而非“继承”。例如你的页面需要一个表单组件，表单中需要有输入框，按钮，列表，单选框等。那么在开发中你不应该只开发一（整）个表单组件（<Form>），而是应该开发若干个单一功能的组件，比如输入框<Input>、提交按钮<Submit>、单选框<Checkbox>等，最后再将它们组合起来。这其中的重点是每个组件仅做一件事。

不仅仅是编写组件，哪怕仅仅是编写一个简单的函数也是应该如此，例如你需要一个函数异步请求数据并返回JSON数据格式，那么你应该拆分为两个函数，一个复杂数据请求，另一个负责数据转化。你可能会好奇为什么一个简单的JSON.parse也拆分出来，因为将来需要会变动，你可能不仅仅需要JSON.parse，还需要转义，需要转化为proto buffer数据格式。而拆分之后如果再面临修改的话，就不会影响到数据请求部分的代码。

上面这个例子也同样适用于开放/封闭(Open/closed principle)原则。开放/封闭强调的是对修改封闭（禁止修改内部代码），对拓展开放（允许你拓展功能）。因为修改意味着风险，可能会影响到不用修改的代码， 同时意味着暴露细节。你一定纳闷如果不允许修改代码的话如何拓展功能呢，在传统的面向对象编程中，这样的需求是通过继承和接口机制来实现的。在React中我们使用官方推荐的 Higher-Order Components 的模式去实现。这个在后面会详细叙述。

接口隔离（Interface segregation principle）这个就放之四海而皆准了。第三方类库或者模块都避免不了对外提供调用接口，比如对于jQuery来说$是选择器，css用于设置样式，animate负责动画，你不希望把这三个接口都合并成一个叫做together吧，虽然实现起来没有问题，但是对于你将来维护这个类库，以及使用者调用类库，以及调用者的接替者阅读代码（因为他要区分不同上下文中调用这个接口究竟是用来干嘛的），都是不小的困难。

最后一条依赖反转（Inversion Of Control）原则。这条原则听上去有点拗口，不过它有另外一个名字：Hollywood Principle，虽然我也不理解为什么会有这个别名。这条原则是意思是，当你在为一个框架编写模块或者组件时，你只需要负责实现接口，并且到注册到框架里即可，然后等待框架来调用你，所以它的另另一个别名是 “Don't call us, we'll call you”。

这么说你可能没什么太大感觉，也不明白和“依赖”和“反转”有什么关系，说到底其实是一个控制权的问题。这里举一个图书Building Microservices: Designing Fine-Grained Systems中的例子。常规情况下当你在用express编写一个server时，代码是这样的：

	const app = express();
	module.exports = function (app) {
		app.get('/newRoute', function(req, res) {...})
	};

这意味着你正在编写的这个模块负责了/newRoute这个路径的处理，这个模块在掌握着主动权。

而用依赖反转的写法是：

	module.exports = function plugin() {
		return {
			method: 'get',
			route: '/newRoute',
			handler: function(req, res) {...}
		}
	}

意味着你把控制权交给了引用这个模块的框架，这样的对比就体现了控制权的反转。

其实前端编程中常常用到这个原则，注入依赖就是对这个思维的体现。比如requireJS和Angular1.0中对依赖模块的引用使用的都是注入依赖的思想。

至于里氏替换原则在前端是真的用不上了。

Higher-Order Components 和 Container Components 和 Stateless Components
上面我们了解完总体的设计思想之后，细化的来看针对React组件还有哪些具体的设计模式。

Higher-Order Components

让我们考虑一下这的业务场景：假设你现在有一个基础款的<div>组件，允许自定义属性和点击事件，以及添加容器内的文字。接下来你需要另一个相似的高级的<div>组件，包含基础款的所有功能，并且还有额外的功能，例如有额外的标题和图片。你要怎么实现这个功能？

程序员思维告诉我们新的高级组件应该继承自基础款的组件，而不是重写。但是如何实现呢？这就要使用我们的 Higher-Order Components 模式（以下简称HOC）了。

这个模式很简单，我们定义一个工厂函数名为enchce(wrapperComponent, config)，支持传入旧的基础款的组件，而这个函数的功能则是在采用类似外观模式在对旧的组件做一次新的封装，引入新的功能。

举一个实际的例子。假设我们的基础款的组件长这个样子：

	class BaseComponent extends React.Component {
	  render() {
	    return (
	      <div onClick={this.props.onClick}
	          style={this.props.style}>

	        <h1>{ this.props.title }</h1>
	        <p>{ this.props.content }</p>
	      </div>
	    );
	  }
	}

而我们希望新的组件稍稍的改变事，click事件的回调函数不使用传入值，而使用的是更强大的自定义函数，那么我们的enhance函数是这样：

	const enhance = (WrappedComponent) => {
	  return class ClickLogger extends React.Component {
	    constructor(props) {
	      super(props);
	      this.onClick = this.onClick.bind(this);
	    }

	    onClick(e) {
	      console.log(e)
	    }

	    render() {
	      const { title, content } = this.props;
	      return (
	        <div>
	          <WrappedComponent {...this.props} onClick={this.onClick} />
	        </div>
	      );
	    }
	  }
	}

	const LoggableComponent = enhance(BaseComponent);

当然在这个例子中你可以直接使用class ClickLogger extends BaseComponent，但是如果存在多次复用，或者类似于Vue中的Mixins情况，那么HOC模式就很重要了。注意HOC模式的重点是：不要修改原有的组件

Facebook有整篇的官方的博文来介绍这个模式，关于在什么场景下使用这个模式，高级用法以及需要注意的事项。这里就不赘述了

Container Components

先来看看这样这样一个组件，你认为有什么问题：

	class CommentList extends React.Component {
	  this.state = { comments: [] };

	  componentDidMount() {
	    fetchSomeComments(comments =>
	      this.setState({ comments: comments }));
	  }
	  render() {
	    return (
	      <ul>
	        {this.state.comments.map(c => (
	          <li>{c.body}—{c.author}</li>
	        ))}
	      </ul>
	    );
	  }
	}

这个组件的问题在于数据抓取和数据展示同放在同一个组件和代码块中。这样一来无论是数据抓取部分逻辑还是数据展示逻辑都无法复用。于是我们把数据逻辑部分分离出来成为独立的组件，这类组件就是Container Components，而展现部分组件则是Presentational Components。根据这个思路，上面这个例子可以划分为两个组件：

Presentational Components:

	const CommentList = props =>
	  <ul>
	    {props.comments.map(c => (
	      <li>{c.body}—{c.author}</li>
	    ))}
	  </ul>
	Container Components:

	class CommentListContainer extends React.Component {
	  state = { comments: [] };
	  componentDidMount() {
	    fetchSomeComments(comments =>
	      this.setState({ comments: comments }));
	  }
	  render() {
	    return <CommentList comments={this.state.comments} />;
	  }
	}
	
当然你还可以将Container Components封装为类似于HOC的工厂函数

Stateless Components

上一小节的 Presentational Components 就近似于 Stateless Components 的概念，也就是自己不维护状态而是依靠外部传入的状态。关于 Stateless Components 的具体描述在关于 Flux 的设计章节会有详细叙述

代码层面
更细节的问题就是代码层面了。然而如何写好代码这件事对于React来说并没有什么特殊之处。如果在面试的过程中需要补充这方面的内容的话，请强调你的代码是足够符合规范的。以及在设计代码的过程中你会根据业务场景灵活的运用设计模式组织代码。又例如对于组织样式代码，也会运用BEM规则。

总之代码需要可读性强，复用性高，可维护性好。以后如果还有什么想到的再继续补充

组件的Render函数在何时被调用
如果单纯、侠义的回答这个问题，毫无疑问Render是在组件 state 发生改变时候被调用。无论是通过 setState 函数改变组件自身的state值，还是继承的 props 属性发生改变都会造成render函数被调用，即使改变的前后值都是一样的。

如果你想手动决定是否调用也没有问题，如果你还记得React的生命周期的话，一定记得有一个boolean shouldComponentUpdate(object nextProps, object nextState)生命周期函数，这个函数的返回值决定了Render是否被调用，默认都返回true，即允许render被调用。如果你对自己的判断能力有自信，你可以重写这个函数，根据参数判断是否应该调用 Render 函数。这也是React其中的一个优化点。

但退一步说，即使render函数被调用了，DOM就一定被更新了？这要看更新的是哪一类DOM了。

React组件中存在两类DOM，一类是众所周知的Virtual DOM，相信大家也耳熟能详了；另一类就是浏览器中的真实DOM（Real DOM/Native DOM）。React的Render函数被调用之后，React立即根据props或者state重新创建了一颗Virtual DOM Tree，虽然每一次调用时都重新创建，但因为在内存中创建DOM树其实是非常快且不影响性能的，所以这一步的开销并不大。而Virtual DOM的更新并不意味这Real DOM的更新，接下来的事情也是大家知道的，React采用算法将Virtual DOM和Real DOM进行对比，找出需要更新的最小步骤，此时Real DOM才可能发生修改。

所以正确答案是，每一次的state更改都会使得render函数被调用，但页面的DOM不一定会发生修改

组件的生命周期有哪些？
这一道问题其实是有标准答案的，具体可以参考Facebook官方的这篇文章React.Component，我在这里强调一下重点。

组件的声明周期有三种阶段，一种是初始化阶段（Mounting），一种是更新阶段（Updating）最后一种是析构阶段（Unmounting）。而这两个阶段的声明周期函数都是相似且有一一对应的关系的。

组件的初始化阶段的声明周期函数以及重点用法如下：

constructor(): 用于绑定事件以及初始化state（可以通过"fork"props的方式给state赋值）
componentWillMount(): 只会在服务端渲染时被调用，你可以在这里同步操作state
render(): 这个函数是用来渲染DOM没有错。但它只能用来渲染DOM，请保证它的纯粹性。如果有操作DOM或者和浏览器打交道的一系列操作，请在下一步骤componentDidMount中进行
componentDidMount(): 如果你有第三方操作DOM的类库需要初始化（类似于jQuery，Bootstrap的一些组件）操作DOM、或者请求异步数据，都应该放在这个步骤中做
组件更新阶段：

componentWillReceiveProps(nextProps): 在这里你可以拿到即将改变的状态，可以在这一步中通过setState方法设置state
shouldComponentUpdate(nextProps, nextState): 这一步骤非常重要，它的返回值决定了接下来的生命周期函数是否会被调用，默认返回true，即都会被调用；你也可以重写这个函数使它返回false。
componentWillUpdate(): 我也不知道这个声明周期函数的意义在哪里，在这个函数内你不能调用setState改变组件状态
render()
componentDidUpdate(): 和componentDidMount类似，在这里执行DOM操作以及发起网络请求
组件析构阶段：

componentWillUnmount(): 主要用于执行一些清理工作，比如取消网络请求，清楚多余的DOM元素等
认识了以上所有的生命周期之后，请不假思索的回答，有哪些生命周期是允许设置state的？

Hollywood Principle
Higher-Order Components in React
Higher-Order Components
Container Components
Leveling Up With React: Container Components
ReactJS - Does render get called any time “setState” is called?
How does React decide to re-render a component?
Virtual DOM in ReactJS
Reconciliation
React.Component
Understanding the React Component Lifecycle
