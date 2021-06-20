# 一、Object的变化侦测

## 什么是变化侦测

* `Angular`和React中的变化侦测都属于“拉”，这就是说当状态发生变化时，不知道具体哪个状态变了，只知道状态有可能变了，然后发送一个信号告诉框架，框架内部收到信号后，会进行一个暴力比对来找出哪些DOM节点需要重新渲染。这在Angular中是脏检查的流程，而在React中使用的是虚拟DOM。

* `Vue.js`的变化属于“推”。当变化发生变化时，`Vue.js`立刻就知道了，而且在一定程度上知道哪些状态变化了。因此，它知道的信息更多，也就可以进行更细粒度的更新。

### 细粒度

但是细粒度越细，每个状态绑定的依赖就越多，依赖追踪在内存上的开销就会越大。**因此`Vue.js`2.0引入了虚拟DOM，将粒度调整为中等粒度，即一个状态绑定的依赖不再是具体的DOM节点，而是一个组件。**这样状态变化后，会通知到组件，组价内部再使用虚拟DOM进行比对。这可以大大降低依赖数量，从而降低依赖追踪所消耗的内存。

> `Vue.js`之所以可以随意调整粒度，本质上还要归功于变化侦测。因为“推”类型的变化侦测可以随意调整粒度

## 如何追踪变化侦测

到目前为止`Vue.js`还是使用的`Object.defineProperty`来实现的，但是在`Vue3.0`的版本中使用了`Proxy`来重写。

知道了`Object.defineProperty`可以侦测到对象的变化，那么可以先写出这样的代码

```javascript
export function defineReactive (
  obj: Object,
  key: string,
  val: any,
  customSetter?: ?Function,
  shallow?: boolean
) {
  // 在闭包中定义一个dep对象(依赖收集和派发的仓库)
  const dep = new Dep()

  // 拿到传入obj中key的一些定义
  const property = Object.getOwnPropertyDescriptor(obj, key)
  if (property && property.configurable === false) {
    return
  }

  // 如果之前该数据对象已经预设了getter以及setter函数则将其取出来，
  // 新定义的getter/setter中会将其执行，保证不会覆盖之前已经定义的getter/setter。
  const getter = property && property.get
  const setter = property && property.set
  if ((!getter || setter) && arguments.length === 2) {
    val = obj[key]
  }

  // 对象的子对象递归进行observe并返回子节点的Observer对象，不断的进行递归observe
  let childOb = !shallow && observe(val)

  // 将数据变成响应式对象
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter () {
      // 如果原本对象拥有getter方法则优先执行原有的getter方法
      const value = getter ? getter.call(obj) : val
      // Dep.target为一个全局的Watcher
      if (Dep.target) {
        // 通过dep的depend进行依赖收集
        dep.depend()
        console.log(dep)
        // 子对象进行依赖收集，其实就是将同一个watcher观察者实例放进了两个depend中，
        // 一个是正在本身闭包中的depend（例如对象obj），另一个是子元素的depend（例如obj.a）
        if (childOb) {
          childOb.dep.depend()
          if (Array.isArray(value)) {
            // 是数组则需要对每一个成员都进行依赖收集，如果数组的成员中有对象类型，则递归。
            // 对于数组中object身上所有属性，也要进行依赖收集
            dependArray(value)
          }
        }
      }
      return value
    },
    set: function reactiveSetter (newVal) {
      // 通过getter方法获取当前值，与新值进行比较，一致则不需要执行下面的操作
      const value = getter ? getter.call(obj) : val
      /* eslint-disable no-self-compare */
      if (newVal === value || (newVal !== newVal && value !== value)) {
        return
      }
      /* eslint-enable no-self-compare */
      if (process.env.NODE_ENV !== 'production' && customSetter) {
        customSetter()
      }
      // #7981: for accessor properties without setter
      if (getter && !setter) return
      if (setter) {
        // 如果原本对象拥有setter方法则优先执行原有的setter方法
        setter.call(obj, newVal)
      } else {
        val = newVal
      }
      // 如果新的值为对象的话，需要重新进行observe，保证数据响应式
      childOb = !shallow && observe(newVal)
      // dep对象通知所有的观察者(派发更新)
      dep.notify()
    }
  })
}
```

这里的函数`defineReactive`用来对`Object.defineProperty`进行封装。封装好了之后，每当从`data`的key中读取数据时，get函数就会被出发；每当往data的key中设置数据时，set函数被触发。

## 如何收集依赖

我们之所以要观察数据，其目的是当数据的属性发生变化时，可以通知那些曾经使用该数据的地方。

举个例子：

```vue
<template>
<h1>{{ name }}</h1>
</template>

data: function () {
	return {
		name: 'ljw'
	}
}
```

该模板中使用了name，所以当它发生变化时，要向使用了它的地方发送通知。

对于上面的问题，我们应该先收集依赖，即把用到数据name的地方收集起来，然后等属性发生变化时，把之前收集好的依赖循环出发一遍就好了。

其实就一句话，在getter中收集依赖，在setter中触发依赖。

## 依赖收集在哪里

每个key都有一个数组，用来存储当前key的依赖。假设依赖时一个函数，保存在`Dep.target`上，现在就可以将`defineReactive`函数稍微改造一下：

```javascript
function defineReactive (data, key, val) { // data为属性所在对象，key为属性的名字，val为属性值
    // ....
    get: function reactiveGetter () {
      // 如果原本对象拥有getter方法则优先执行原有的getter方法
      const value = getter ? getter.call(obj) : val
      // Dep.target为一个全局的Watcher
      if (Dep.target) {
        // 通过dep的depend进行依赖收集
        dep.depend()
        console.log(dep)
        // 子对象进行依赖收集，其实就是将同一个watcher观察者实例放进了两个depend中，
        // 一个是正在本身闭包中的depend（例如对象obj），另一个是子元素的depend（例如obj.a）
        if (childOb) {
          childOb.dep.depend()
          if (Array.isArray(value)) {
            // 是数组则需要对每一个成员都进行依赖收集，如果数组的成员中有对象类型，则递归。
            // 对于数组中object身上所有属性，也要进行依赖收集
            dependArray(value)
          }
        }
      }
      return value
    }
    // ... 
}
```

我们把依赖收集的代码封装成一个`Dep`类，用它来帮我们专门管理依赖。使用这个类，我们可以收集依赖、删除依赖或者向依赖发送通知等等。

### `Dep类`

```javascript
export default class Dep {
  static target: ?Watcher;
  id: number;
  subs: Array<Watcher>;

  constructor () {
    this.id = uid++
    this.subs = []
  }

  // 添加一个观察者对象
  addSub (sub: Watcher) {
    this.subs.push(sub)
  }

  // 移除一个观察者对象
  removeSub (sub: Watcher) {
    remove(this.subs, sub)
  }

  // 依赖收集，当存在Dep.target的时候添加观察者对象
  depend () {
    if (Dep.target) {
      Dep.target.addDep(this)
    }
  }

  // 通知所有订阅者
  notify () {
    // stabilize the subscriber list first
    const subs = this.subs.slice()
    for (let i = 0, l = subs.length; i < l; i++) {
      subs[i].update()
    }
  }
}
```

## 依赖是谁

在前面代码中，我们收集的是`window.target`，那么它到底是什么？

我们要通知用到数据的地方，而使用这个数据的地方有很多，而且类型还不一样，既有可能是模板，也有可能是用户写的一个`watch`，这时就需要抽象一个能集中处理这些情况的类。然后，我们在依赖收集阶段只收集这个封装好的类的实例进来，通知也只通知它一个。接着，它再负责通知其他地方。我们就叫这个为`watcher`

### `Watcher`

Watcher只是一个中介角色，数据发生变化时通知它，然后它再通知其他地方。

先看一个经典的使用方式：

```javascript
vm.$watch('a.b.c', function (newVal, oldVal) {
  // 做点什么
})
```

这段代码表示当`data.a.b.c`属性发生变化时，触发第二个参数中的函数。

大概流程就是：先把这个watcher实例添加到`data.a.b.c`属性的`Dep`中，当`data.a.b.c`的值发生变化时，通知`Watcher`，接着Watcher再执行参数中的这个回调函数。

```JavaScript
export default class Watcher {
  constructor () {
    this.vm = vm
    this.cb = cb
    // 执行this.getter(),就可以读取data.a.b.c的内容
    this.getter = parsePath(expOrFn)
    this.value = this.get()
  }
  get () {
    window.target = this
    let value = this.getter.call(this.vm, this.vm)
    window.target = undefined
    return value
  }

  update () {
   const oldValue = this.value
   this.value = this.get()
   this.cb.call(this.vm, this.vm, oldValue)
  }

}
```

* 在`get`方法中先把`window.target`设置成了this.也就是当前watcher实例。然后再读一下data.a.b.c的值,这肯定会触发getter，触发了getter就会触发收集依赖的逻辑。（先在`window.targe`t赋一个this，然后再读一下值，去触发getter，就可以把this主动添加到keypath的Dep中。）
* 依赖注入到`Dep`中后,每当`data.a.bc`的值发生变化时,就会让依赖列表中所有的依赖循环触发update方法,也就是Watcher中的update方法而方法会执行参数中的回调函数,将value和oldValue传到参数中。
* 其实不管是用户执行的`vm.$watch('a.b.c', (value,oldvalue) => {})`,还是模板中用到的data,都是通过Watcher来通知自己是否需要发生变化。

> 当data中的数据应用于视图时，该数据会被记为一个Watcher；watch中的状态必定会被记为一个Watcher；computed中的也是在被用于视图的情况下，会被记为一个Watcher

## 递归侦测所有key

每个key都有一个对应的`Dep`列表来存储依赖。因为我们要把数据中所有的属性（包括子属性）都侦测到，所以要封装一个`Observer`类。这个类的作用就是将一个数据内的所有属性（包括子属性）都转换成getter/setter的形式，然后去追踪它们的变化。

```javascript
export class Observer {
  value: any;
  dep: Dep;
  vmCount: number; // number of vms that have this object as root $data

  constructor (value: any) {
    this.value = value
    this.dep = new Dep()
    this.vmCount = 0
    // 将当前的Observer实例赋给value上新增的一个不可枚举的属性__ob__
    def(value, '__ob__', this)
    if (！Array.isArray(value)) {
      this.walk(value) 
    }
  }

  walk (obj: Object) {
    const keys = Object.keys(obj)
    for (let i = 0; i < keys.length; i++) {
      defineReactive(obj, keys[i])
    }
  }
}
function defineReactive (data, key, val) {
  // 伪代码，递归子属性
  if (typeof val === 'object') {
    new Observer(val)
  }
}
```

定义了一个Observer类，用它来将一个正常的object转换成被侦测的object，只有object类型的数据才会调用walk将每一个属性转换成getter/setter的形式来侦测变化。

最后，在`defineReactive`中新增`new Observer`来递归子属性，这样我们就可以把data中所有的属性（包括子属性）都转换成getter/setter的形式来侦测变化。

## 关于Object的问题

对于使⽤`Object.defineProperty`实现响应式的对象，当我们去给这个对象添加或删除⼀个新的属性的时候，由于这些操作不能够触发它的setter，也就不会更新视图。

为了解决这些问题，Vue.js提供了两个API — `vm.$se`t与`vm.$delete`

## 总结

* 变化侦测就是侦测数据的变化。当数据发生变化时，要能侦测到并发出通知。

* Object可以通过Object.defineproperty属性转换为getter/setter形式来追踪变化。

* 读取数据时会触发getter，修改数据时会触发setter。我们需要在getter中收集有哪些依赖使用了数据，当setter触发时,去通知getter中收集的依赖数据发生了变化。

* 我们创建了Dep,它用来收集依赖、删除依赖和向依赖发送消息等

* 所谓的依赖,其实就是Watcher。触发的getter才会收集依赖,哪个Watcher触发了getter，就把哪个Watcher收集到Dep中。当数据发生变化时，会循环依赖列表，把所有的Watcher都通知一遍。变化通知到外界后，可能会触发视图更新，也有可能触发用户的某个回调函数等（watch、computed方法）。

![image-20191129171637510](D:\Note\image\image-20191129171637510.png)

# 二、Array的变化侦测

