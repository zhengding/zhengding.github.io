---
title: React渲染机制解析
date: 2019-04-01 15:58:23
tags:
- React
categories: 前端
---

### React渲染过程

我们都知道使用React可以使得网页的性能有很大的提高，本文具体探究它是通过什么样的渲染机制做到的。

在页面一开始打开的时候，React会调用render函数构建一棵Dom树，在state/props发生改变的时候，render函数会被再次调用渲染出另外一棵树，接着，React会用对两棵树进行对比，找到需要更新的地方批量改动。

### Diff 算法

这个过程中，比较两棵Dom tree高效找出需要更新的地方是很重要的。React基于两个假设：

- 两个相同的组件产生类似的DOM结构，不同组件产生不同DOM结构
- 对于同一层次的一组子节点，它们可以通过唯一的id区分

发明了一种叫Diff的算法，它极大的优化了这个比较的过程，将算法复杂度从O(n^3)降低到O(n)。

同时，基于第一点假设，我们可以推论出，Diff算法只会对同层的节点进行比较。如图，它只会对颜色相同的节点进行比较。

![img](React渲染机制解析/squares.svg)

也就是说如果父节点不同，React将不会在去对比子节点。因为不同的组件DOM结构会不相同，所以就没有必要在去对比子节点了。这也提高了对比的效率。

下面，我们具体看下Diff算法是怎么做的，这里分为两种情况考虑

- 节点类型不同
- 节点类型相同，但是属性不同

### 不同节点类型

对于不同的节点类型，react会基于第一条假设，直接删去旧的节点，新建一个新的节点。

比如：

```
<A>
  <C/>
</A>
// 由shape1到shape2
<B>
  <C/>
</B>
```

React会直接删掉A节点（包括它所有的子节点）,然后新建一个B节点插入。

为了验证这一点，我打印出了从shape1到shape2节点的生命周期，gitbub链接：
[https://github.com/hhhuangqio...](https://github.com/hhhuangqiong/reconciliation)

感兴趣的可以自己跑一跑代码~

```
Shape1 :
A is created 
A render
C is created
C render
C componentDidMount
A componentDidMount

Shape2 :
A componentWillUnmount
C componentWillUnmount
B is created
B render
C is created
C render
C componentDidMount
B componentDidMount
```

由此可以看出，A与其子节点C会被直接删除，然后重新建一个B，C插入。

### 相同节点类型

当对比相同的节点类型比较简单，react会对比它们的属性，只改变需要改变的属性

比如：

```
<div className="before" title="stuff" />

<div className="after" title="stuff" />
```

这两个div中，react会只更新className的值

```
<div style={{color: 'red', fontWeight: 'bold'}} />

<div style={{color: 'green', fontWeight: 'bold'}} />
```

这两个div中，react只会去更新color的值

### 列表比较

```
<div>
  <A />
  <B />
</div>
// 列表一到列表二
<div>
  <A />
  <C />
  <B />
</div>
```

从列表一到列表二，只是在中间插入了一个C，但是如果没有key的时候，react会把B删去，新建一个C放在B的位置，然后重新建一个节点B放在尾部。

你说什么就是什么咯？！不信的话，我们还是跑一边代码，看看生命周期验证一下

```
列表一：
A is created
A render
B is created
B render
A componentDidMount
B componentDidMount

列表二：
A render
B componentWillUnmount
C is created
C render
B is created
B render
A componentDidUpdate
C componentDidMount
B componentDidMount
```

当节点很多的时候，这样做是非常低效的，所以我们需要给每个节点配一个key，让react可以识别出来哪些节点是一样的，不需要重新创建。
配上key之后，在跑一遍代码看看，

```
A render
C is created
C render
B render
A componentDidUpdate
C componentDidMount
B componentDidUpdate
```

果然，配上key之后，列表二的生命周期就如我所愿，只在指定的位置创建C节点插入。
这里要注意的一点是，key值必须是稳定（所以我们不能用Math.random()去创建key），可预测，并且唯一的。

## 小结

React整个的渲染机制就是在state/props发生改变的时候，重新渲染所有的节点，构造出新的虚拟Dom tree跟原来的Dom tree用Diff算法进行比较，得到需要更新的地方在批量造作在真实的Dom上，由于这样做就减少了对Dom的频繁操作，从而提升的性能。