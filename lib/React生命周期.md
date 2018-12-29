### react生命周期

React16废弃的三个生命周期函数

`componentWillMount
componentWillReceiveProps
componentWillUpdate`
    
取而代之的是两个新的生命周期函数

`getDerivedStateFromProps
getSnapshotBeforeUpdate`

#### 原有的生命周期:

	*constructor(初始化state，绑定this，可以把首屏数据请求放到这里)
	*componentWillMount(数据不放在这里请求，是因为请求异步，可能数据没回来就执行了render)
	----[render]----
	*componentDidMount(这时候dom什么的已经生成)
	<if>props改变
	*componentWillReceiveProps
	</if>
	*shouldComponentUpdate ---> *componentWillUpdate
	----[render]----
	*componentDidUpdate
	*componentWillUnmount


#### 现在的生命周期:

	*constructor(初始化state，绑定this，可以把首屏数据请求放到这里)
	*getDerivedStateFromProps(替换原有的)
	----[render]----
	*componentDidMount(这时候dom什么的已经生成)
	<if>props改变
	*getDerivedStateFromProps
	</if>
	*shouldComponentUpdate
	----[render]----
	*getSnapshotBeforeUpdate
	(update dom)
	*componentDidUpdate
	*componentWillUnmount


另外 prevProps 那个还有三个原因:

- 一是初次渲染的时候，prevProps 是 undefined/null，这样你得做一次 empty check，太麻烦了。。而且与已有的 cDU 的 prevProps 表现形式不一致。
- 二是将状态变化和昂贵操作区分开，更加便于 render 和 commit 阶段操作或者说优化。
- 三是没有 prevProps，react 可以不保留所有组件的 prevProps，让它们被 GC 掉。
