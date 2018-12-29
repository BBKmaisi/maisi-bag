### setState是异步吗？

#### 1. 合成事件中的setState

所以当你在increment中调用 setState 之后去 console.log 的时候，是属于try代码块中的执行，但是由于是合成事件，try 代码块执行完 state 并没有更新，所以你输入的结果是更新前的 state 值，这就导致了所谓的"异步"，但是当你的 try 代码块执行完的时候（也就是你的increment合成事件），这个时候会去执行 finally 里的代码，在 finally 中执行了 performSyncWork 方法，这个时候才会去更新你的 state并且渲染到UI上

	try {
		return fn(a, b);
	} finally {
		isBatchingInteractiveUpdates = previousIsBatchingInteractiveUpdates;
		isBatchingUpdates = previousIsBatchingUpdates;
		if (!isBatchingUpdates && !isRendering) {
		  	performSyncWork();
		}
	}

 #### 2. 生命周期函数中的setState

 其实还是和合成事件一样，当componentDidmount执行的时候，react内部并没有更新，执行完componentDidmount后才去commitUpdateQueue更新。这就导致你在componentDidmount中setState完去console.log拿的结果还是更新前的值。

 #### 3. 原生事件中的setState

	class App extends Component {
		state = { val: 0 }
		changeValue = () => {
	    	this.setState({ val: this.state.val + 1 })
	    	console.log(this.state.val) // 输出的是更新后的值 --> 1
	  	}
		componentDidMount() {
		    document.body.addEventListener('click', this.changeValue, false)
		}
		render() {
		    return (
			    <div>
			        {`Counter is: ${this.state.val}`}
			    </div>
		    )
		}
	}

原生事件是指非react合成事件，原生自带的事件监听 addEventListener ，或者也可以用原生js、jq直接 document.querySelector().onclick 这种绑定事件的形式都属于原生事件。

#### 4. setTimeout中的setState

	class App extends Component {
	  state = { val: 0 }
	 componentDidMount() {
	    setTimeout(_ => {
	      this.setState({ val: this.state.val + 1 })
	      console.log(this.state.val) // 输出更新后的值 --> 1
	    }, 0)
	 }
	  render() {
	    return (
	      <div>
	        {`Counter is: ${this.state.val}`}
	      </div>
	    )
	  }
	}

在 setTimeout 中去 setState 并不算是一个单独的场景，它是随着你外层去决定的，因为你可以在合成事件中 setTimeout，可以在钩子函数中 setTimeout，也可以在原生事件setTimeout，但是不管是哪个场景下，基于event loop的模型下，setTimeout 中里去 setState 总能拿到最新的state值

举个栗子，比如之前的合成事件，由于 setTimeout(_ => { this.setState()}, 0)是在 try 代码块中,当你 try 代码块执行到setTimeout的时候，把它丢到列队里，并没有去执行，而是先执行的 finally 代码块，等 finally 执行完了，isBatchingUpdates又变为了 false，导致最后去执行队列里的 setState 时候， requestWork 走的是和原生事件一样的 expirationTime === Sync if分支，所以表现就会和原生事件一样，可以同步拿到最新的state值。

#### 5. setState中的批量更新

	class App extends Component {
	  state = { val: 0 }
	  batchUpdates = () => {
	    this.setState({ val: this.state.val + 1 })
	    this.setState({ val: this.state.val + 1 })
	    this.setState({ val: this.state.val + 1 })
	 }
	  render() {
	    return (
	      <div onClick={this.batchUpdates}>
	        {`Counter is ${this.state.val}`} // 1
	      </div>
	    )
	  }
	}

上面的结果最终是1，在setState的时候react内部会创建一个updateQueue，通过firstUpdate、lastUpdate、lastUpdate.next去维护一个更新的队列，在最终的performWork中，相同的key会被覆盖，只会对最后一次的setState进行更新


#### 总结 :

- setState 只在合成事件和钩子函数中是“异步”的，在原生事件和setTimeout 中都是同步的。
- setState 的“异步”并不是说内部由异步代码实现，其实本身执行的过程和代码都是同步的，只是合成事件和钩子函数的调用顺序在更新之前，导致在合成事件和钩子函数中没法立马拿到更新后的值，形成了所谓的“异步”，当然可以通过第二个参数 setState(partialState, callback) 中的callback拿到更新后的结果。
- setState 的批量更新优化也是建立在“异步”（合成事件、钩子函数）之上的，在原生事件和setTimeout 中不会批量更新，在“异步”中如果对同一个值进行多次setState，setState的批量更新策略会对其进行覆盖，取最后一次的执行，如果是同时setState多个不同的值，在更新时会对其进行合并批量更新。