## 如何追踪依赖

在`ES6`之前，`JavaScript`并没有给数组提供元编程能力，也就是没有提供可以拦截原型方法的能力，但是我们可以通过自定义去覆盖原生的原型方法.

![image-20191129160537153](D:\Note\image\image-20191129160537153.png)

每当使用Array原型上的方法操作数组时，其实执行的都是拦截器中提供的方法，比如`push`方法。然后在拦截器中使用原生Array的原型方法去操作数组。

有了拦截器之后，想要它生效，就必须用它去覆盖`Array.prototype`.但是我们又不能直接覆盖，因为这样会污染全局的`Array`。我们希望拦截操作只会对那些被侦测了变化的响应式数据生效，也就是说希望拦截器只覆盖那些响应式数组的原型。

```javascript
// 代码有大量删减

export class Observer {
  
  // 。。。
  constructor (value: any) {
    
    // 。。。
    
    if (Array.isArray(value)) {
      // 如果data中有属性是数组，将修改后可以截获响应的数组方法替换掉该数组的原型中的原生方法，
      // 达到监听数组数据变化响应的效果。这里如果当前浏览器支持__proto__属性，
      // 则直接覆盖当前数组对象原型，如果不支持该属性，
      // 则直接将arrayMethods身上的方法设置到被侦测的数组身上。
      // value.__proto__ = arrayMethods
      const augment = hasProto
        ? protoAugment  /*直接覆盖原型的方法来修改目标对象*/
        : copyAugment   /*定义（覆盖）目标对象或数组的某一个方法*/
      // 将拦截器2赋值给value.__proto__，通过__proto__可以巧妙地实现覆盖value原型的功能
      augment(value, arrayMethods, arrayKeys)

      /*如果是数组则需要遍历数组的每一个成员进行observe*/
      this.observeArray(value)
    } else {
      /*如果是对象则直接walk进行绑定*/
      this.walk(value)
    }
  }

	// 。。。
}
```

## 注意：

如果data中有属性是数组，将修改后可以截获响应的数组方法替换掉该数组的原型中的原生方法，达到监听数组数据变化响应的效果。这里如果当前浏览器支持`__proto__`属性，则直接覆盖当前数组对象原型，如果不支持该属性，则直接将`arrayMethods`身上的方法设置到被侦测的数组身上。

![image-20191129161928627](D:\Note\image\image-20191129161928627.png)

>  从`ECMAScript 2015` 开始，JavaScript 获得了 [`Proxy`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy) 和 [`Reflect`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect) 对象的支持，允许你拦截并定义基本语言操作的自定义行为（例如，属性查找，赋值，枚举，函数调用等）。借助这两个对象，你可以在 JavaScript 元级别进行编程。

## 如何收集依赖

`Array的依赖和Object一样也是在defineReactive中收集`

**但我们需要注意的是，`Array在getter`中收集依赖，却是在拦截器中触发依赖**

## 依赖列表存在哪儿

`Vue.js`把`Array`的依赖存放在`Observer`实例中（事实上，`Object`的依赖除了保存在`defineReactive`中，也会保存一份在`Observer`实例上）

**为什么数组的`dep`会保存在`Observer`实例上呢？**

**我们知道数组会在getter中收集依赖，在拦截器中触发依赖，所以这个依赖保存的位置就很关键，它必须在getter和拦截器中都可以访问到。我们之所以将依赖保存在Observer实例上，就是因为在getter中可以访问到Observer实例，同时在Array拦截器中也可以访问到Observer实例**

## 收集依赖

通过`observer`函数我们可以得到数组的`Observer`实例（`childOb`），最后通过`childOb的dep`执行`depend`方法来收集依赖

```javascript
// ...
let childOb = !shallow && observe(val)
// ...
if (childOb) {
  childOb.dep.depend()
  if (Array.isArray(value)) {
    // 数组每个元素也去做依赖收集
    dependArray(value)
  }
}
```

## 在拦截器中获取Observer实例

```javascript
export class Observer {
  value: any;
  dep: Dep;
  vmCount: number; // 用来判断是否为根data

  constructor (value: any) {
    this.value = value
    // 实例化一个Dep对象（注意，该dep实例被存放在Observer实例上，
    // 而且该Observer实例会被存放到当前value的__ob__属性上）
    // 同时这也是数组类型的依赖列表
    this.dep = new Dep()
    this.vmCount = 0
     // 通过def函数将当前的Observer的实例赋给value上新增的一个不可枚举的属性__ob__，
    // 这样我们就可以通过数据的__ob__属性拿到Observer实例，然后就可以拿到__ob__上的dep了
    // 当然__ob__的作用不仅仅是为了在拦截器中访问Observer实例这么简单，还可以用来标记当前value是否
    // 已经被转换为响应式数据。
    def(value, '__ob__', this)
    if (Array.isArray(value)) {
      if (hasProto) {
        protoAugment(value, arrayMethods)
      } else {
        copyAugment(value, arrayMethods, arrayKeys)
      }
      this.observeArray(value)
    } else {
      this.walk(value)
    }
  }
	// ...删除了部分代码
}
```

将当前的`Observer`的实例赋给`value`上新增的一个不可枚举的属性`__ob__`，这样我们就可以通过数据的`__ob__`属性拿到`Observer`实例，然后就可以拿到`__ob__`上的`dep`了。当然`__ob__`的作用不仅仅是为了在拦截器中访问`Observer`实例这么简单，还可以用来标记当前`value`是否已经被转换为响应式数据。

## 向数组的依赖发送通知

当侦测到数组发生变化时，会向依赖发送通知。使用`ob.dep.notify()`去通知依赖（`Watcher`）数据发生了变化

## 侦测数组中元素的变化

前面说过如何侦测数组的变化，指的是数组自身的变化，比如是否新增一个元素，是否删除一个元素等。

其实数组中保存了一些元素，它们的变化也是需要侦测的，比如，当数组中的object类型元素身上某个属性的值发生了变化时，也需要发送通知。

此外,如果用户使用了push往数组中新增了一个元素，这个新增元素的变化也需要侦测数据。

也就是说,所有响应式数据的子数据都要侦测，不论是Object中的数据还是Array中的数据（但是**注意：Array中只会侦测对象类型的子数据**）。

## 新增元素

如果操作数组的方法是`push、unshift和splice`（可以新增数组元素的方法），则把参数中新增的元素提取出来，然后使用`observeArray`对新增数据进行变化侦测。

```javascript
const methodsToPatch = [
  'push',
  'pop',
  'shift',
  'unshift',
  'splice',
  'sort',
  'reverse'
]

methodsToPatch.forEach(function (method) {
  // cache original method
  const original = arrayProto[method]
  def(arrayMethods, method, function mutator (...args) {
    const result = original.apply(this, args)
    // 获取当前value的Observer实例
    const ob = this.__ob__
    let inserted
    switch (method) {
      case 'push':
      case 'unshift':
        inserted = args
        break
      case 'splice':
        inserted = args.slice(2)
        break
    }
    if (inserted) ob.observeArray(inserted)
    // 派发依赖更新
    ob.dep.notify()
    return result
  })
})
```

我们通过`switch对methods`进行判断，如果`methods是push、unshift、splice`这种可以新增数组的方法，`name从args`中将新增元素取出来，暂存在`inserted`中，然后使用`Observer把inserted`中的元素转换成响应式。

## 关于Array的问题

对Array的变化侦测额是通过拦截原型的方式实现的。正是因为这种实现方式，其实有些数组操作Vue.js是拦截不到的。

`Vue`不能检测到以下变动的数组： 

1. 当你利⽤索引直接设置⼀个项时，例如：`vm.items[indexOfItem]	=	newValue`
2. 当你修改数组的⻓度时，例如：`vm.items.length = newLength`

对于第⼀种情况，可以使⽤：`Vue.set(example1.items, indexOfItem, newValue)`；⽽对于第⼆种情况，可以使⽤`vm.items.splice(newLength)`。 

> 在`ES6`中提供了元编程的能力，所以有能力拦截。可能未来`Vue.js`很有可能使用`ES6`提供的`Proxy`来实现这部分功能。

## 总结

* Array追踪变化的方式和Object不一样，因为它是通过方法来改变内容的，所有我们通过创建拦截器去覆盖数组原型的方式来追踪变化。
* 在Observer中，我们对每个侦测了变化的数据都标上了印记`__ob__`，并把this（Observer实例）保存在`__ob__`上。主要有两个作用：
  * 一方面是为了标记数据是否被侦测了变化（保证同一数据只被侦测一次）
  * 另一方面可以很方便地通过数据读取到`__ob__`，从而拿到Observer实例上保存的依赖。当拦截到数组发生变化时，向依赖发送通知。

# 三、变化侦测相关的`API`实现原理

## `vm.$watch`

### 用法

```javascript
vm.$watch(expOrFn, callback, [options])
```

#### 参数

* `{string | Function} expOrFn`

* `{Function | Object} callback`

* ```
  {Object} [options]
  ```

  - `{boolean} deep`当设置了deep为true时，使用traverse方法对该被监听数据进行深层次的依赖收集。
  - `{boolean} immediate`

#### 返回值

返回一个取消观察函数，用来停止触发回调。其本质就是把Watcher实例从当前正在观察的状态的依赖列表中移除。

* `{Function} unwatch`

### 内部原理

```JavaScript
// $watch源码  
Vue.prototype.$watch = function (
    expOrFn: string | Function,
    cb: any,
    options?: Object
  ): Function {
    const vm: Component = this
    if (isPlainObject(cb)) {
      return createWatcher(vm, expOrFn, cb, options)
    }
    options = options || {}
    options.user = true
    const watcher = new Watcher(vm, expOrFn, cb, options)
    if (options.immediate) {
      try {
        cb.call(vm, watcher.value)
      } catch (error) {
        handleError(error, vm, `callback for immediate watcher "${watcher.expression}"`)
      }
    }
    // 返回一个取消观察函数，用来停止触发回调。
    return function unwatchFn () {
      watcher.teardown()
    }
  }
```

```javascript
// Wacther类源码
  // 删除了大量代码

export default class Watcher {
	// ...
  constructor (
    vm: Component,
    expOrFn: string | Function,
    cb: Function,
    options?: ?Object,
    isRenderWatcher?: boolean
  ) {
    this.vm = vm
    // ...
    // 当expOrFn为函数时
    if (typeof expOrFn === 'function') {
      this.getter = expOrFn
    } else {
      // 如果不是函数，则使用parsePath函数来读取路径属性（keypath）中的数据。
      // 例如a.b.c.d就是一个keypath，说明从vm.a.b.c.d中读取数据
      this.getter = parsePath(expOrFn)
      if (!this.getter) {
        this.getter = noop
        process.env.NODE_ENV !== 'production' && warn(
          `Failed watching path: "${expOrFn}" ` +
          'Watcher only accepts simple dot-delimited paths. ' +
          'For full control, use a function instead.',
          vm
        )
      }
    }
    tgis.cb = cb
    this.value = this.lazy
      ? undefined
      : this.get()
  }

  // ...
}
```

* 如果expOrFn是函数，直接将它赋给getter，而且Watcher会同时观察expOrFn函数中读取的所有Vue.js实例上的响应数据，也就是说如果函数从Vue.js读取了两个数据，那么Watcher会同时观察这两个数据的变化，当其中任意一个发生变化时，Watcher都会得到通知。
* 如果不是函数，则使用parsePath函数来读取路径属性（keypath）中的数据。例如a.b.c.d就是一个keypath，说明从vm.a.b.c.d中读取数据。

### `Watcher与Dep`的关系

![image-20191206210637724](D:\Note\image\image-20191206210637724.png)

为什么是多对多的关系。Watcher每次只读一个数据，不应该只有`Dep`吗？

其实不是,如果Watcher中的`expOrFn`参数是一个表达式，那么肯定只收集一个`Dep`，并且大部分都是这样。但凡事总有例外，`expOrFn`可以是一个函数，此时如果该函数中使用了多个数据，那么这时`Watcher`就要收集多个`Dep`了,例如:

```JavaScript
this.$watch(function () {
  return this.name + this.age
}, function (newValue, oldValue) {
  console.log(newValue, oldValue)
})
```

