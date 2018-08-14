---
title: React列表增删时使用index当作key引发的问题及原因
date: 2018-08-05 21:45:26
tags:
- 前端
- antD
categories:
- 前端
- React
---

最近项目中遇到对使用数组渲染出的输入框`<input>`列表做增加和删除操作时，已输入的数据会错乱的问题。此前隐隐觉得这个现象和「不要用数组index当key」的忠告有关，但由于没有深入了解过React的diff机制，再加上以前类似场景中，完全受控（controlled）元素的表现掩盖了这个问题，终于使得被团队code review时出现了翻车现场。在这里对这个问题作一下总结，希望结案。

TL;DR: 在React中，涉及到改变数组长度的操作时，不要用数组的index作为key渲染列表。

## 现象
演示可以点击这里：[CodePen](https://codepen.io/urnotcarol/pen/yqjPdP)

假如原始数据如下：
```
defaultItems = [
  { text: 'foo' },
  { text: 'bar' },
  { text: 'baz' }
]
```
使用这个数组渲染出一个输入框列表，为了演示方便为每个`<input>`设置`defaultValue`属性，这个属性只会作用一次，这里可以看作和用户手动输入是一样的效果，你也可以自己自由地输入值：
```
  render() {
    return (
      <ul>
        {this.state.items.map((item, index) => (
        <li key={index}>    // 这里是有问题的地方
          <input defaultValue={item.text} />
          <input
            type="button"
            value="DELETE"
            onClick={this.deleteItem.bind(this, index)}
          />
        </li>
        ))}
      </ul>
    )
  }
```
对列表项进行删除操作时，点击末项之前的任何一个删除按钮，都会发现并不是预期的效果，如[codepen](https://codepen.io/urnotcarol/pen/yqjPdP)中的示例一。当删除foo时，预期是bar成为第一项，baz成为第二项；实际上是foo依然是第一项，bar是第二项，baz消失了。

## 原因
### 前提：受控 or 非受控
这个问题浮现的原因除了和virtual DOM的key相关之外，还和React的受控/非受控的概念有关，因此需要先了解一下后面这个概念。可以参考React官网推荐的这篇文章：[Controlled and uncontrolled form inputs in React don't have to be complicated](https://goshakkk.name/controlled-vs-uncontrolled-inputs-react/). 简单来说，以下两种写法中，前一种是非受控元素，后一种是受控元素：
```
<input />   // 非受控
<input value={this.state.value} onChange={this.handleChange}/> // 受控
```
这两种写法的本质区别在于数据的流向不同，对非受控元素来说，当在页面的输入框中输入内容时，数据只保存在input这个DOM元素的value属性中，对React对象来说这个数据是不可见的。如果想要收集到输入值进行处理或上报给后端等操作，需要通过预先加在virtual DOM中的ref属性（详见文后参考）：
```
<input ref={input => this._name = input} />
```
对受控元素来说，虽然看起来可能一样，但输入框中显示的内容并不是用户输入的内容，React接管了数据，在输入发生改变时，响应onChange事件，修改state，重新渲染输入框。这样一来，我们可以对输入值做实时处理，如数据校验、把小写字母变成大写，或者各种奇奇怪怪的操作等。
总结下就是：
* 非受控：
```
graph LR
输入value --> 输入框显示value
输入框显示value --> React获取value
```
* 受控：
```
graph LR
输入value --> 触发onChange事件
触发onChange事件 --> 重新设置state
重新设置state --> 重新渲染组件显示value
```

在示例exp1中，input元素是非受控元素，亦即它的值是由DOM自己管理的。

### DOM只和数组长度相关
有了这个前提之后，就可以理解前面说的问题出现的原因了。在点击第一个输入框foo的删除按钮前后，state中的数据发生了如下的变化：
```
// before
state.items: [
  { text: 'foo' },
  { text: 'bar' },
  { text: 'baz' }
]

// after
state.items: [
  { text: 'bar' },
  { text: 'baz' }
]
```
嗯，看起来没毛病，foo不见了，理论上来说输入框也应该是这样子，可是并不是。

示例的exp1使用了数组的index作为key，因此删除前后，virtual DOM的改变如下（简短起见，省略了包裹`<input>`的`<li>`标签）：
```html
<!-- before -->
<input key=0>
<input key=1>
<input key=2>

<!-- after -->
<input key=0>
<input key=1>
```
state.items中的数据，在渲染时只用到了各项的index，也就是和其长度相关。这里的数据-视图的驱动，只和数量相关，和值无关。那3个变2个，到底是去掉第几个呢？virtual DOM的diff机制，使用key作为re-render前后元素的标识，在diff时将key相同的两个节点认为是同一个元素，进行对比，如果节点属性有变化，会更新原来的DOM（而不是替换），如果没有变化，就直接复用原来的DOM。
那么，根据key的变化，key为2的input 不再保留，剩下了前两个。自然也就只留下了前两个输入框的内容，也就是说这样的情况下，不论删除第几个，留下的输入框内容都是初始状态下的顺序排列。

也许你依然在想，我明明点的第一个删除按钮呀？因为key是index嘛，删了index=0的item，重新生成的数组依然存在index=0的元素。因此保留的总是前两个DOM元素。

又因为DOM中的值实际上是它自身在管理的，这里state中数据的变化并没有反映在DOM中，每个item的text是什么，DOM根本就不知道。

## 解决方法
基于以上原因，解决这个问题的方法有两种：
1. 为每个item指定唯一的、不变的id当作key，不要使用数组的index 
2. 将input变为受控元素

### 为列表项指定唯一不变的key
在render之前，为每个列表项设置唯一不变的id，由后台返回的id最好，如果没有的话，就要自己想办法，设置随机数、时间戳或者写一个全局的函数来生成key等等。如示例exp2所示。
```
items = [
  { text: 'foo', id: 0 },
  { text: 'bar', id: 1 },
  { text: 'baz', id: 2}
]
```
```
// render()函数中
<input key={item.id} />
```
这样一来，在点击foo的删除按钮，删除items的首项元素前后，state.items变化为：
```
// bofore
state.items: [
  { text: 'foo', id: 0 },
  { text: 'bar', id: 1 },
  { text: 'baz', id: 2}
]

// after
state.items: [
  { text: 'bar', id: 1 },
  { text: 'baz', id: 2}
]
```
又因为key用了id，virtual DOM的变化为：
```html
<!-- before -->
<input key=0>
<input key=1>
<input key=2>

<!-- after -->
<input key=1>
<input key=2>
```
这个时候就是真正删掉了第一个DOM，保留了后面两个，视图是预期的效果。需要注意的是此时的正常表现依然和item.text是无关的，换了自己输入的其他值也是一样的。

### 使用受控的`<input>`
如exp3所示，即使依然用index作为key，但如果为input指定value和onChange属性，权力集中，双向绑定，那么依然可以达到预期的效果，但是这种方式存在性能隐患。
```
// bofore
state.items: [
  { text: 'foo' },
  { text: 'bar' },
  { text: 'baz' }
]

// after
state.items: [
  { text: 'bar' },
  { text: 'baz' }
]
```
```
// render()函数中
<input key={index} value={item.text} onChange={handleChange} />
```
```
<!-- before -->
<input key=0 value="foo">
<input key=1 value="bar">
<input key=2 value="baz">

<!-- after -->
<input key=0 value="bar">
<input key=1 value="baz">
```
为什么说使用受控的input会解决这个问题呢，因为此时input的值来自于state.items。此时页面中依然是删除了原本baz所在的第三个input，但是bar和baz却保留了，原因就是第一个和第二个DOM都根据state.items更新了它们的value，使得看起来像是第一个被删除了。

之所以说这种方式存在性能隐患，是因为它只是「碰巧」解决了问题的表象。diff机制使用key来作为前后是否为同一节点的判断依据，所以它是由「删除baz所在的input -> 将原本foo所在的input的值改成bar -> 将原本bar所在的input的值改成baz」这样的步骤来完成的。和第一种解决方法相比，除了遍历开销之外，这一步的复杂度由O(1)上升为O(n)。

在不存在受控与否情况的元素（本来就完全由数据渲染的元素）列表中，比如一组图片、一组按钮，如果使用index作为key的话，对非末项元素进行删除或添加等操作，造成的开销也是一样大的。

当然不是说不能使用受控元素，而是说不提倡为了解决这个问题使用，受控元素应该是我们为了对输入进行更细粒度的控制时使用的。换句话说，即使是受控元素或者其他没有用户输入干扰的元素，已经不出现这种现象了，也最好不要使用index作为key。

## 总结
列表渲染是一个很常见的动作，在后台管理这类系统中，允许用户对列表项进行增加和删除的功能也会很常见，在这种情况下，就很有必要为列表项指定唯一不变的key。

这里的唯一和不变是两层含义，唯一就是开发过程中，React会经常提示的，要为数组中的每一项指定key，以保证其在父元素中的唯一性，往往这个时候我们就会偷懒使用index……而不变是更重要也容易被忽略的，即保证每一个列表项在渲染前后拥有同样的key，以避免开头提到的问题，同时减少diff和更新页面过程中的开销。

（React大概在吐槽：如果要用index当key，我自己就直接用了，还用你来给我说……-_-b

## 后记
实际上在项目中遇到这个问题时，情况稍微复杂一点，是在使用了antDesign 2.x的Form组件的`getFieldDecorator(id, options)`API的时候。它会使得input等元素变成受控元素，为其自动添加value和onChange属性，创建双向绑定，但会绑定到antd Form的一个values对象上，使用第一个参数id指定的字符串作为对象key-value中的key。也就是说，虽然受控了，但是控制权并不在自己手里，因此也需要注意这里id的唯一和不变性。否则的话，由于数据被antd Form接管、未和state绑定，id又和index关联，最后客观上就会造成和示例exp1中，input未受控且key依赖index一样的效果。

- - - -
# reference
1. [深度剖析：如何实现一个 Virtual DOM 算法](https://github.com/livoras/blog/issues/13)
2. [Controlled and uncontrolled form inputs in React don't have to be complicated](https://goshakkk.name/controlled-vs-uncontrolled-inputs-react/ )
3. [Refs and the DOM - React](https://reactjs.org/docs/refs-and-the-dom.html)

#react #antd