在上面这个例子中，我们的表达式是一个函数，并且在函数中访问了`name和age`两个数据，这种情况下`watcher`内部会收集两个`Dep——name的Dep和age的Dep`,同时这两个`Dep`中也会收集`watcher`，这导致`age和name`中的任意一个数据发生变化，`watcher`都会收到通知。

**`computed`中也是同理**

```javascript
var	vm	=	new	Vue({		
  data:	{				
    firstName:	'Foo',				
    lastName:	'Bar'		
  },		
  computed:	{				
    fullName:	function	()	{						
      return	this.firstName	+	'	'	+	this.lastName				
    }		
  } 
}) 
```

**当触发`fullName的getter`时，由于`this.firstName和this.lastName`都是响应式对象，这⾥会触发它们的`getter`，根据我们之前的分析，它们会把⾃⾝持有的`dep`添加到当前正在计算的watcher中，这个时候`Dep.target`就是这个`computed	watcher`，同时`this.firstName和this.lastName`的`dep`也会收集这个`computed watcher（fullName）。如果计算属性fullName在模板中使用了，那么this.firstName和this.lastName`的`dep`也会收集这个`render watcher`**	

## `vm.$set`

`vm.$set`为解决无法侦测object什么时候被添加了一个新属性而存在。

### 用法

在object上设置一个属性，如果object是响应式，`Vue.js`会保证属性被创建后也是响应式的，并且触发视图更新。

```javascript
vm.$set(target, key, value) 
```

> target不能是`Vue.js`实例或者`Vue.js`实例的根数据对象（this.$data）

#### 参数

- `{Object | Array} target`
- `{string | number} propertyName/index`
- `{any} value`

#### **返回值**

`{Function} unwatch`

### 内部原理

```javascript
/instance/state.js

Vue.prototype.$set = set

/observer/index.js
export function set (target: Array<any> | Object, key: any, val: any): any {
  if (process.env.NODE_ENV !== 'production' &&
    (isUndef(target) || isPrimitive(target))
  ) {
    warn(`Cannot set reactive property on undefined, null, or primitive value: ${(target: any)}`)
  }
  // 如果target是数组并且key是一个有效值
  if (Array.isArray(target) && isValidArrayIndex(key)) {
    // 如果我们传递的索引值大于当前数组的length，就需要让target的length等于索引值
    target.length = Math.max(target.length, key)
    // 使用splice把val设置到target时，数组拦截器会侦测到target发生变化，
    // 并且会自动帮助我们把这个新增的val转成响应式
    target.splice(key, 1, val)
    return val
  }
  if (key in target && !(key in Object.prototype)) {
    // 如果key已经存在target中，直接修改数据
    target[key] = val
    return val
  }
  // 获取target的__ob__
  const ob = (target: any).__ob__
  // 因为target不能时Vue实例或Vue实例的根数据对象，所以此处进行判断
  // target._isVue判断是否为Vue实例，ob.vmCount判断是否为根数据
  if (target._isVue || (ob && ob.vmCount)) {
    process.env.NODE_ENV !== 'production' && warn(
      'Avoid adding reactive properties to a Vue instance or its root $data ' +
      'at runtime - declare it upfront in the data option.'
    )
    return val
  }
  // 如果target不是响应式，那么就直接通过key和val在target身上设置即可
  if (!ob) {
    target[key] = val
    return val
  }
  // 如果前面的条件都不满足，那么使用defineReactive将新增属性转换成getter/setter形式即可
  defineReactive(ob.value, key, val)
  // 派发更新
  ob.dep.notify()
  return val
}
```

**如果target是数组并且key是一个有效的索引值，当我们传递的索引值大于当前数组的length，就需要让target的length等于索引值，然后使用splice把val设置到target时，数组拦截器会侦测到target发生变化，并且会自动帮助我们把这个新增的val转成响应式。如果key已经存在target中，直接修改数据并返回，如果不存在则再判断target是不是`Vue`实例或`Vue`实例的根数据对象，如果是的话就进行报错。再判断target存不存在`__ob__`属性，如果target不是响应式，那么就直接通过key和val在target身上设置。如果前面的条件都不满足，那么使用`defineReactive`将新增属性转换成`getter/setter`形式，最后派发更新。**

## `vm.$delete`

`vm.$set`为解决无法侦测object什么时候被删除了一个属性而存在。

### 用法

```javascript
vm.$delete(target, key)
```

> 目标对象不能是`Vue.js`实例或者`Vue.js`实例的根数据对象（this.$data）

#### 参数

- `{Object | Array} target`
- `{string | number} propertyName/index`

### 实现原理

```javascript
/instance/state.js

Vue.prototype.$delete = del

/observer/index.js

export function del (target: Array<any> | Object, key: any) {
  if (process.env.NODE_ENV !== 'production' &&
    (isUndef(target) || isPrimitive(target))
  ) {
    warn(`Cannot delete reactive property on undefined, null, or primitive value: ${(target: any)}`)
  }
  // 如果是数组，就利用splice方法来执行删除操作
  if (Array.isArray(target) && isValidArrayIndex(key)) {
    // 使用splice删除元素时,数组拦截器会侦测到target发生变化,自动派发更新
    target.splice(key, 1)
    return
  }
  // 获取target的__ob__
  const ob = (target: any).__ob__
  // 因为target不能时Vue实例或Vue实例的根数据对象，所以此处进行判断
  // target._isVue判断是否为Vue实例，ob.vmCount判断是否为根数据
  if (target._isVue || (ob && ob.vmCount)) {
    process.env.NODE_ENV !== 'production' && warn(
      'Avoid deleting properties on a Vue instance or its root $data ' +
      '- just set it to null.'
    )
    return
  }
  // 如果target中没有key属性,那么直接返回
  if (!hasOwn(target, key)) {
    return
  }
  // 从target中将key删除
  delete target[key]
  if (!ob) {
    // 如果target不是响应式数据,则直接返回
    return
  }
  // 派发更新
  ob.dep.notify()
}
```

**如果是数组，就利用splice方法来执行删除操作，会被拦截器侦测到，然后发送通知。否则判断target是不是`Vue`实例或`Vue`实例的根数据对象，如果是的话就进行报错。然后判断target身上如果是否存在key属性，如果不存在则直接返回。然后再判断target存不存在`__ob__`属性，只有响应式数据才需要发送同时，非响应式数据只要执行删除操作即可。**

> 其实我们也可以使用delete来删除属性，但是强烈不推荐这样做

```javascript
delete this.obj.name
this.obj.__ob__.dep.notify() // 手动向依赖发送变化通知
```

# 四、虚拟DOM

> 事实上，并不是引入虚拟DOM后，渲染速度就变快了。准确来说，应该是百分之八十的场景下变得更快了，而剩下的百分之二十反而变慢了。

## 解决方式

**通过状态生成一个虚拟节点树，然后使用虚拟节点树进行渲染。在渲染之前，会使用新生成的虚拟节点树和上一次生成的虚拟节点树进行比对，只重新渲染不同的部分。**

> **当某个状态发生变化时，只更新与这个状态相关联的`DOM`节点**

## 为什么要引入虚拟DOM

**因为`Vue.js1.0`的侦测粒度太细，开销太大。**因此，`Vuejs2.0`开始选择了一个中等粒度的解决方案，那就是引入了虚拟DOM。组件级别是个Watcher实例，就是说**即便一个组件内有10个节点使用了某个状态，但其实也只有一个Watcher在观察这个状态的变化。所以当这个状态发生变化时，只能通知到组件，然后组件内部通过虚拟DOM去进行比对与渲染。这是一个比较折中的方案。**

## `Vue.js`中的虚拟DOM

在`Vue.js`中，我们使用模板来描述状态与DOM之间的映射关系。`Vue.js`通过编译将模板转换为渲染函数（render），执行渲染函数就可以得到一个虚拟节点树，使用这个虚拟节点树就可以渲染页面。

![image](https://upload-images.jianshu.io/upload_images/16269351-ce873bd02e2847d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

虚拟DOM在`Vue.js`中所做的事情其实并没有想象的那么复杂，它主要做了两件事：

* **提供与真实DOM节点所对应的虚拟接线`vnode`**
* **将虚拟节点`vnode`和旧虚拟节点`oldVnode`进行比对，然后更新视图。**对两个虚拟节点进行比对是虚拟DOM中最核心的算法（patch）

# 五、`VNode`

## `什么是VNode`

在`Vue.js`中表示一个`VNode`类，使用它可以实例化不同类型的`vnode`实例，而不同类型的`vnode`实例各自表示不同类型的DOM元素

例如，**DOM元素有元素节点、文本节点和注释节点等，`vnode`实例也会对应有元素节点、文本节点和注释节点等。**

```javascript
// VNode类

export default class VNode {
  tag: string | void;
  data: VNodeData | void;
  children: ?Array<VNode>;
  text: string | void;
  elm: Node | void;
  ns: string | void;
  context: Component | void; // rendered in this component's scope
  key: string | number | void;
  componentOptions: VNodeComponentOptions | void;
  componentInstance: Component | void; // component instance
  parent: VNode | void; // component placeholder node

  // strictly internal
  raw: boolean; // contains raw HTML? (server only)
  isStatic: boolean; // hoisted static node
  isRootInsert: boolean; // necessary for enter transition check
  isComment: boolean; // empty comment placeholder?
  isCloned: boolean; // is a cloned node?
  isOnce: boolean; // is a v-once node?
  asyncFactory: Function | void; // async component factory function
  asyncMeta: Object | void;
  isAsyncPlaceholder: boolean;
  ssrContext: Object | void;
  fnContext: Component | void; // real context vm for functional nodes
  fnOptions: ?ComponentOptions; // for SSR caching
  devtoolsMeta: ?Object; // used to store functional render context for devtools
  fnScopeId: ?string; // functional scope id support

  constructor (
    tag?: string,
    data?: VNodeData,
    children?: ?Array<VNode>,
    text?: string,
    elm?: Node,
    context?: Component,
    componentOptions?: VNodeComponentOptions,
    asyncFactory?: Function
  ) {
    this.tag = tag
    this.data = data
    this.children = children
    this.text = text
    this.elm = elm
    this.ns = undefined
    this.context = context
    this.fnContext = undefined
    this.fnOptions = undefined
    this.fnScopeId = undefined
    this.key = data && data.key
    this.componentOptions = componentOptions
    this.componentInstance = undefined
    this.parent = undefined
    this.raw = false
    this.isStatic = false
    this.isRootInsert = true
    this.isComment = false
    this.isCloned = false
    this.isOnce = false
    this.asyncFactory = asyncFactory
    this.asyncMeta = undefined
    this.isAsyncPlaceholder = false
  }

  // DEPRECATED: alias for componentInstance for backwards compat.
  /* istanbul ignore next */
  get child (): Component | void {
    return this.componentInstance
  }
}

export const createEmptyVNode = (text: string = '') => {
  const node = new VNode()
  node.text = text
  node.isComment = true
  return node
}
```

`vnode`只是一个名字，**本质上其实是JavaScript中一个普通对象，是从`VNode`类实例化的对象。**我们用这个对象来描述一个真实DOM元素，该DOM元素上的所有属性在`vnode`这个对象上都存在相应的属性。

## `VNode`的作用

由于每次渲染视图时都是先创建`vnode`，然后使用它创建真实`DOM`插人到页面中，所以可以**将上一次渲染视图时所创建的`vnode`缓存起来，之后每当需要重新渲染视图时，将新创建的`vnode`和上一次缓存的`vnode`进行对比，查看它们之间有哪些不一样的地方，找出这些不一样的地方并基于此去修改真实的DOM，减少DOM操作。**

如果组件只有一个节点发生了变化，那么重新渲染整个组件的所有节点，很明显会造成很大的性能浪费。因此，对`vnode`进行缓存，并将上一次缓存的`vnode`和当前新创建的`vnode`进行对比，**只更新发生变化的节点**就变得尤为重要。这也是`vnode`最重要的一个作用。

## `VNode`的类型

* 注释节点

  两个有效属性 —— `text和isComment`

* 文本节点

  被创建时，只有一个`text`属性

* 元素节点

  通过`tag`属性来识别元素节点

* 组件节点

* 函数式组件

* 克隆节点

  克隆节点是将现有节点的属性复制到新节点中，让新创建的节点和被克隆节点的属性保持一致，从而实现克隆效果。**克隆节点的作用是优化静态节点和插槽节点。**

# 六、`patch`

**虚拟`DOM`最核心的部分是`patch`，它可以将`vnode`渲染成真实的DOM。**

`patch`也可以叫作`patching`算法，通过它渲染真实`DOM`时，并不是暴力覆盖原有`DOM`，而是比对新旧两个`vnode`之间有哪些不同，然后根据对比结果找出需要更新的节点进行更新。**在现有DOM上进行修改来实现更新视图的目的。**

## patch介绍

对现有`DOM`进行修改需要做三件事：

* **创建新增的节点**
* **删除已经废弃的节点**
* **修改需要更新的节点**

### 新增节点

1. **首次渲染**

   当`oldVnode`不存在而`vnoed`存在时，就需要直接使用`vnode`生成真实DOM元素并将其插入到视图当中去

2. **`vnode`和`oldVnode`完全不是同一个节点**

   当`vnode`和`oldVnode`完全不是同一个节点时，可以得知`vnode`就是一个全新的节点，而`oldVnod`e就是一个被废弃的节点，需要使用`vnode`生成真实的`DOM`元素并用它去替换`oldVnode`所对应的真实`DOM`节点。

### 删除节点

当`oldVnode`和`vnode`完全不是同一个节点时，在`DOM`中需要使用`vnode`创建的新节点替换`oldVnode`所对应的旧节点，而替换过程是将新创建的`DOM`节点插入到旧节点的旁边然后再将旧节点删除，从而完成替换过程。

### 更新节点

对于**新增节点和删除节点**的场景，我们发现它们之间有一个**共同点**，那就是**两个虚拟节点是完全不同**的。另一个更**常见的场景是新旧两个节点是同一个节点。**当新旧两个节点是相同的节点时，我们需要**对两个节点进行细致比对，然后对`oldVnode`在视图中所对应的真实节点进行更新。**

例如：**当新旧两个节点是同一个文本节点，但两个节点的文本不一样时，我们需要重新设置`oldVnode`在视图中所对应的真实DOM节点的文本。**

![img](D:\Note\image\72BCE6EDA6D6F6E97ADCAFF10D37E29A.jpg)

### 小结

**通过前面的介绍，可以发现整个patch的过程并不复杂。当`oldVnode`不存在时,直接使用`vnode`渲染视图；当`oldVnode`和`vnode`都存在但并不是同一个节点时，使用`vnode`创建的`DOM`元素替换旧的DOM元素；当`oldVnode`和`vnode`是同一个节点时，使用更详细的对比操作对真实的DOM节点进行更新。**

![image-20200404115702452](D:\Note\image\image-20200404115702452.png)

## 创建节点

只有三种类型的节点会被创建并插入到`DOM`中：**文本节点、元素节点和注释节点**。注意**元素节点有`tag`属性，注释节点的`isComment`属性值为`true`**

**创建子节点的过程是一个递归过程。`vnode`中的属性保存了当前节点的所有子虚拟节点，所以只需要将`vnode`中的`children`属性循环一遍，将每个子虚拟节点都执行一遍创建元素的逻辑，就可以实现我们想要的功能。**

**创建子节点时，子节点的父节点就是当前刚创建出来的这个节点，所以节点被创建后，会被插人当前节点的下面。当所有子节点都创建完并插入到当前节点中之后，我们把当前节点插入到指定父节点的下面。如果这个指定的父节点已经被渲染到视图中，那么将当前这个节点插入进去之后，会将当前节点(包括其子节点)渲染到视图中。**

![image-20200404121724353](D:\Note\image\image-20200404121724353.png)

## 删除节点

`Vue.js`源码中使用`removeVnodes`来删除一组指定的节点

```javascript
function removeVnodes (vnodes, startIdx, endIdx) {
  for (; startIdx <= endIdx; ++startIdx) {
    const ch = vnodes[startIdx]
    if (isDef(ch)) {
      removeNode(ch.elm)
    }
  }
}
```

而`removeNode`用来删除视图中的单个节点

```javascript
function removeVnode(el) {
	const parent = nodeOps.parentNode(el)
  if (isDef(parent)) {
    nodeOps.removeChild(parent, el)
  }
}
```

> 考虑到跨平台渲染，我们对节点操作做了一层封装，其中`nodeOps`就是对节点操作的封装。

## 更新节点

**更新节点并不是很暴力地使用新节点覆盖旧节点，而是通过比对找出新旧两个节点不一样的地方，针对那些不一样的地方进行更新。**

### 静态节点

**直接跳过更新节点的过程**

### 新虚拟节点有文本节点

新虚拟节点有文本节点，并且和旧虚拟节点的文本属性不一样时，我们可以直接把视图中的真实DOM节点内容改成新虚拟节点的文本。

### 新虚拟节点无文本属性

* 有`children`的情况

  对新旧两个虚拟节点的`children`进行对比更新

* 无children的情况

  直接将`oldVnode`内容清空

![image-20191207162309841](D:\Note\image\image-20191207162309841.png)

## 更新子节点

分为四种操作：

* **更新节点**

  两个节点是同一个节点并且位置相同，这种情况下只需要进行更新节点的操作即可。

* **新增节点**

  如果在`oldchildren`中没有找到与本次循环所指向的新子节点相同的节点，那么说明本次循环所指向的新子节点是一个新增节点对于新增节点。对于新增节点，我们需要执行创建节点的操作，并将新创建的节点插入到`oldchildren`中所有**未处理节点**(未处理就是没有进行任何更新操作的节点)的**前面**。当节点成功插入DOM后，这一轮的循环就结束了。

* **删除节点**

  **本质上是删除那些`oldChildren`中存在但`newChildren`中不存在的节点。**

  当`newChildren`中的所有节点都被循环了一遍后，也就是循环结束后，如果`oldChildren`中还有剩余的没有被处理的节点，那么这些节点就是被废弃、需要删除的节点。

* **移动节点**

  **通过位置比对，然后用`Node.insertBefore()`方法将节点移动到所有未处理节点的前面。**节点更新并且移动完位置后，开始下一轮循环，也就是开始处理后面未处理节点。

## 优化策略

减少多次循环操作

名词介绍：

* **新前**：`newChildren`中所有未处理的第一个节点
* **新后**：`newChildren`中所有未处理的最后一个节点
* **旧前**：`oldChildren`中所有未处理的第一个节点
* **旧后**：`oldChildren`中所有未处理的最后一个节点

**查找方式：（依次进行）**

1. 新前与旧前

   “新前”与“旧前”节点进行对比，如果是同一个节点，那么只需要更新节点即可，如果不是同一个节点，再进行“新后”与“旧后”查找

2. 新后与旧后

   “新后”与“旧后”节点进行对比，如果是同一个节点，那么只需要更新节点即可，如果不是同一个节点，再进行“新后”与“旧前”查找2

3. 新后与旧前

   “新后”与“旧前”节点进行对比，如果是同一个节点，由于它们位置不同，所以除了更新节点之外，还需要执行移动节点操作（**将节点移动到`oldChildren`中所有未处理节点的最后面**），如果不是同一个节点，再进行“新前”与“旧后”查找

4. 新前与旧后

   “新后”与“旧前”节点进行对比，如果是同一个节点，由于它们位置不同，所以除了更新节点之外，还需要执行移动节点操作（**将节点移动到`oldChildren`中所有未处理节点的最前面**）

**如果前面这四种方式对比之后都没找到相同的节点，这是再通过循环的方式去`oldChildren`中详细找一遍，看看能否找到。**

## 哪些节点是未处理过的（待更新）

因为我们的逻辑都是在循环体内处理的，所以只要让循环条件保证只有未处理过的节点才能进入循环体内，就能达到忽略已处理过的节点从而只对未处理节点进行比对和更新操作。

# 七、模板编译原理

渲染函数是创建`HTML`最原始的方法。模板最终会通过编译转换成渲染函数，渲染函数执行后，会得到一份`vnode`用于虚拟`DOM`渲染，所以模板编译其实是配合虚拟`DOM`进行渲染。

大体逻辑上，模板编译分为三部分内容：

* **将模板解析为`AST`   ——  解析器（parse方法）**
* **遍历`AST`标记静态节点  ——  优化器（optimize方法）**
* **使用`AST`生成代码字符串（将`AST`转换成渲染函数中的内容）  ——  代码生成器（generate方法）**

## 解析器

**解析器要实现的功能是将模板解析成`AST`**

```html
<ul	:class="bindCls"	class="list"	v-if="isShow">				
  <li	v-for="(item,index)	in	data"	@click="clickItem(index)">{{item}}:{{index}}</ li> </ul> 
```

经过`parse`过程后，⽣成的`AST`如下：

```javascript
ast	=	{		
  'type':	1,		
  'tag':	'ul',		
  'attrsList':	[],		
  'attrsMap':	{				
    ':class':	'bindCls',				
    'class':	'list',				
    'v-if':	'isShow'		
  },		
  'if':	'isShow',		
  'ifConditions':	[{				
    'exp':	'isShow',				
    'block':	//	ul	ast	element		
  }],		
   'parent':	undefined,		
   'plain':	false,		
   'staticClass':	'list',	
   'classBinding':	'bindCls',		
   'children':	[{				
     'type':	1,				
     'tag':	'li',				
     'attrsList':	[{						
     'name':	'@click',						
     'value':	'clickItem(index)'				
    }],				
  'attrsMap':	{						
    '@click':	'clickItem(index)',						
    'v-for':	'(item,index)	in	data'					
  },				
  'parent':	//	ul	ast	element				
  'plain':	false,				
  'events':	{
    'click':	{								
      'value':	'clickItem(index)'						
    }				
  },				
  'hasBindings':	true,				
  'for':	'data',				
  'alias':	'item',				
  'iterator1':	'index',				
  'children':	[						
    'type':	2,						
    'expression':	'_s(item)+":"+_s(index)'						
    'text':	'{{item}}:{{index}}',						
    'tokens':	[								
      {'@binding':'item'},								
      ':',								
      {'@binding':'index'}						
    ]				
  ]		
}] 
}
```

### 解析器内部原理

事实上，解析器内部也分好几个子解析器，比如`HTML`解析器、文本解析器以及过滤解析器，其中最主要的是`HTML`解析器。

#### `HTML`解析器

伪代码如下：

```javascript
parseHTML(template, {
  start (tag, attrs, unary) { // tag: 标签名、attrs：标签属性、unary：是否自闭合标签
    // 每当解析到标签的开始位置时，触发该函数
    // 构建元素类型节点
  },
  end () {
    // 每当解析到标签的结束位置时，触发该函数
  },
  chars (text) { // text:文本
    // 每当解析到文本时，触发该函数
    // 构建文本类型节点
  },
  comment (text) { // text:文本
    // 每当解析到注释时，触发该函数
    // 构建注释类型节点
  }
})
```

`AST`是有层级关系的，一个`AST`节点具有父节点和子节点，因此我们需要一套逻辑来实现层级关系。

**构建`AST`层级关系非常简单，只需要用一个栈来记录层级关系即可，这个层级关系也可以理解为`DOM`的深度。基于`HTML`解析器的逻辑，我们可以在每次触发钩子函数`start`时，把当前正在构建的节点压入栈中即可；每当触发`end`钩子函数，就从栈中弹出一个节点。**

![image-20200404144819518](D:\Note\image\image-20200404144819518.png)

#### 文本解析器

**文本解析器是对`HTML`解析器解析出来的文本进行二次加工。**

**文本分为两种：**

* **纯文本**

  ```javascript
  Hello Berwin
  ```

* **带变量的文本**

  ```vue
  Hello{{ name }}
  ```

**HTML解析器在解析文本时，并不会区分文本是否是带变量的文本。如果是纯文本，不需要进行任何处理；但如果是带变量的文本，那么需要使用文本解析器进一步解析。因为带变量的文本在使用虚拟`DOM`进行渲染时，需要将变量替换成变量中的值。**

![image-20200404153446796](D:\Note\image\image-20200404153446796.png)

**`_s`其实是下面这个`toSting`函数的别名。**

```javascript
function toString (val) {
  return val == null
    ? ''
    : typeof val === 'object'
      ? JSON.stringify(val, null, 2)
  	  : String(val)
}
```

## 优化器

**优化器的作用是在`AST`中找出静态子树并打上标记。**

**标记静态子树的好处：**

* **每次重新渲染时，不需要为静态子树创建新节点**
* **在虚拟`DOM`中打补丁`（patching）`的过程可以跳过**

**优化器的内部实现：**

1. **在`AST`中找出所有静态节点并打上标记**
2. **在`AST`中找出所有静态根节点并打上标记**

### 静态节点和静态根节点

```html
<p>
  我是静态节点，我不需要发生变化
</p>
```

落实到`AST`中，**静态节点指的是`static`属性为`true`的节点**，例如：

```javascript
{
  type: 1,
  tag: 'p',
  staticRoot: false,
  static: true,
  ...
}
```

下面模板中，`ul`就是静态根节点

```html
<ul>
  <li>我是静态节点，我不需要发生变化</li>
  <li>我是静态节点2，我不需要发生变化</li>
  <li>我是静态节点3，我不需要发生变化</li>
</ul>
```

落实到`AST`中，**静态根节点指的是`staticRoot`属性为`true`的节点**，例如：

```javascript
{
  type: 1,
  tag: 'p',
  staticRoot: true,
  static: true,
  ...
}
```

### 标记静态节点

当模板被解析器解析成`AST`时，会根据不同元素类型设置不同的`type`值

| type的值 | 说明                 |
| -------- | -------------------- |
| 1        | 元素节点             |
| 2        | 带变量的动态文本节点 |
| 3        | 不带变量的纯文本节点 |

**通过`isStatic`函数来判断一个节点是否为静态节点**

```javascript
function isStatic (node: ASTNode): boolean {
  if (node.type === 2) { // 带变量的动态文本
    return false
  }
  if (node.type === 3) { // 不带变量的纯文本节点
    return true
  }
  return !!(node.pre || (
    !node.hasBindings && // no dynamic bindings
    !node.if && !node.for && // not v-if or v-for or v-else
    !isBuiltInTag(node.tag) && // not a built-in
    isPlatformReservedTag(node.tag) && // not a component
    !isDirectChildOfTemplateFor(node) &&
    Object.keys(node).every(isStaticKey)
  ))
}
```

> **如果元素节点使用了指令`v-pre`，那么可以直接断定它是一个静态节点**

**如果元素节点没有使用指令`v-pre`，那么它必须同时满足以下条件才会被认为是一个静态节点**

* 不能使用动态绑定语法，也就是说标签上不能有以`v-、@、：`开头的属性
* 不能使用`v-if、v-for或者v-else`指令
* 不能是内置标签，也就是说标签名不能是`solt或者template`
* 不能是组件
* 当前节点的父节点不能是带`v-for指令的template`标签
* 节点中不存在动态节点才会有的属性

**在子节点被打完标记之后，我们需要判断它是否为静态节点，如果不是，那么他的父节点也不可能是静态节点，此时需要将父节点的`static`属性设置为`false`。**

### 标记静态根节点

**通过`markStaticRoots`函数来标记静态根节点**

```javascript
function markStaticRoots (node: ASTNode, isInFor: boolean) {
  if (node.type === 1) {
    if (node.static || node.once) {
      // 标记static的或者有v-once指令同时处于for循环中的节点
      node.staticInFor = isInFor
    }

    // 一个static root节点必须有子节点否则它可能只是一个static的文本节点，而且它不能只有文本子节点
    if (node.static && node.children.length && !(
      node.children.length === 1 &&
      node.children[0].type === 3
    )) {
      node.staticRoot = true
      return
    } else {
      node.staticRoot = false
    }
    // 遍历子节点
    if (node.children) {
      for (let i = 0, l = node.children.length; i < l; i++) {
        markStaticRoots(node.children[i], isInFor || !!node.for)
      }
    }
    // ifConditions存储了if条件。
    // 是一个数组，格式为[{exp: xxx, block:xxx}, {exp: xxx, block:xxx}, {exp: xxx, block:xxx}]
    // block存储了element，exp存储了表达式。
    if (node.ifConditions) {
      for (let i = 1, l = node.ifConditions.length; i < l; i++) {
        markStaticRoots(node.ifConditions[i].block, isInFor)
      }
    }
  }
}
```

**我们从上往下找，找到的第一个静态节点一定是静态根节点，而它的所有子节点一定也是静态节点。**

![image-20200404161703015](D:\Note\image\image-20200404161703015.png)

**静态根节点至少要有一个子节点，并且该子节点不能是静态文本，否则优化成本将超过收益。**

## 代码生成器

**代码生成器的作用是将AST转换成渲染函数中的内容，这个内容可以称为代码字符串**

**代码字符串可以被包装在函数中执行，这个函数就是我们通常说的渲染函数。**

生成的代码字符串是这样的：

```javascript
'with(this){return _c("div", {attrs:{"id":"el"}},[_v("Hello "+_s(name))])}'

// 格式化之后
with (this) {
  return _c(
    "div", 
    {
      attrs:{"id":"el"}
    },
    [
      _v("Hello "+_s(name))
    ]
  )
}
```

代码字符串中的**`_c`函数实际上是`createElement`的别名**，`createElement`是虚拟`DOM`提供的方式，它的**作用是创建虚拟节点**。有三个参数，分别是：

* 标签名
* 一个包含模板相关属性的数据对象
* 子节点列表

### 生成代码字符串

**生成代码字符串是一个递归的过程，从顶向下依次处理每一个`AST`节点**

| 类型     | 创建方法        | 别名 |
| -------- | --------------- | ---- |
| 元素节点 | createElement   | _c   |
| 文本节点 | createTextVNode | _v   |
| 注释节点 | createEmptyNode | _e   |

> **代码生成器其实就是字符串拼接的过程。通过递归`AST`来生成字符串，最先生成根节点，然后在子节点字符串生成后，将其拼接在根节点的参数中，子节点的子节点拼接在子节点的参数中，这样一层一层地拼接，直到最后拼接成完整的字符串。当字符串拼接好之后，会将字符串拼在`with`中返回给调用者。**

# 八、实例方法的实现原理

##  数据相关的实例方法

**与数据相关的实例方法有3个，分别是`vm.$watch、vm.$set、vm.$delete`。它们是在`stateMixin`中挂载到`Vue`的原型上的。**

## 事件相关的实例方法

**与事件相关的实例方法有4个，分别是`vm.$on、vm.$once、vm.$off和vm.$emit`。它们是在`eventsMixin`中挂载到`Vue`的原型上的。**

### `vm.$on`

#### 用法

**监听当前实例上的自定义事件，事件可以由`vm.$emit`触发。回调函数会接收所有传入事件所触发的函数的额外参数。**

```javascript
vm.$on(event, callback)

// 示例
vm.$on('test', function (msg) {
  console.log(msg)
})

vm.$emit('test', 'hi')
```

#### 参数

* `{string | Array<string>} event`
* `{Function} callback`

#### 内部原理

```javascript
  Vue.prototype.$on = function (event: string | Array<string>, fn: Function): Component {
    const vm: Component = this
    // 当event参数为数组时，遍历数组，将其中的每一项递归调用vm.$on
    if (Array.isArray(event)) {
      for (let i = 0, l = event.length; i < l; i++) {
        vm.$on(event[i], fn)
      }
    } else {
      // vm.$_events是一个对象，用来存储事件，我们使用事件名（event）从vm.$_envent中取出事件列           // 表，如果列表不存在，则使用空数组初始化，然后再将回调函数添加到事件列表中。
      (vm._events[event] || (vm._events[event] = [])).push(fn)
      // optimize hook:event cost by using a boolean flag marked at registration
      // instead of a hash lookup
      if (hookRE.test(event)) {
        vm._hasHookEvent = true
      }
    }
    return vm
  }
```

**当event参数为数组时，遍历数组，将其中的每一项递归调用`vm.$on`，使回调可以被注册到`vm.$_events`对象中每项事件名所对应的事件列表中。通俗地讲，就是将回调注册到事件列表中。**

**`vm.$_events`是一个对象，用来存储事件，我们使用事件名`（event）从vm.$_envent`中取出事件列表，如果列表不存在，则使用空数组初始化，然后再将回调函数添加到事件列表中。**

```javascript
vm.$_events = Object.create(null);
```

### `vm.$off`

#### 用法

**移除自定义事件监听器**

```javascript
vm.$off(event, callback)
```

#### 参数

* `{string | Array<string>} event`
* `{Function} callback`

#### 内部原理

```javascript
Vue.prototype.$off = function (event?: string | Array<string>, fn?: Function): Component {
  const vm: Component = this
  // 不传参数，将所有事件清空
  if (!arguments.length) {
    vm._events = Object.create(null)
    return vm
  }
  // 当event参数为数组时，遍历数组，将其中的每一项递归调用vm.$off
  if (Array.isArray(event)) {
    for (let i = 0, l = event.length; i < l; i++) {
      vm.$off(event[i], fn)
    }
    return vm
  }
  // 从vm.$_events中取出事件列表
  const cbs = vm._events[event]
  // 如果不存在，则直接返回实例
  if (!cbs) {
    return vm
  }
  // 只传第一个参数（没有传入fn），则将_events对象下的该属性事件数组清空
  if (!fn) {
    vm._events[event] = null
    return vm
  }
  let cb
  let i = cbs.length
  // 在cbs列表中找到与参数中提供的回调函数相同的那个函数，并将它从列表中移除
  while (i--) {
    cb = cbs[i]
    // cb.fn === fn为vm.$once方法提供服务
    if (cb === fn || cb.fn === fn) {
      cbs.splice(i, 1)
      break
    }
  }
  return vm
}
```

**如果不传参数，则将所有事件清空，当`event`参数为数组时，遍历数组，将其中的每一项递归调用`vm.$off`。然后从`vm.$_events`中取出事件列表，如果只传第一个参数（没有传入`fn`），则将`_events`对象下的该属性事件数组清空。如果`event和fn`都传入了，那么在`cbs`列表中找到与参数中提供的回调函数相同的那个函数，并将它从列表中移除。**

### `vm.$once`

#### 用法

**监听一个自定义事件，但是只触发一次，在第一次触发之后移除监听器。**

```javascript
vm.$off(event, callback)
```

#### 参数

* `{string | Array<string>} event`
* `{Function} callback`

#### 内部原理

```JavaScript
  Vue.prototype.$once = function (event: string, fn: Function): Component {
    const vm: Component = this
    function on () {
      // 只要事件一触发，便移除监听器
      vm.$off(event, on)
      // 执行fn回调函数，并将参数arguments传递给函数fn
      fn.apply(vm, arguments)
    }
    // 保存fn函数到on函数上，以便于当我们使用拦截器代替监听器注入到时间列表中，移除监听器时操作有效
    // （因为拦截器和用户提供的函数是不相同的，此时我们可以找寻fn属性来执行移除操作）
    /* 
        function handle () {
          // ...
        }
        比如：this.$once('handle', handle); (内部使用的是vm.$on(event, on))
              当想要清除监听器时，我们可以使用
              this.$off('handle', handle)
              内部通过cb.fn === fn（即on.handle === handle）来判断清除监听器
    */
    on.fn = fn
    // on函数是对fn函数做一层包装（除了要执行相应的处理函数之外，还要对监听器进行移除）
    vm.$on(event, on)
    return vm
  }
```

**首先，定义函数`on`，然后将回调函数`fn作为on`函数的静态属性，再将函数on注册到事件中。当自定义事件被触发时，会先执行函数`on`（在这个函数中，会使用`$off(event, on)`将自定义事件移除），然后手动执行函数`fn`，并将参数`arguments传递给函数fn`，这就可以实现`vm.$once`的功能。**

### `vm.$emit`

#### 用法

**触发当前实例上的事件。附加参数都会传给监听器回调。**

```javascript
vm.$emit(event, [...args])
```

#### 参数

* `{string} event`
* `{...args}`

#### 内部原理

```javascript
  Vue.prototype.$emit = function (event: string): Component {
    const vm: Component = this
    if (process.env.NODE_ENV !== 'production') {
      const lowerCaseEvent = event.toLowerCase()
      if (lowerCaseEvent !== event && vm._events[lowerCaseEvent]) {
        tip(
          `Event "${lowerCaseEvent}" is emitted in component ` +
          `${formatComponentName(vm)} but the handler is registered for "${event}". ` +
          `Note that HTML attributes are case-insensitive and you cannot use ` +
          `v-on to listen to camelCase events when using in-DOM templates. ` +
          `You should probably use "${hyphenate(event)}" instead of "${event}".`
        )
      }
    }
    // 从_events中取出相应的事件监听器回调函数列表
    let cbs = vm._events[event]
    if (cbs) {
      // toArray的作用：将类数组转换成真正的数组
      cbs = cbs.length > 1 ? toArray(cbs) : cbs
      // toArray的第二个参数是指起始位置，也就是说args是一个数组，里面包含除第一个参数之外的所有参数
      const args = toArray(arguments, 1)
      const info = `event handler for "${event}"`
      for (let i = 0, l = cbs.length; i < l; i++) {
        // 执行cbs中的回调函数并将参数传递给监听回调，只不过其中使用了try...catch语句来捕获错误
        invokeWithErrorHandling(cbs[i], vm, args, vm, info)
      }
    }
    return vm
  }
```

**首先从`vm.$_events`中取出相应的事件监听器回调函数列表`cbs`，然后对`cbs`存在，则循环它，依次调用每一个监听器回调并将所有参数传给监听回调**

## 生命周期相关的实例方法

**与生命周期相关的实例方法有4个，分别是`vm.$mount、vm.$forceUpdate、vm.$nextTick和vm.$destroy`。其中`vm.$forceUpdate和vm.$destroy`是从`lifecycleMixin`中挂载到`Vue`的原型上的。`vm.$nextTick是从renderMixin中挂载到Vue原型上的。vm.$mount`方法则是在跨平台的代码中挂载到`Vue`的原型上的。**

### `vm.$forceUpdate`

**`vm.$forceUpdate`的作用是迫使`Vue.js`实例重新渲染。注意它仅仅影响实例本身以及插入插槽内容的子组件，而不是所有子组件。**

#### 内部原理

```javascript
  Vue.prototype.$forceUpdate = function () {
    const vm: Component = this
    // vm.watcher就是Vue.js实例的watcher，
    // 手动执行实例watcher的update方法，就可以使Vue.js实例重新渲染
    if (vm._watcher) {
      vm._watcher.update()
    }
  }
```

**手动执行实例`watcher的update`方法，就可以使`Vue.js`实例重新渲染**

### `vm.$destroy`

**`vm.$destroy`的作用是完全销毁一个实例，它会清理该实例与其他实例的连接，并解绑其全部指令以及监听器，同时会触发`beforeDestroy和destroyed`的钩子函数。**

> 该方法并不常用，大部分场景下并不需要销毁组件，只需要使用`v-if或v-for`等指令以数据驱动的方式控制子组件的生命周期即可。

#### 内部原理

```javascript
  Vue.prototype.$destroy = function () {
    const vm: Component = this
    // 如果_isBeingDestroyed为true，说明实例正在被销毁，直接使用return语句退出函数，避免反复销毁
    if (vm._isBeingDestroyed) {
      return
    }
    // 触发beforeDestroy钩子函数
    callHook(vm, 'beforeDestroy')
    // 设置_isBeingDestroyed为true，避免重复销毁
    vm._isBeingDestroyed = true
    // 清除当前组件与父组件之间的连接
    // 拿到父组件
    const parent = vm.$parent
    if (parent && !parent._isBeingDestroyed && !vm.$options.abstract) {
      // 如果父组件存在，且未被销毁，则将当前组件实例从父组件实例的$children属性中删除
      remove(parent.$children, vm)
    }
    if (vm._watcher) {
      // 销毁Vue实例的watcher
      vm._watcher.teardown()
    }
    // 将组件下的所有Watcher释放（vm._watchers在initState中定义）
    let i = vm._watchers.length
    while (i--) {
      // teardown方法会使watcher从vm._watchers和它收集的deps中移除
      vm._watchers[i].teardown()
    }
    // 移除__ob__属性
    if (vm._data.__ob__) {
      vm._data.__ob__.vmCount--
    }
    // 给通过给实例添加_isDestroyed属性来表示实例已被销毁
    vm._isDestroyed = true
    // 将模板中的所有指令解绑
    vm.__patch__(vm._vnode, null)
    // 调用destroyed钩子函数
    callHook(vm, 'destroyed')
    // 移除所有的事件监听
    vm.$off()
    // remove __vue__ reference
    if (vm.$el) {
      vm.$el.__vue__ = null
    }
    // release circular reference (#6759)
    if (vm.$vnode) {
      vm.$vnode.parent = null
    }
  }
```

首先判断如果`_isBeingDestroyed`为`true`，说明实例正在被销毁，直接使用`return`语句退出函数，避免反复销毁，否则触发`beforeDestroy`钩子函数，然后设置`_isBeingDestroyed为true`，避免重复销毁，再清除当前组件与父组件之间的连接（将当前组件实例从父组件实例的`$children`属性中删除）,将组件下的所有`Watcher`释放,移除`__ob__`属性，再通过给实例添加`_isDestroyed`属性来表示实例已被销毁，再将模板中的所有指令解绑，调用`destroyed`钩子函数，移除所有的事件监听。

### `vm.$nextTick`（待更新）

**`nextTick`接收一个回调函数作为参数，它的作用是将回调延迟到下次`DOM`更新周期之后再执行。它与全局方法`Vue.nextTick`方法一样，不同的是回调的`this`自动绑定到调用它的实例上。**

#### 示例：

```javascript
new Vue({
  // ...
  methods: {
    // ...
    example: function () {
      // 修改数据
      this.message = 'changed'
      // DOM还没有更新
      this.$nextTick(function () {
        // DOM现在更新了
        // this绑定到当前实例
        this.doSomethingElse()
      })
    }
  }
})
```

在`Vue.js`中，当状态发生变化时，`watcher`会得到通知，然后触发虚拟`DOM`的渲染流程。而`watcher`触发渲染这个操作**并不是同步的，而是异步的**。**`Vue.js`中有一个队列，**每当需要渲染时，**会将`watcher`推送到队列中**，在**下一次事件循环中再让`watcher`触发渲染的过程。**

#### 异步更新队列

我们知道`Vue.js2.0`开始使用虚拟DOM进行渲染，变化侦测的通知只发送到组件，组件内用到的所有状态的变化都会通知到同一个 `watcher`，然后虚拟`DOM`会对整个组件进行“`比对(diff)`”并更改`DOM`。也就是说，如果在同一轮事件循环中有两个数据发生了变化，那么组件的`watcher`会收到两份通知，从而进行两次渲染。事实上,并不需要渲染两次，虚拟`DOM`会对整个组件进行渲染，所以只需要等所有状态都修改完毕后，一次性将整个组件的`DOM`渲染到最新即可。
要解决这个问题， `Vue. js`的实现方式是**将收到通知的 `watcher`实例添加到队列中缓存起来，并且在添加到队列之前检查其中是否已经存在相同的 `watcher`，只有不存在时，才将 `watcher`实例添加到队列中。然后在下一次事件循环`(event loop)`中，`Vue.js`会让队列中的 `watcher`触发渲染流程并清空队列。**这样就可以保证即便在同一事件循环中有两个状态发生改变，`watcher`最后也只执行一次渲染流程。

#### 什么是事件循环

`JavaScript`是一门单线程且非阻塞的脚本语言，这意味着 `Javascript`的任何时候都只有一个主线程来处理所有任务。而非阻塞是指当代码需要处理异步任务时，主线程会挂起`(pending)`这个任务，当异步任务处理完毕后，主线程再根据一定规则去执行相应回调。

事实上，当任务处理完毕后，`JavaScript`会将这个事件加入一个队列中，我们称这个队列为**回调事件队列**。被放人事件队列中的事件不会立刻执行其回调，而是等待当前找行栈中的所有任务执行完毕后，主线程会去查找事件队列中是否有任务。

异步任务有两种类型:**微任务**( `microtask`)和**宏任务**( `macrotask`)。不同类型的任务会被分配到不同的任务队列中。

当**执行栈**中的所有任务都执行完毕后，会去检查微任务队列中是否有事件存在，如果存在，则会依次执行微任务队列中事件对应的回调，直到为空。然后去宏任务队列中取出一个事件，把对应的回调加入当前执行栈，当执行栈中的所有任务都执行完毕后，检查微任务队列中是否有事件存在。无限重复此过程,就形成了一个无限循环，这个循环就叫作**事件循环**。

#### 什么是执行栈

**当我们执行一个方法时，`JavaScript`会生成一个与这个方法对应的执行环境`（context）`，又叫执行上下文。这个执行环境中有这个方法的私有作用域、上层作用域的指向、方法的参数、私有作用域中定义的变量以及`this`对象。这个执行环境会被添加到一个栈中，这个栈就是执行找。**

#### 内部原理

### `vm.$mount`

并不常用这个方法，其原因是如果在实例化`Vue.js`设置了`el`选项，会自动把`Vue.js`实例挂载到`DOM`元素上。但是其内部实现原理十分重要。

#### 内部原理

```javascript
// 缓存了runtime中的的$mount方法（缓存了原型上的$mount⽅法，再重新定义该⽅法）
const mount = Vue.prototype.$mount
Vue.prototype.$mount = function (
  // 可以传入字符串el或者直接传入元素
  el?: string | Element,
 // hydrating跟服务端渲染有关，在浏览器环境下，可以不传第二个参数
  hydrating?: boolean
): Component {
  // 如果el存在，就调用query方法对el进行转换，返回dom对象赋给el
  el = el && query(el)

  // 对el做了限制，因为Vue实例不能挂载在body、html这样的根节点上，否则会报错
  if (el === document.body || el === document.documentElement) {
    process.env.NODE_ENV !== 'production' && warn(
      `Do not mount Vue to <html> or <body> - mount to normal elements instead.`
    )
    return this
  }
  // 拿到用户传递进来的配置options
  const options = this.$options
  // 判断用户是否有写render方法，当没有直接使用render函数时，通过解析template/el并转换为呈现函数
  if (!options.render) {
    let template = options.template
    // 判断是否定义了template
    if (template) {
      if (typeof template === 'string') {
        // 当template是以"#tmp1"这样的形式存在时，
        // 通过idToTemplate方法（内部再次使用了query(el)方法）去获取template组件
        if (template.charAt(0) === '#') {
          template = idToTemplate(template)
          // 当没有找到对应的元素时，会进行报错
          if (process.env.NODE_ENV !== 'production' && !template) {
            warn(
              `Template element not found or is empty: ${options.template}`,
              this
            )
          }
        } 
      } else if (template.nodeType) {
        // 当template为DOM节点的时候，拿到template的innerHTML
        template = template.innerHTML
      } else {
        if (process.env.NODE_ENV !== 'production') {
          warn('invalid template option:' + template, this)
        }
        return this
      }
    } else if (el) { 
      // 当没有定义template时，通过el去获取el的outerHTML返回给template
      template = getOuterHTML(el)
    }
    // 通过上面转换之后得到的template来得到render方法
    if (template) {
      /* istanbul ignore if */
      if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
        mark('compile')
      }
      // 将template编译成render函数，这里会有render以及staticRenderFns两个返回，
      // 这是vue的编译时优化，static静态不需要在VNode更新时进行patch，优化性能
      const { render, staticRenderFns } = compileToFunctions(template, {
        outputSourceRange: process.env.NODE_ENV !== 'production',
        shouldDecodeNewlines,
        shouldDecodeNewlinesForHref,
        delimiters: options.delimiters,
        comments: options.comments
      }, this)
      // 生成render方法和staticRenderFns,放到options身上，方便后续使用
      options.render = render
      options.staticRenderFns = staticRenderFns

      /* istanbul ignore if */
      if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
        mark('compile end')
        measure(`vue ${this._name} compile`, 'compile', 'compile end')
      }
    }
  }
  // 调用const mount = Vue.prototype.$mount保存下来的不带编译的mount方法
  // 也就是执行runtime中的$mount方法
  return mount.call(this, el, hydrating)
}
```

```javascript
// runtime only版本

// 组件挂载方法，传入两个参数el和hydrating
Vue.prototype.$mount = function (
  // el可以是一个字符串，也可以是一个DOM对象
  el?: string | Element,
  // hydrating跟服务端渲染有关，在浏览器环境下，可以不传第二个参数
  hydrating?: boolean
): Component {
  // 获取DOM实例对象（注意，考虑到runtime only版本，此处需要对el进行再次判断，以获得dom实例对象）
  el = el && inBrowser ? query(el) : undefined
  // 通过mountComponent挂载组件
  return mountComponent(this, el, hydrating)
}
```

```javascript
// mountComponent核⼼就是先定义一个updateComponent方法，再实例化⼀个渲染Watcher，并将
// updateComponent方法作为回调传入，在实例化watcher过程中触发回调，执行updateComponent函数，
// updateComponent函数中会调⽤vm._render⽅法先⽣成虚拟Node，最终调⽤vm._update更新DOM
export function mountComponent (
  vm: Component,
  el: ?Element,
  hydrating?: boolean
): Component {
  // 对传入的el做缓存
  vm.$el = el
  // 接下来进行一系列的校验
  if (!vm.$options.render) {
    // 经过前面的转换，render函数依然不存在时，就会创建一个空的VNode
    vm.$options.render = createEmptyVNode
    if (process.env.NODE_ENV !== 'production') {
      if ((vm.$options.template && vm.$options.template.charAt(0) !== '#') ||
        vm.$options.el || el) {
          // 在开发环境下，当我们使用了RuntimeOnly版本的Vue时，没有写render函数，而是使用template
          // 就会报警告（因此在RuntimeOnly版本下，不会对template进行编译生成render函数）
        warn(
          'You are using the runtime-only build of Vue where the template ' +
          'compiler is not available. Either pre-compile the templates into ' +
          'render functions, or use the compiler-included build.',
          vm
        )
      } else {
        warn(  // 既没有定义template也没有定义render函数，也会进行报警告
          'Failed to mount component: template or render function not defined.',
          vm
        )
      }
    }
  }
  // 执行beforeMount钩子函数（因此模板编译是在created和beforeMount生命周期之间）
  callHook(vm, 'beforeMount')
  // 声明updateComponent变量，方便在实例化一个Watcher的时候，在它的回调函数中调⽤updateComponent⽅法
  let updateComponent
  /* istanbul ignore if */
  if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
    // 	定义updateComponent⽅法
    updateComponent = () => {
      const name = vm._name
      const id = vm._uid
      const startTag = `vue-perf-start:${id}`
      const endTag = `vue-perf-end:${id}`

      mark(startTag)
      // 生成vnode
      const vnode = vm._render()
      mark(endTag)
      measure(`vue ${name} render`, startTag, endTag)

      mark(startTag)
      vm._update(vnode, hydrating)
      mark(endTag)
      measure(`vue ${name} patch`, startTag, endTag)
    }
  } else {
    // 	定义updateComponent⽅法
    updateComponent = () => {
      // 执行vue._render()生成虚拟dom（VNode），
      // 再将其传入vm._update，_update 的核心就是调用 vm.__patch__ 方法，渲染成真实dom
      vm._update(vm._render(), hydrating)
    }
  }

  // 这里对该实例注册一个Watcher实例，Watcher的getter为updateComponent函数，
  // 用于触发所有渲染所需要用到的数据的getter，
  // 进行依赖收集，该Watcher实例会存在所有渲染所需数据的闭包Dep中
  // 此处的Watcher为渲染Watcher（除了渲染watcher之外，还存在computed watcher、user watcher等等）

  // Watcher在这⾥起到两个作⽤，⼀个是初始化的时候会执⾏回调函数，
  // 另⼀个是当vm实例中的监测的数据发⽣变化的时候执⾏回调函数
  new Watcher(vm, updateComponent, noop, {
    before () {
      // 如果已经该组件已经挂载过了则代表进入这个步骤是个更新的过程，触发beforeUpdate钩子
      if (vm._isMounted && !vm._isDestroyed) {
        callHook(vm, 'beforeUpdate')
      }
    }
  }, true /* isRenderWatcher */)
  hydrating = false

  // manually mounted instance, call mounted on self
  // mounted is called for render-created child components in its inserted hook
  if (vm.$vnode == null) {
    // 标志位，代表该组件已经挂载
    vm._isMounted = true
    // 组件挂载完成之后，执行mounted钩子函数
    callHook(vm, 'mounted')
  }
  return vm
}
```



# 八、全局`API`的实现原理

## `Vue.extend`

### 用法

**使用基础`Vue`构造器创建一个"子类"，其参数是一个包含”组件选项“的对象**

```javascript
Vue.extend(options)
```

`data`选项是特例，在`Vue.extend()`中，它必须是函数

```html
<div id="mount-point"></div>
```

```javascript
// 创建构造器
var Profile = Vue.extend({
  template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
  data: function () {
    return {
      firstName: 'Walter',
      lastName: 'White',
      alias: 'Heisenberg'
    }
  }
})
// 创建 Profile 实例，并挂载到一个元素上。
new Profile().$mount('#mount-point')
```

```html
// 渲染结果
<p>Walter White aka Heisenberg</p>
```

### 参数

* `{Object} options`

### 内部原理（待更新）

## `Vue.nextTick`

### 用法

**在下次`DOM`更新循环结束之后执行延迟回调，修改数据之后立即使用这个方法获取更新后的`DOM`**

```javascript
Vue.nextTick([callback, context])
```

```javascript
// 修改数据
vm.msg = 'Hello'
// DOM 还没有更新
Vue.nextTick(function () {
  // DOM 更新了
})

// 作为一个 Promise 使用 (2.1.0 起新增，详见接下来的提示)
Vue.nextTick()
  .then(function () {
    // DOM 更新了
  })
```

### 参数

* `{Function}[callback]`
* `{Object}[context]`

### 内部原理

`Vue.nextTick与vm.$nextTick`实现原理相同

```javascript
import { nextTick } from '../util/index'

Vue.nextTick = nextTick
```

## `Vue.set`

`Vue.set`为解决无法侦测object什么时候被添加了一个新属性而存在。

### 用法

在object上设置一个属性，如果object是响应式，`Vue.js`会保证属性被创建后也是响应式的，并且触发视图更新。

```javascript
vm.$set(target, key, value) 
```

> target不能是`Vue.js`实例或者`Vue.js`实例的根数据对象（this.$data）

### 参数

- `{Object | Array} target`
- `{string | number} propertyName/index`
- `{any} value`

### 内部原理

`Vue.set与vm.$set`实现原理相同

```javascript
import { set } from '../observer/index'
Vue.set = set
```

## `Vue.delete`

删除数据中的某个属性。

### 用法

```javascript
vm.$delete(target, key)
```

> 目标对象不能是`Vue.js`实例或者`Vue.js`实例的根数据对象（this.$data）

### 参数

- `{Object | Array} target`
- `{string | number} propertyName/index`

### 内部原理

`Vue.delete与vm.$delete`实现原理相同

```javascript
import { delete } from '../observer/index'
Vue.delete = delete
```

## `Vue.directive`

### 用法

注册或获取全局指令。

```javascript
// 注册
Vue.directive('my-directive', {
  bind: function () {},
  inserted: function () {},
  update: function () {},
  componentUpdated: function () {},
  unbind: function () {}
})

// 注册 (指令函数)
Vue.directive('my-directive', function () {
  // 这里将会被 `bind` 和 `update` 调用
})

// getter，返回已注册的指令
var myDirective = Vue.directive('my-directive')
```

### 参数

- `{string} id`
- `{Function | Object} [definition]`

### 内部原理

```javascript
// 用于保存指令的位置
Vue.options = Object.create(null)
Vue.options['directives'] = Object.create(null)

Vue.directives = function (id, definition) {
  if (!definition) {
    return this.options['directives'][id]
  } else {
    if (typeof definition === 'function') {
      definition = { bind: definition, update: definition}
    }
    this.options['directives'][id] = definition
    return definition
  }
}
```

​       `Vue. directive`方法接收两个参数`id和definition`，它可以注册或获取指令，这取决于`definition`参数是否存在。如果 `definition`参数不存在，则使用`id`从`this. options ['directives']`中读出指令并将它返回；如果 `definition`参数存在，则说明是注册操作，那么进而判断 `definition`参数的类型是否是函数。
​       如果是函数，则默认监听`bind和 update`两个事件，所以代码中将 `definition`函数分别赋值给对象中的`bind和 update`这两个方法，并使用这个对象覆盖 `definition`；如果 `definition`不是函数,则说明它是用户自定义的指令对象，此时不需要做任何操作，直接将用户提供的指令对象保存在`this.options['directives']`上即可。

## `Vue.filter`

### **用法**：

注册或获取全局过滤器。

```javascript
// 注册
Vue.filter('my-filter', function (value) {
  // 返回处理后的值
})

// getter，返回已注册的过滤器
var myFilter = Vue.filter('my-filter')
```

```html
<!-- 在双花括号中 -->
{{ message | my-filter }}

<!-- 在 `v-bind` 中 -->
<div v-bind:id="rawId | formatId"></div>
```

### **参数**：

- `{string} id`
- `{Function} [definition]`

### 内部原理（待更新）

## `Vue.component`

### **用法**：

注册或获取全局组件。注册还会自动使用给定的`id`设置组件的名称

```javascript
// 注册组件，传入一个扩展过的构造器
Vue.component('my-component', Vue.extend({ /* ... */ }))

// 注册组件，传入一个选项对象 (自动调用 Vue.extend)
Vue.component('my-component', { /* ... */ })

// 获取注册的组件 (始终返回构造器)
var MyComponent = Vue.component('my-component')
```

### 参数

- `{string} id`
- `{Function | Object} [definition]`

### 内部原理（待更新）

## `Vue.use`

### 用法

**安装 `Vue.js` 插件。如果插件是一个对象，必须提供 `install` 方法。如果插件是一个函数，它会被作为 `install` 方法。`install` 方法调用时，会将 `Vue` 作为参数传入。**

**该方法需要在调用 `new Vue()` 之前被调用。**

**当 `install` 方法被同一个插件多次调用，插件将只会被安装一次。**

### **参数**：

- `{Object | Function} plugin`

### 内部原理

```javascript
  Vue.use = function (plugin: Function | Object) {
    const installedPlugins = (this._installedPlugins || (this._installedPlugins = []))
    // 如果该plugin已经存在于installedPlugins中了，为避免重复注册，则立即终止函数执行
    if (installedPlugins.indexOf(plugin) > -1) {
      return this
    }

    // 利用toArray方法：将除了第一个参数之外，剩余的所有参数得到的列表赋给args
    const args = toArray(arguments, 1)
    // 将Vue添加到args列表最前面
    args.unshift(this)
    // 判断plugin和plugin.install哪个是函数，即可得知用户使用哪种方式注册插件，然后执行用户编写的插件
    // 并将args作为参数传入（注意args的第一个参数为Vue）
    if (typeof plugin.install === 'function') {
      plugin.install.apply(plugin, args)
    } else if (typeof plugin === 'function') {
      plugin.apply(null, args)
    }
    // 将插件添加到installedPlugins中，保证相同插件不会反复被执行
    installedPlugins.push(plugin)
    return this
  }
```

**在该方法中，首先判断插件是否已经注册过，如果注册过，则直接终止方法执行，此时只需要使用`indexOf`方法即可。**

**接下来,使用 `toArray`方法得到 `arguments`。除了第一个参数之外，剩余的所有参数将得到的列表赋值给`args`，然后将`Vue`添加到`args`列表的最前面。这样做的目的是保证 `install`方法被执行时第一个参数是`Vue,`其余参数是注册插件时传入的参数。**
**由于 `plugin`参数支持对象和函数类型,所以通过判断 `plugin.Install和 plugin`哪个是函数，即可得知用户使用哪种方式注册的插件，然后执行用户编写的插件并将`ags`作为参数传入。**
**最后,将插件添加到 `installedPlugins`中,保证相同的插件不会反复被注册。**

## `Vue.mixin`

### **用法**：

全局注册一个混入，影响注册之后所有创建的每个 `Vue` 实例。插件作者可以使用混入，向组件注入自定义的行为。**不推荐在应用代码中使用**。

### **参数**：

- `{Object} mixin`

### 内部原理

```JavaScript
  Vue.mixin = function (mixin: Object) {
    // 将用户传入的mixin与this.options合并成一个新对象，然后赋给this.options
    this.options = mergeOptions(this.options, mixin)
    return this
  }
```

**`mergeOptions`方法会将用户传入的`mixin与this.options`合并成一个新对象，然后赋给`this.options`。这里的`this.options其实就是Vue.options`。**

## `Vue.compile`

### **用法**：

将一个模板字符串编译成 `render` 函数。**只在完整版时可用**。

```javascript
var res = Vue.compile('<div><span>{{ msg }}</span></div>')

new Vue({
  data: {
    msg: 'hello'
  },
  render: res.render,
  staticRenderFns: res.staticRenderFns
})
```

### **参数**：

- `{string} template`

### 内部原理

与`Vue.$mout`类似，`Vue.compile`只存在于完整版中。`Vue.compile`方法只需要调用编译器就可以实现功能。

```javascript
Vue.compile = compileToFunctions
```

## `Vue.version`

### **用法**：

```javascript
var version = Number(Vue.version.split('.')[0])

if (version === 2) {
  // Vue v2.x.x
} else if (version === 1) {
  // Vue v1.x.x
} else {
  // Unsupported versions of Vue
}
```

### 细节

提供字符串形式的 `Vue` 安装版本号。这对社区的插件和组件来说非常有用，你可以根据不同的版本号采取不同的策略。

### 实现原理

`Vue.js`在构建文件的配置中定义了`__VERSION__`常量，使用`rollup-plugin-replace`插件在构建过程中将代码中的常量`__VERSION__`替换成`package.json`文件中的版本号。

```javascript
Vue.version = '__VERSION__'
```

构建完之后，它将类似下面这样

```javascript
Vue.version = '2.6.1'
```

# 九、生命周期

![image-20200407091443320](D:\Note\image\image-20200407091443320.png)

![image-20200407091451002](D:\Note\image\image-20200407091451002.png)

## 初始化阶段

**`new Vue()到created`之间的阶段叫作初始化阶段**

**这个阶段的主要目的是在`Vue.js`实例上初始化一些属性、事件以及响应式数据，如`props、methods、data、computed、watch、provide和inject等`**

## 模板编译阶段

**在`created`钩子函数到`beforeMount`钩子函数之间的阶段是模板编译阶段**

**将模板编译为`render`函数**

## 挂载阶段

**在这个阶段，`Vue.js`会将其实例挂载到DOM元素上，通俗地讲，就是将模板渲染到指定的`DOM`元素中。在挂载的过程中，`Vue.js会开启watcher`来持续追踪依赖的变化。**

**在已挂载的状态下，`Vue.js`仍会持续追踪状态变化，当数据（状态）发生变化时会通知虚拟`DOM`重新渲染视图，并且会在渲染视图前触发`beforeUpdata`钩子函数，渲染结束之后触发`updated`钩子函数。这也就是我们常说的响应式。这个状态会持续到组件被销毁。**

## 卸载阶段

**在这个阶段，`Vue.js`会将自身从父组件中删除，取消实例上所有依赖的追踪并且移除所有的事件监听器。**

## `inject`的内部原理

```javascript
export function initInjections (vm: Component) {
  // resolveInject函数的主要思想：读出用户在当前组件中设置的inject的key，然后循环key，
  // 将每一个key从当前组件起，不断向父组件查找是否有值，找到了就停止循环，最终将所有key对应的值一起返回
  const result = resolveInject(vm.$options.inject, vm)
  if (result) {
    toggleObserving(false)
    Object.keys(result).forEach(key => {
      if (process.env.NODE_ENV !== 'production') {
        // 循环result并依次调用defineReactive函数，将它们设置到Vue.js实例上。
        defineReactive(vm, key, result[key], () => {
          warn(
            `Avoid mutating an injected value directly since the changes will be ` +
            `overwritten whenever the provided component re-renders. ` +
            `injection being mutated: "${key}"`,
            vm
          )
        })
      } else {
        defineReactive(vm, key, result[key])
      }
    })
    // 将shouldObserve设置为true，其作用通知defineReact函数将内容转为响应式。
    toggleObserving(true)  // shouldObserve = value
  }
}
```

**读出用户在当前组件中设置的inject的key，然后循环`key`，将每一个`key`从当前组件起，不断向父组件查找是否有值，找到了就停止循环，最终将所有`key`对应的值一起返回给`result`。然后循环`result`并依次调用`defineReactive`函数，将它们设置到`Vue.js`实例上。**

## `provide`内部原理

```javascript
export function initProvide (vm: Component) {
  // 获取用户定义的provide
  const provide = vm.$options.provide
  if (provide) {
    // 当provide存在时，判断provide如果为函数，则执行它，并且返回给vm._provided，
    // 否则直接返回给vm._provided
    vm._provided = typeof provide === 'function'
      ? provide.call(vm)
      : provide
  }
}
```

**首先判断`provide`的类型是否为函数，如果是，则执行函数，将返回值赋值给`vm._provided`，否则直接将变量`provide`赋值给`vm._provided`**

## 初始化`props`

### 规格化`props`

**子组件被实例化时，会先对`props`进行规格化处理，规格化之后的`props`为对象的格式。**

#### 实现原理

```javascript

```

首先判断是否有`props`属性，如果没有，说明用户没有使用`props`接受任何数据，那么不需要规格化，直接使用`return`语句退出即可。然后声明一个变量`res`，用来保存规格化后的结果。

随后是规格化的主要逻辑。先检查`props`是否为一个数组。如果不是则调用`isPlainObject函数`检查它是否为**对象类型**，如果都不是，那么在非生产环境下在控制台打印警告。如果`props`是一个**数组**，那么通过while语句循环数组中每一项，判断`props`名称的类型是否是`string`类型：如果不是，则在非生产环境下在控制台中打印警告；如果是，则调用`camelize`函数将`props`**名称驼峰化**，即可以将`a-b`这样的名称转换成`aB`。最后再将`res`赋值给`options.props`

### 初始化`props`

#### 实现原理

```javascript
function initProps (vm: Component, propsOptions: Object) {
  // propsData保存的是通过父组件传入或用户通过propsData传入的真实的props数据
  const propsData = vm.$options.propsData || {}
  // 将props指向vm._props
  const props = vm._props = {}
  // 缓存属性的key，使得将来能直接使用数组的索引值来更新props而不是动态地枚举对象
  const keys = vm.$options._propKeys = []
  // 根据$parent是否存在来判断当前是否是根结点，
  // 如果不是，那么不需要将props数据转换成响应式数据。
  const isRoot = !vm.$parent
  // root实例的props属性应该被转换成响应式数据
  if (!isRoot) {
    // 通过toggleObserving函数来确定并控制defineReactive函数调用时所传入的value参数
    // 是否需要转换成响应式。
    toggleObserving(false)
  }
  for (const key in propsOptions) {
    keys.push(key)
    // 验证prop,不存在用默认值替换，类型为bool则声称true或false，
    // 当使用default中的默认值的时候会将默认值的副本进行observe
    const value = validateProp(key, propsOptions, propsData, vm)
    /* istanbul ignore else */
    if (process.env.NODE_ENV !== 'production') {
      const hyphenatedKey = hyphenate(key)
      // 判断是否是保留字段，如果是则发出warning
      if (isReservedAttribute(hyphenatedKey) ||
          config.isReservedAttr(hyphenatedKey)) {
        warn(
          `"${hyphenatedKey}" is a reserved attribute and cannot be used as component prop.`,
          vm
        )
      }
      // 通过defineReactive函数，将props上的属性转换成响应式数据，
      // 同时将通过调用validateProp函数得到的props数据设置到vm._props中
      // 注意：此时的参数中的props是指向于vm._props的
      defineReactive(props, key, value, () => {
        if (!isRoot && !isUpdatingChildComponent) {
          warn(
            `Avoid mutating a prop directly since the value will be ` +
            `overwritten whenever the parent component re-renders. ` +
            `Instead, use a data or computed property based on the prop's ` +
            `value. Prop being mutated: "${key}"`,
            vm
          )
        }
      })
    } else {
      defineReactive(props, key, value)
    }
    // static props are already proxied on the component's prototype
    // during Vue.extend(). We only need to proxy props defined at
    // instantiation here.
    if (!(key in vm)) {
      // 最后将props中的属性代理到vm身上，当使用vm[key]访问数据时，
      // 其实访问的是vm._props[key]
      proxy(vm, `_props`, key)
    }
  }
  toggleObserving(true)
}
```

**首先从`propsData`中获取真实的props数据。再将变量`props`指向`vm._props`的指针，也就是所有设置到`props`变量中的属性最终会保存到`vm._props`中。变量`keys`是指向`vm.$options._propKeys`的指针，其作用缓存props对象中的`key`，将来更新props时只需要遍历`vm.$options_propKeys`数组即可得到所有`props的key`。变量`isRoot`的作用是判断当前组件是否是根组件。接下来会判断当前组件是否为根组件，如果不是，那么不需要将`props`数据转为响应式数据。**

**这里`toggleObserving`函数的作用是确定并控制`defineReactive`函数调用时所传入的value参数是否需要转换成响应式。**

**然后循环`propsOptions`，在循环体中先将`key`添加到`keys`中，然后调用`validateProp`函数将得到的props数据通过`defineReactive`函数设置为响应式。**

**最后判断这个`key`在`vm`中是否存在，如果不存在，则调用`proxy将props`中的属性代理到`vm`身上，当使用`vm[key]`访问数据时，其实访问的是`vm._props[key]`**

## 初始化`computed`

### 实现原理

**`computed`是定义在`vm`上的一个特殊的`getter`方法。之所以说特殊，是因为在`vm上定义getter`方法时，`get`并不是用户提供的函数，而是`Vue.js`内部的一个代理函数。在代理函数中可以结合`watcher`实现缓存与收集依赖等功能。**
我们知道计算属性的结果会被缓存，且只有在计算属性所依赖的响应式属性或者说计算属性的返回值发生变化时才会重新计算。那么，如何知道计算属性的返回值是否发生了变化？这其实是结合 `Watcher的 dirty`属性来分辨的：当 `dirty属性为true`时,说明需要重新计算“计算属性”的返回值；当`dirty属性为flase`时，说明计算属性的值并没有变，不需要重新计算。

当计算属性中的内容发生变化后，计算属性的 `Watcher与组件的 Watcher`都会得到通知。计算属性的 `Watcher`会将自己的 `dirty属性设置为true`，当下一次读取计算属性时，就会重新计算一次值。然后组件的 `Watcher`也会得到通知，从而执行 `render`函数进行重新渲染的操作。（**注意：就算计算属性值进行了重新计算，只要新值与旧值相等，便不会发生重新渲染**）
由于要重新执行 `render`函数，所以会重新读取计算属性的值，这时候计算属性的 `Watcher`已经把自己的`dirty`属性设置为`true`，所以会重新计算一次计算属性的值，用于本次渲染。

**简单来说，计算属性会通过 `Watcher`来观察它所用到的所有属性的变化，当这些属性发生变化时，计算属性会将自身的 `Watcher的 dirty`属性设置为true，说明自身的返回值变了。**

![image-20200407110112103](D:\Note\image\image-20200407110112103.png)

```javascript
// 伪代码
get: function computedGetter () {
  		 const watcher = this._computedWatchers && this._computedWatchers[key]
  		 // 实际是脏检查，在计算属性中的依赖发生改变的时候dirty会变成true，
  	   // 在get的时候重新计算计算属性的输出值
  		 if (watcher) {
    		 // 当dirty为true时，重新计算计算属性的返回值,并将dirty设为false
    		 if (watcher.dirty) {
      		 watcher.evaluate()
      		 /*
              evaluate () {
                this.value = this.get()
                this.dirty = false
              }
           */
    		 }
    	 // 对render watcher进行依赖收集
    	 if (Dep.target) {
      	 watcher.depend()
    	 }
    	 return watcher.value
  	 }
	 }
```

