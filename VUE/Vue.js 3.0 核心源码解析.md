# 一、`Vue.js3.0`的优化

**`Vue.js` 从 `1.x` 到 2.0 版本，最大的升级就是引入了虚拟 DOM 的概念，它为后续做服务端渲染以及跨端框架 `Weex` 提供了基础。**

在迭代 `2.x` 版本的过程中，存在很多需要解决的痛点，比如**源码自身的维护性，数据量大后带来的渲染和更新的性能问题，一些想舍弃但为了兼容一直保留的鸡肋 `API**` 等**；另外还有**更好的 `TypeScript` 支持、更好的逻辑复用实践等**，所以`Vue.js`的作者希望能从**源码、性能和语法 `API`** 三个大的方面优化框架。

## 1.1 源码优化

### 更好的代码管理方式：`monorepo`

> `Monorepo` 其实不是一个新的概念，在软件工程领域，它已经有着十多年的历史了。概念上很好理解，就是把**多个项目**放在**一个仓库**里面，相对立的是传统的 `MultiRepo` 模式，即每个项目对应一个单独的仓库来分散管理。
>
> 参考链接：[现代前端工程为什么越来越离不开 `Monorepo`?](https://juejin.cn/post/6944877410827370504#heading-2)

首先，源码的优化体现在代码管理方式上。`Vue.js 2.x` 的源码托管在 `src` 目录，然后依据功能拆分出了 `compiler`（模板编译的相关代码）、`core`（与平台无关的通用运行时代码）、`platforms`（平台专有代码）、`server`（服务端渲染的相关代码）、`sfc`（`.vue` 单文件解析相关代码）、`shared`（共享工具代码） 等目录：

![image-20211107113047022](../image/image-20211107113047022.png)

而到了 `Vue.js 3.0` ，整个源码是通过 `monorepo` 的方式维护的，根据功能将不同的模块拆分到 packages 目录下面不同的子目录中：

![image-20211107113454613](../image/image-20211107113454613.png)

可以看出相对于 `Vue.js 2.x` 的源码组织方式，`monorepo` 把这些模块拆分到不同的 `package` 中，每个 `package` 有各自的 `API`、类型定义和测试。这样**使得模块拆分更细化，职责划分更明确，模块之间的依赖关系也更加明确，开发人员也更容易阅读、理解和更改所有模块源码，提高代码的可维护性。**

另外**一些 `package`（比如 `reactivity` 响应式库）是可以独立于 `Vue.js` 使用的，这样用户如果只想使用 `Vue.js 3.0` 的响应式能力，可以单独依赖这个响应式库而不用去依赖整个 `Vue.js`，减小了引用包的体积大小**，而 `Vue.js 2 .x` 是做不到这一点的。

### 有类型的 `JavaScript：TypeScript`

其次，源码的优化还体现在 `Vue.js 3.0` 自身采用了 `TypeScript` 开发。`Vue.js 1.x` 版本的源码是没有用类型语言的，尤雨溪用 `JavaScript` 开发了整个框架，但对于复杂的框架项目开发，**使用类型语言非常有利于代码的维护，因为它可以在编码期间帮你做类型检查，避免一些因类型问题导致的错误；也可以利于它去定义接口的类型，利于 `IDE` 对变量类型的推导**。

因此在重构 `2.0` 的时候，尤雨溪选型了 `Flow`，但是在 `Vue.js 3.0` 的时候抛弃 `Flow` 转而采用 `TypeScript` 重构了整个项目，这里有两方面原因：

①`Flow` 是 `Facebook` 出品的 `JavaScript` 静态类型检查工具，它可以以非常小的成本对已有的 JavaScript 代码迁入，非常灵活，这也是 `Vue.js 2.0` 当初选型它时一方面的考量。但是 `Flow` 对于一些复杂场景类型的检查，支持得并不好。记得在看 `Vue.js 2.x` 源码的时候，在某行代码的注释中看到了对 `Flow` 的吐槽，比如在组件更新 `props` 的地方出现了：

```javascript
const propOptions: any = vm.$options.props // wtf flow?
```

什么意思呢？其实是由于这里 `Flow` 并没有正确推导出 `vm.$options.props` 的类型 ，开发人员不得不强制申明 `propsOptions` 的类型为 `any`，显得很不合理；另外他也在社区平台吐槽过 `Flow` 团队的烂尾。

②其次，`Vue.js 3.0` 抛弃 Flow 后，使用 `TypeScript` 重构了整个项目。 `TypeScript`提供了更好的类型检查，能支持复杂的类型推导；由于源码就使用 `TypeScript` 编写，也省去了单独维护 `d.ts` 文件的麻烦；就整个 `TypeScript` 的生态来看，`TypeScript` 团队也是越做越好，`TypeScript` 本身保持着一定频率的迭代和更新，支持的 `feature` 也越来越多。

## 1.2 性能优化

性能优化一直是前端老生常谈的问题。对于 `Vue.js 2.x` 已经足够优秀的前端框架，它的性能优化主要从**源码体积、数据劫持、编译优化**几个方面进行突破。

### 源码体积优化

首先是源码体积优化，我们在平时工作中也经常会尝试优化静态资源的体积，因为 `JavaScript` 包体积越小，意味着网络传输时间越短，`JavaScript` 引擎解析包的速度也越快。

`Vue.js 3.0` 在源码体积的减少方面做了以下工作：

- **首先，移除一些冷门的 `feature`（比如 `filter、inline-template` 等）；**
- **其次，引入 `tree-shaking` 的技术，减少打包体积。**

####  `tree-shaking`

`tree-shaking`的原理比较简单，`tree-shaking` 依赖 `ES2015` 模块语法的静态结构（即 `import 和 export`），通过编译阶段的静态分析，找到没有引入的模块并打上标记。

举个例子，一个 `math` 模块定义了 2 个方法 `square(x)` 和 `cube(x)` ：

```javascript
export function square(x) {
  return x * x
}

export function cube(x) {
  return x * x * x
}
```

我们在这个模块外面只引入了 `cube` 方法：

```javascript
import { cube } from './math.js'
// do something with cube
```

最终 `math` 模块会被 `webpack` 打包生成如下代码：

```javascript
/* 1 */

/***/ (function(module, __webpack_exports__, __webpack_require__) {

  'use strict';

  /* unused harmony export square */

  /* harmony export (immutable) */ __webpack_exports__['a'] = cube;

  function square(x) {
    return x * x;
  }

  function cube(x) {
    return x * x * x;
  }
});

```

可以看到，未被引入的 `square` 模块被标记了`（unused harmony export square）`， 然后**压缩阶段会利用例如 `uglify-js、terser` 等压缩工具真正地删除这些没有用到的代码**。

也就是说，利用 `tree-shaking` 技术，**如果你在项目中没有引入 `Transition、KeepAlive` 等组件，那么它们对应的代码就不会打包，这样也就间接达到了减少项目引入的 `Vue.js` 包体积的目的。**

### 数据劫持优化

其次是数据劫持优化。`Vue.js` 区别于 `React` 的一大特色是它的数据是响应式的，这个特性从 `Vue.js 1.x` 版本就一直伴随着，这也是 `Vue.js` 粉喜欢 `Vue.js` 的原因之一，**`DOM` 是数据的一种映射，数据发生变化后可以自动更新 DOM，用户只需要专注于数据的修改，没有其余的心智负担。**

**在 `Vue.js` 内部，想实现这个功能是要付出一定代价的，那就是必须劫持数据的访问和更新。**其实这点很好理解，当数据改变后，为了自动更新 `DOM`，那么就必须劫持数据的更新，也就是说当数据发生改变后能自动执行一些代码去更新 DOM，那么问题来了，`Vue.js` 怎么知道更新哪一片 `DOM` 呢？因为在渲染 `DOM` 的时候访问了数据，我们可以对它进行访问劫持，这样就在内部建立了依赖关系，也就知道数据对应的 `DOM` 是什么了。以上只是大体的思路，具体实现要比这更复杂，内部还依赖了一个 `watcher` 的数据结构做依赖管理，参考下图：

![CgqCHl8CudyAJc3tAAGkPxYyp9k501](../image/CgqCHl8CudyAJc3tAAGkPxYyp9k501.png)

#### `Vue.js1.x 和 Vue.js2.x的不足`

`Vue.js 1.x 和 Vue.js 2.x` 内部都是通过 `Object.defineProperty` 这个 `API` 去劫持数据的 getter 和 setter，**针对于的是对象中的单个属性进行劫持**，具体是这样的：

```javascript
Object.defineProperty(data, 'a',{
  get(){
    // track
  },
  set(){
    // trigger
  }
})

```

但这个 `API` 有一些缺陷，它必须预先知道要拦截的 key 是什么，所以它并不能检测对象属性的添加和删除。尽管 `Vue.js` 为了解决这个问题提供了 `$set` 和 ​`$delete` 实例方法，但是对于用户来说，还是增加了一定的心智负担。

另外 `Object.defineProperty` 的方式还有一个问题，举个例子，比如这个嵌套层级比较深的对象：

```javascript
export default {
  data: {
    a: {
      b: {
        c: {
          d: 1
        }
      }
    }
  }
}
```

由于 **`Vue.js` 无法判断你在运行时到底会访问到哪个属性，所以对于这样一个嵌套层级较深的对象，如果要劫持它内部深层次的对象变化，就需要递归遍历这个对象，执行 `Object.defineProperty` 把每一层对象数据都变成响应式的。**毫无疑问，如果我们定义的响应式数据过于复杂，这就会有相当大的性能负担。

#### `Vue.js3.0`的优化

为了解决上述 2 个问题，`Vue.js 3.0` 使用了 `Proxy API` 做数据劫持，它的内部是这样的：

```javascript
observed = new Proxy(data, {
  get() {
    // track
  },
  set() {
    // trigger
  }
})

```

**由于它劫持的是整个对象，那么自然对于对象的属性的增加和删除都能检测到。**

但要注意的是，**`Proxy API` 并不能监听到内部深层次的对象变化，因此 `Vue.js 3.0` 的处理方式是在 `getter` 中去递归响应式，这样的好处是真正访问到的内部对象才会变成响应式，而不是无脑递归，这样无疑也在很大程度上提升了性能。**

### 编译优化

最后是编译优化，为了便于理解，我们先来看一张图：

![CgqCHl8Cuf2AZw70AAEFU2EMA50521](../image/CgqCHl8Cuf2AZw70AAEFU2EMA50521.png)

这是 `Vue.js 2.x` 从 `new Vue` 开始渲染成 DOM 的流程，上面说过的响应式过程就发生在图中的 `init` 阶段，另外 `template compile to render function` 的流程是可以借助 `vue-loader` 在 `webpack` 编译阶段离线完成，并非一定要在运行时完成。

所以**想优化整个 `Vue.js` 的运行时，除了数据劫持部分的优化，我们可以在耗时相对较多的 `patch` 阶段想办法**，`Vue.js 3.0` 也是这么做的，并且它通过在编译阶段优化编译的结果，来实现运行时 `patch` 过程的优化。

我们知道，通过数据劫持和依赖收集，`Vue.js 2.x` 的数据更新并触发重新渲染的粒度是组件级的：

> 在`Vue.js1.x`中，是通过细粒度的绑定来更新视图，也就是说：假如有一个状态绑定着好多个依赖，每个依赖表示一个具体的DOM节点，那么当这个状态发生变化时，向这个状态的所有依赖发送通知，让它们进行`DOM`更新操作。但是这样也会有一定的代价，因为粒度越细，每个状态所绑定的依赖就越多，依赖追踪在内存上的开销就会越大。因此，从`Vue.js2.0`开始，引入了虚拟DOM，将粒度调整为中等粒度，即一个状态所绑定的依赖不再是具体的DOM节点，而是一个组件。这样状态变化后，会通知到组件，组件内部再使用虚拟DOM进行比对。这可以大大降低依赖数量，从而降低依赖追踪所消耗的内存。

![Ciqc1F8CuhCADiJ1AAGMYHdzHa0498](../image/Ciqc1F8CuhCADiJ1AAGMYHdzHa0498.png)

虽然 `Vue` 能保证触发更新的组件最小化，但在单个组件内部依然需要遍历该组件的整个 `vnode` 树，举个例子，比如我们要更新这个组件：

```vue
<template>
  <div id="content">
    <p class="text">static text</p>
    <p class="text">static text</p>
    <p class="text">{{message}}</p>
    <p class="text">static text</p>
    <p class="text">static text</p>
  </div>
</template>

```

整个 `diff` 过程如图所示：

![Ciqc1F-zmN-AbAP_AAEJ0vicgdA415](../image/Ciqc1F-zmN-AbAP_AAEJ0vicgdA415.png)

可以看到，**因为这段代码中只有一个动态节点，所以这里有很多 `diff` 和遍历其实都是不需要的，这就会导致 `vnode` 的性能跟模版大小正相关，跟动态节点的数量无关，当一些组件的整个模版内只有少量动态节点时，这些遍历都是性能的浪费。**

而对于上述例子，**理想状态只需要 `diff` 这个绑定 `message` 动态节点的 `p` 标签即可。**

**`Vue.js 3.0` 做到了，它通过编译阶段对静态模板的分析，编译生成了 `Block tree`。`Block tree` 是一个将模版基于动态节点指令切割的嵌套区块，每个区块内部的节点结构是固定的，而且每个区块只需要以一个 `Array` 来追踪自身包含的动态节点。借助 `Block tree`，`Vue.js` 将 `vnode` 更新性能由与模版整体大小相关提升为与动态内容的数量相关，这是一个非常大的性能突破。**

除此之外，`Vue.js 3.0` 在编译阶段还包含了对 `Slot` 的编译优化、事件侦听函数的缓存优化，并且在运行时重写了 `diff` 算法。

## 1.3 语法 `API` 优化：`Composition API`

除了源码和性能方面，`Vue.js 3.0` 还在语法方面进行了优化，主要是提供了 `Composition API`。

### 优化逻辑组织

首先，是优化逻辑组织。

在 `Vue.js 1.x 和 2.x` 版本中，编写组件本质就是在编写一个“包含了描述组件选项的对象”，我们把它称为 `Options API`，它的好处是在于写法非常符合直觉思维，对于新手来说这样很容易理解，这也是很多人喜欢 `Vue.js` 的原因之一。

**`Options API` 的设计是按照 `methods、computed、data、props` 这些不同的选项分类，当组件小的时候，这种分类方式一目了然；但是在大型组件中，一个组件可能有多个逻辑关注点，当使用 `Options API` 的时候，每一个关注点都有自己的 `Options`，如果需要修改一个逻辑点关注点，就需要在单个文件中不断上下切换和寻找。**

举一个官方例子 `Vue CLI UI file explorer`，它是 `vue-cli GUI` 应用程序中的一个复杂的文件浏览器组件。这个组件需要处理许多不同的逻辑关注点：

- 跟踪当前文件夹状态并显示其内容
- 处理文件夹导航（比如打开、关闭、刷新等）
- 处理新文件夹的创建
- 切换显示收藏夹
- 切换显示隐藏文件夹
- 处理当前工作目录的更改

如果我们按照逻辑关注点做颜色编码，就可以看到当使用 `Options API` 去编写组件时，这些逻辑关注点是非常分散的：

![Ciqc1F8CoIeAToThAAG5J8trLZc593](../image/Ciqc1F8CoIeAToThAAG5J8trLZc593.png)

`Vue.js 3.0` 提供了一种新的 `API：Composition API`，它有一个很好的机制去解决这样的问题，就是将某个逻辑关注点相关的代码全都放在一个函数里，这样当需要修改一个功能时，就不再需要在文件中跳来跳去。

通过下图，我们可以很直观地感受到 `Composition API` 在逻辑组织方面的优势：

![CgqCHl8CoI-ACOXEAAM5NZiddQs980](../image/CgqCHl8CoI-ACOXEAAM5NZiddQs980.png)

### 优化逻辑复用

其次，是优化逻辑复用。

当我们开发项目变得复杂的时候，免不了需要抽象出一些复用的逻辑。在 `Vue.js 2.x` 中，我们通常会用 `mixins` 去复用逻辑，举一个鼠标位置侦听的例子，我们会编写如下函数 `mousePositionMixin`：

```javascript
const mousePositionMixin = {
  data() {
    return {
      x: 0,
      y: 0
    }
  },
  mounted() {
    window.addEventListener('mousemove', this.update)
  },
  destroyed() {
    window.removeEventListener('mousemove', this.update)
  },
  methods: {
    update(e) {
      this.x = e.pageX
      this.y = e.pageY
    }
  }
}
export default mousePositionMixin
```

然后在组件中使用：

```vue
<template>
  <div>
    Mouse position: x {{ x }} / y {{ y }}
  </div>
</template>
<script>
import mousePositionMixin from './mouse'
export default {
  mixins: [mousePositionMixin]
}
</script>
```

**使用单个 `mixin` 似乎问题不大，但是当我们一个组件混入大量不同的 `mixins` 的时候，会存在两个非常明显的问题：命名冲突和数据来源不清晰。**

首先每个 `mixin` 都可以定义自己的 `props、data`，它们之间是无感的，所以很容易定义相同的变量，导致命名冲突。另外对组件而言，如果模板中使用不在当前组件中定义的变量，那么就会不太容易知道这些变量在哪里定义的，这就是数据来源不清晰。但是`Vue.js 3.0` 设计的 `Composition API`，就很好地帮助我们解决了 `mixins` 的这两个问题。

我们来看一下在 `Vue.js 3.0` 中如何书写这个示例：

```javascript
import { ref, onMounted, onUnmounted } from 'vue'
export default function useMousePosition() {
  const x = ref(0)
  const y = ref(0)
  const update = e => {
    x.value = e.pageX
    y.value = e.pageY
  }
  onMounted(() => {
    window.addEventListener('mousemove', update)
  })
  onUnmounted(() => {
    window.removeEventListener('mousemove', update)
  })
  return { x, y }
}
```

这里我们约定 `useMousePosition` 这个函数为 hook 函数，然后在组件中使用：

```vue
<template>
  <div>
    Mouse position: x {{ x }} / y {{ y }}
  </div>
</template>
<script>
  import useMousePosition from './mouse'
  export default {
    setup() {
      const { x, y } = useMousePosition()
      return { x, y }
    }
  }
</script>
```

可以看到，整个数据来源清晰了，即使去编写更多的 `hook` 函数，也不会出现命名冲突的问题。

`Composition API` 除了在逻辑复用方面有优势，也会有更好的类型支持，因为它们都是一些函数，在调用函数时，自然所有的类型就被推导出来了，不像 `Options API` 所有的东西使用 `this`。另外，`Composition API` 对 `tree-shaking` 友好，代码也更容易压缩。

虽然 `Composition API` 有诸多优势，它也不是一点缺点都没有。这里还需要说明的是，`Composition API` 属于 `API` 的增强，它并不是 `Vue.js 3.0` 组件开发的范式，如果你的组件足够简单，你还是可以使用 `Options API`。

## 1.4 引入 RFC：使每个版本改动可控

作为一个流行开源框架，可能每天都会收到各种各样的 `feature request`。但并不是社区一有新功能的需求，框架就会立马支持，因为随着 `Vue.js` 的用户越来越多，肯定会更加重视稳定性，会仔细考虑所做的每一个可能对最终用户影响的更改，以及有意识去防止新 `API` 对框架本身实现带来的复杂性的提升。

因此在 `Vue.js 2.x` 版本开发到后期的阶段 ，小右就启用了 `RFC` ，它的全称是 `Request For Comments`，旨在为新功能进入框架提供一个一致且受控的路径。当社区有一些新需求的想法时，它可以提交一个 RFC，然后由社区和 `Vue.js` 的核心团队一起讨论，如果这个 `RFC` 最终被通过了，那么它才会被实现。比如 2.6 版本对于 `slot 新 API` 的改动，就是[这条 RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md) 里。

到了 `Vue.js 3.0` ，小右在实现代码前就大规模启用 `RFC`，来确保他的改动和设计都是经过讨论并确认的，这样可以避免走弯路。`Vue.js 3.0` 版本有很多重大的改动，每一条改动都会有对应的 `RFC`，通过阅读这些 `RFC`，你可以了解每一个 `feature` 采用或被废弃掉的前因后果。

`Vue.js 3.0` 目前已被实现并合并的 RFC 都在[这里](https://github.com/vuejs/rfcs/pulls?q=is%3Apr+is%3Amerged+label%3A3.x)，通过阅读它们，你也可以大致了解 `Vue.js 3.0` 的一些变化，以及为什么会产生这些变化，帮助你了解它的前因后果。 

## 1.5 过渡期

通常框架的 `major` 版本从升级到大规模投入使用，都需要经历相当长的一段过渡期。不过， `Vue.js 1.x` 到 `Vue.js 2.0` 的升级过渡期不长，主要是因为那个时候 `Vue.js` 的用户还不多，生态也不完善，很多用户都是直接上手的 2.0 版本，没有旧项目的历史包袱。

而 `Vue.js 2.x` 的发展历经了 3 年多的时间，用户众多，而且周边生态也已经非常完善了。通常 `major` 版本的升级会有很多 `breaking change`，这就意味着想从 `2.x` 升级到 `3.0` 的项目需要改代码，而且不仅仅项目的代码要修改，所依赖的周边生态也需要升级。这其实是一个相当大的工作量，也需要承担一定的风险，所以如果你的项目非常庞大且已经相对稳定，没有什么特别的痛点，那么升级要慎重。

**`Vue.js 3.0` 使用 `ES2015` 的语法开发，有些 `API` 如 `Proxy` 是没有 `polyfill` 的，这就意味着官方需要单独出一个 `IE11 compat` 版本来支持 `IE11`。如果你的项目需要兼容 `IE11`，你就不得不小心使用某些 `API`，这也就带来了一些额外的心智负担。**

因此可能在 `Vue.js 3.0` 出来的相当长的一段时间，复杂的大项目都不会考虑去升级，而一些小的、对浏览器兼容要求不高的新项目可以考虑尝鲜了。

官方会继续维护 `Vue.js 2.x` 版本 18 个月，如果你的有些项目一辈子都不打算升级 `Vue.js 3.0`，那么你应该去认真学习 `Vue.js 2.x` 的源码，在官方不再维护的时候遇到问题你可以自己去修改它的源码来解决。

不过，虽然 `Vue.js 3.0` 距离大规模应用还有相当长一段时间，但是越早开始学习你就越能在未来掌握主动权。这段时间里，你可以关注它的发展，去学习它的设计思想，也可以去为它的生态建设贡献代码，从而提升自己的技术能力。另外也可以尝试在一些小项目中应用 `Vue.js 3.0`，不仅可以享受 `Vue.js 3.0` 带来的性能方面的优势以及 `Composition API` 在逻辑复用方面便利，也为了将来某一天全面升级 `Vue.js 3.0` 做技术储备。

# 二、`Vue.js`核心组件的实现原理

我们开发 `Vue.js` 的项目，大部分时间都是在写组件，组件系统是 `Vue.js` 的一个重要概念，它是一种对 `DOM` 结构的抽象，我们可以使用小型、独立和通常可复用的组件构建大型应用。仔细想想，几乎任意类型的应用界面都可以抽象为一个组件树，如下：

![CgqCHl8CumKAR7ayAABTdicCgXc349](../image/CgqCHl8CumKAR7ayAABTdicCgXc349.png)

组件化也是 `Vue.js` 的核心思想之一，它允许我们用模板加对象描述的方式去创建一个组件，再加上我们给组件注入不同的数据，就可以完整地渲染出组件：

![CgqCHl8CulaAe5o1AABk5y8xUSI791](../image/CgqCHl8CulaAe5o1AABk5y8xUSI791.png)

当数据更新后，组件可以自动重新渲染，因此用户只需要专注于数据逻辑的处理，而无须关心 DOM 的操作，无论是开发体验和开发效率都得到了很大的提升。

## 2.1 组件渲染：`vnode` 到真实 DOM 是如何转变的？

首先，组件是一个抽象的概念，它是对一棵 `DOM` 树的抽象，我们在页面中写一个组件节点：

```vue
<hello-world></hello-world>
```

这段代码并不会在页面上渲染一个`<hello-world>`标签，而它具体渲染成什么，取决于你怎么编写 `HelloWorld` 组件的模板。举个例子，`HelloWorld` 组件内部的模板定义是这样的：

```javascript
<template>
  <div>
    <p>Hello World</p>
  </div>
</template>
```

可以看到，模板内部最终会在页面上渲染一个 `div`，内部包含一个 `p` 标签，用来显示 `Hello World` 文本。

所以，从表现上来看，组件的模板决定了组件生成的 `DOM` 标签，而在 `Vue.js` 内部，一个组件想要真正的渲染生成 `DOM`，还需要经历`“创建 vnode - 渲染 vnode - 生成 DOM”` 这几个步骤：

![Ciqc1F8EO_OAOS86AABTGXEAHqA770](../image/Ciqc1F8EO_OAOS86AABTGXEAHqA770.png)

### 应用程序初始化

> 一个组件可以通过“模板加对象描述”的方式创建，组件创建好以后是如何被调用并初始化的呢？因为整个组件树是由根组件开始渲染的，为了找到根组件的渲染入口，我们需要从应用程序的初始化过程开始分析。

在这里，我分别给出了通过 `Vue.js 2.x` 和 `Vue.js 3.0` 来初始化应用的代码：

```javascript
// 在 Vue.js 2.x 中，初始化一个应用的方式如下
import Vue from 'vue'
import App from './App'
const app = new Vue({
  render: h => h(App)
})
app.$mount('#app')
```

```javascript
// 在 Vue.js 3.0 中，初始化一个应用的方式如下
import { createApp } from 'vue'
import App from './app'
const app = createApp(App)
app.mount('#app')
```

可以看到，`Vue.js 3.0` 初始化应用的方式和 `Vue.js 2.x` 差别并不大，本质上都是把 `App` 组件挂载到 `id` 为 `app` 的 `DOM` 节点上。

但是，在 `Vue.js 3.0` 中还导入了一个 `createApp`，其实这是个入口函数，它是 `Vue.js` 对外暴露的一个函数，我们来看一下它的内部实现：

```javascript
const createApp = ((...args) => {
  // 创建 app 对象
  const app = ensureRenderer().createApp(...args)
  const { mount } = app
  // 重写 mount 方法
  app.mount = (containerOrSelector) => {
    // ...
  }
  return app
})

```

从代码中可以看出 `createApp` 主要做了两件事情：**创建 `app` 对象和重写 `app.mount` 方法**。

#### 创建 `app` 对象

首先，我们使用 `ensureRenderer().createApp()` 来创建 `app` 对象 ：

```javascript
 const app = ensureRenderer().createApp(...args)
```

其中 `ensureRenderer()` 用来创建一个渲染器对象，它的内部代码是这样的：

> 为什么说用`ensureRenderer()` 来延时创建渲染器？因为 `ensureRenderer` 是在执行 `createApp` 的时候调用的，如果你不执行 `createApp` 而只使用 `vue` 的一些响应式 `API`，就不会创建这个渲染器，所以说延时渲染。

```javascript
// 渲染相关的一些配置，比如更新属性的方法，操作 DOM 的方法
const rendererOptions = {
  patchProp,
  ...nodeOps
}
let renderer
// 延时创建渲染器，当用户只依赖响应式包的时候，可以通过 tree-shaking 移除核心渲染逻辑相关的代码
function ensureRenderer() {
  return renderer || (renderer = createRenderer(rendererOptions))
}
function createRenderer(options) {
  return baseCreateRenderer(options)
}
function baseCreateRenderer(options) {
  function render(vnode, container) {
    // 组件渲染的核心逻辑
  }
  return {
    render,
    createApp: createAppAPI(render)
  }
}
function createAppAPI(render) {
  // createApp createApp 方法接受的两个参数：根组件的对象和 prop
  return function createApp(rootComponent, rootProps = null) {
    const app = {
      _component: rootComponent,
      _props: rootProps,
      mount(rootContainer) {
        // 创建根组件的 vnode
        const vnode = createVNode(rootComponent, rootProps)
        // 利用渲染器渲染 vnode
        render(vnode, rootContainer)
        app._container = rootContainer
        return vnode.component.proxy
      }
    }
    return app
  }
}
```

可以看到，这里先用 `ensureRenderer()` 来延时创建渲染器，这样做的好处是当用户只依赖响应式包的时候，就不会创建渲染器，因此可以通过 `tree-shaking` 的方式移除核心渲染逻辑相关的代码。

这里涉及了渲染器的概念，它是为跨平台渲染做准备的，之后我会在自定义渲染器的相关内容中详细说明。在这里，你可以简单地把渲染器理解为包含平台渲染核心逻辑的 `JavaScript` 对象。

我们结合上面的代码继续深入，在 `Vue.js 3.0` 内部通过 `createRenderer` 创建一个渲染器，这个渲染器内部会有一个 `createApp` 方法，它是执行 `createAppAPI` 方法返回的函数，接受了 `rootComponent` 和 `rootProps` 两个参数，我们在应用层面执行 `createApp(App)` 方法时，会把 `App` 组件对象作为根组件传递给 `rootComponent`。这样，`createApp` 内部就创建了一个 `app` 对象，它会提供 `mount` 方法，这个方法是用来挂载组件的。

在整个 `app` 对象创建过程中，`Vue.js` 利用闭包和函数柯里化的技巧，很好地实现了参数保留。比如，在执行 `app.mount` 的时候，并不需要传入渲染器 `render`，这是因为在执行 `createAppAPI` 的时候渲染器 `render` 参数已经被保留下来了。

#### 重写 `app.mount` 方法

接下来，是重写 `app.mount` 方法。

根据前面的分析，我们知道 `createApp` 返回的 `app` 对象已经拥有了 `mount` 方法了，但在入口函数中，接下来的逻辑却是对 `app.mount` 方法的重写。先思考一下，为什么要重写这个方法，而不把相关逻辑放在 `app` 对象的 `mount` 方法内部来实现呢？

这是因为 `Vue.js` 不仅仅是为 Web 平台服务，它的目标是支持跨平台渲染，而 `createApp` 函数内部的 `app.mount` 方法是一个标准的可跨平台的组件渲染流程：

```javascript
mount(rootContainer) {
  // 创建根组件的 vnode
  const vnode = createVNode(rootComponent, rootProps)
  // 利用渲染器渲染 vnode
  render(vnode, rootContainer)
  app._container = rootContainer
  return vnode.component.proxy
}
```

**标准的跨平台渲染流程是先创建 `vnode`，再渲染 `vnode`。**此外参数 `rootContainer` 也可以是不同类型的值，比如，在 `Web` 平台它是一个 `DOM` 对象，而在其他平台（比如 `Weex` 和小程序）中可以是其他类型的值。所以这里面的代码不应该包含任何特定平台相关的逻辑，也就是说这些代码的执行逻辑都是与平台无关的。因此我们需要在外部重写这个方法，来完善 Web 平台下的渲染逻辑。

接下来，再来看 `app.mount` 重写都做了哪些事情：

```javascript
app.mount = (containerOrSelector) => {
  // 标准化容器
  // 通过 normalizeContainer 标准化容器
  // 开发者可以给mout方法传字符串选择器或者 DOM 对象作为参数，
  // 但如果是字符串选择器，就需要把它转成 DOM 对象，作为最终挂载的容器
  const container = normalizeContainer(containerOrSelector)
  if (!container)
    return
  const component = app._component
   // 如组件对象没有定义 render 函数和 template 模板，则取容器的 innerHTML 作为组件模板内容
  if (!isFunction(component) && !component.render && !component.template) {
    component.template = container.innerHTML
  }
  // 挂载前清空容器内容
  container.innerHTML = ''
  // 真正的挂载
  return mount(container)
}
```

**首先是通过 `normalizeContainer` 标准化容器（这里可以传字符串选择器或者 DOM 对象，但如果是字符串选择器，就需要把它转成 DOM 对象，作为最终挂载的容器），然后做一个 `if` 判断，如果组件对象没有定义 `render` 函数和 `template` 模板，则取容器的 `innerHTML` 作为组件模板内容；接着在挂载前清空容器内容，最终再调用 `app.mount` 的方法走标准的组件渲染流程（创建 `vnode`，再渲染 `vnode`）。**

在这里，重写的逻辑都是和 `Web` 平台相关的，所以要放在外部实现。此外，这么做的目的是既能让用户在使用 `API` 时可以更加灵活，也兼容了 `Vue.js 2.x` 的写法，比如 `app.mount` 的第一个参数就同时支持选择器字符串和 `DOM` 对象两种类型。

**从 `app.mount` 开始，才算真正进入组件渲染流程，核心渲染流程做的两件事情：创建 `vnode` 和渲染 `vnode`。**

### 核心渲染流程：创建 `vnode` 和渲染 `vnode`

#### 创建 `vnode`

首先，是创建 `vnode` 的过程。

**`vnode` 本质上是用来描述 `DOM` 的 `JavaScript` 对象**，它在 `Vue.js` 中可以描述不同类型的节点，比如普通元素节点、组件节点等。

##### 普通元素节点

什么是普通元素节点呢？举个例子，在 `HTML` 中我们使用 `<button>` 标签来写一个按钮：

```javascript
<button class="btn" style="width:100px;height:50px">click me</button>
```

可以用 `vnode` 这样表示`<button>`标签：

```javascript
const vnode = {
  type: 'button',
  props: { 
    'class': 'btn',
    style: {
      width: '100px',
      height: '50px'
    }
  },
  children: 'click me'
}
```

其中，`type` 属性表示 `DOM` 的标签类型，`props` 属性表示 `DOM` 的一些附加信息，比如 `style` 、`class` 等，`children` 属性表示 `DOM` 的子节点，它也可以是一个 `vnode` 数组，只不过 `vnode` 可以用字符串表示简单的文本 。

##### 组件节点

什么是**组件节点**呢？其实， `vnode` 除了可以像上面那样用于描述一个真实的 `DOM`，也可以用来描述组件。

我们先在模板中引入一个组件标签 `<custom-component>`：

```html
<custom-component msg="test"></custom-component>
```

我们可以用 `vnode` 这样表示 `<custom-component>` 组件标签：

```javascript
const CustomComponent = {
  // 在这里定义组件对象
}
const vnode = {
  type: CustomComponent,
  props: { 
    msg: 'test'
  }
}
```

组件 `vnode` 其实是**对抽象事物的描述**，这是因为我们并不会在页面上真正渲染一个 `<custom-component>` 标签，而是渲染组件内部定义的 HTML 标签。

除了上两种 `vnode` 类型外，还有纯文本 `vnode`、注释 `vnode` 等等，但是主线只需要研究组件 `vnode` 和普通元素 `vnode`即可。

另外，`Vue.js 3.0` 内部还针对 `vnode` 的 `type`，做了更详尽的分类，包括 `Suspense`、`Teleport` 等，且把 `vnode` 的类型信息做了编码，以便在后面的 `patch` 阶段，可以根据不同的类型执行相应的处理逻辑：

```javascript
const shapeFlag = isString(type)
  ? 1 /* ELEMENT */
  : isSuspense(type)
    ? 128 /* SUSPENSE */
    : isTeleport(type)
      ? 64 /* TELEPORT */
      : isObject(type)
        ? 4 /* STATEFUL_COMPONENT */
        : isFunction(type)
          ? 2 /* FUNCTIONAL_COMPONENT */
          : 0
```

##### `vnode`的优势

 **`vnode` 有什么优势呢？为什么一定要设计 `vnode` 这样的数据结构呢？**

* 首先是**抽象**，引入 `vnode`，可以把渲染过程抽象化，从而使得组件的抽象能力也得到提升。

* 其次是**跨平台**，因为 `patch vnode` 的过程不同平台可以有自己的实现，基于 `vnode` 再做服务端渲染、`Weex` 平台、小程序平台的渲染都变得容易了很多。

>  不过这里要特别注意，使用 `vnode` 并不意味着不用操作 DOM 了，很多人会误以为 `vnode` 的性能一定比手动操作原生 `DOM` 好，这个其实是不一定的。
>
> 因为，首先这种基于 `vnode` 实现的 `MVVM` 框架，在每次 `render to vnode` 的过程中，渲染组件会有一定的 `JavaScript` 耗时，特别是大组件，比如一个 `1000 * 10` 的 Table 组件，`render to vnode` 的过程会遍历 `1000 * 10` 次去创建内部 `cell vnode`，整个耗时就会变得比较长，加上 `patch vnode` 的过程也会有一定的耗时，当我们去更新组件的时候，用户会感觉到明显的卡顿。虽然 `diff` 算法在减少 `DOM` 操作方面足够优秀，但最终还是免不了操作 `DOM`，所以说性能并不是 `vnode` 的优势。

##### `vnode`的创建

回顾 `app.mount` 函数的实现，内部是通过 `createVNode` 函数创建了根组件的 `vnode` ：

```javascript
 const vnode = createVNode(rootComponent, rootProps)
```

`createVNode` 函数的大致实现：

```javascript
function createVNode(type, props = null
,children = null) {
  if (props) {
    // 处理 props 相关逻辑，标准化 class 和 style
  }
  // 对 vnode 类型信息编码
  const shapeFlag = isString(type)
    ? 1 /* ELEMENT */
    : isSuspense(type)
      ? 128 /* SUSPENSE */
      : isTeleport(type)
        ? 64 /* TELEPORT */
        : isObject(type)
          ? 4 /* STATEFUL_COMPONENT */
          : isFunction(type)
            ? 2 /* FUNCTIONAL_COMPONENT */
            : 0
  const vnode = {
    type,
    props,
    shapeFlag,
    // 一些其他属性
  }
  // 标准化子节点，把不同数据类型的 children 转成数组或者文本类型
  normalizeChildren(vnode, children)
  return vnode
}
```

通过上述代码可以看到，其实 `createVNode` 做的事情很简单，就是：对 `props` 做标准化处理、对 `vnode` 的类型信息编码、创建 `vnode` 对象，标准化子节点 `children` 。

拥有了这个 `vnode` 对象，接下来要做的事情就是把它渲染到页面中去。

#### 渲染 `vnode`

##### `render`

回顾 `app.mount` 函数的实现，内部通过执行这段代码去渲染创建好的 `vnode`：

```javascript
render(vnode, rootContainer)
const render = (vnode, container) => {
  if (vnode == null) {
    // 销毁组件
    if (container._vnode) {
      unmount(container._vnode, null, null, true)
    }
  } else {
    // 创建或者更新组件
    patch(container._vnode || null, vnode, container)
  }
  // 缓存 vnode 节点，表示已经渲染
  container._vnode = vnode
}
```

这个渲染函数 `render` 的实现很简单，如果它的第一个参数 `vnode` 为空，则执行销毁组件的逻辑，否则执行创建或者更新组件的逻辑。

##### `patch`

主要是看渲染 `vnode` 的代码中涉及的 `patch` 函数的实现：

```javascript
const patch = (n1, n2, container, anchor = null, parentComponent = null, parentSuspense = null, isSVG = false, optimized = false) => {
  // 如果存在旧节点, 且新旧节点类型不同，则销毁旧节点
  if (n1 && !isSameVNodeType(n1, n2)) {
    anchor = getNextHostNode(n1)
    unmount(n1, parentComponent, parentSuspense, true)
    n1 = null
  }
  const { type, shapeFlag } = n2
  switch (type) {
    case Text:
      // 处理文本节点
      break
    case Comment:
      // 处理注释节点
      break
    case Static:
      // 处理静态节点
      break
    case Fragment:
      // 处理 Fragment 元素
      break
    default:
      if (shapeFlag & 1 /* ELEMENT */) {
        // 处理普通 DOM 元素
        processElement(n1, n2, container, anchor, parentComponent, parentSuspense, isSVG, optimized)
      }
      else if (shapeFlag & 6 /* COMPONENT */) {
        // 处理组件
        processComponent(n1, n2, container, anchor, parentComponent, parentSuspense, isSVG, optimized)
      }
      else if (shapeFlag & 64 /* TELEPORT */) {
        // 处理 TELEPORT
      }
      else if (shapeFlag & 128 /* SUSPENSE */) {
        // 处理 SUSPENSE
      }
  }
}
```

**`patch` 本意是打补丁的意思，这个函数有两个功能，一个是根据 `vnode` 挂载 `DOM`，一个是根据新旧 `vnode` 更新 DOM。**对于初次渲染，先只分析创建过程，更新过程在后面分析。

在创建的过程中，patch 函数接受多个参数，目前只重点关注前三个：

1. 第一个参数 **`n1` 表示旧的 `vnode`**，当 `n1 为 null` 的时候，表示是一次挂载的过程；
2. 第二个参数 **`n2` 表示新的 `vnode` 节点**，后续会根据这个 `vnode` 类型执行不同的处理逻辑；
3. 第三个参数 **container 表示 DOM 容器**，也就是 `vnode` 渲染生成 `DOM` 后，会挂载到 `container` 下面。

对于渲染的节点，重点关注两种类型节点的渲染逻辑：对组件的处理和对普通 `DOM` 元素的处理。

##### 对组件节点的处理

###### `processComponent`

**先来看对组件的处理**。由于初始化渲染的是 `App` 组件，它是一个组件 `vnode`，所以我们来看一下组件的处理逻辑是怎样的。首先是用来处理组件的 `processComponent` 函数的实现：

```javascript
const processComponent = (n1, n2, container, anchor, parentComponent, parentSuspense, isSVG, optimized) => {
  if (n1 == null) {
    // 挂载组件
    mountComponent(n2, container, anchor, parentComponent, parentSuspense, isSVG, optimized)
  }
  else {
    // 更新组件
    updateComponent(n1, n2, parentComponent, optimized)
  }
}
```

该函数的逻辑很简单，如果 `n1 为 null`，则执行挂载组件的逻辑，否则执行更新组件的逻辑。

###### `mountComponent`

我们接着来看挂载组件的 `mountComponent` 函数的实现：

```javascript
const mountComponent = (initialVNode, container, anchor, parentComponent, parentSuspense, isSVG, optimized) => {
  // 创建组件实例
  const instance = (initialVNode.component = createComponentInstance(initialVNode, parentComponent, parentSuspense))
  // 设置组件实例
  setupComponent(instance)
  // 设置并运行带副作用的渲染函数
  setupRenderEffect(instance, initialVNode, container, anchor, parentSuspense, isSVG, optimized)
}
```

可以看到，**挂载组件函数 `mountComponent` 主要做三件事情：创建组件实例、设置组件实例、设置并运行带副作用的渲染函数。**

首先是创建组件实例，`Vue.js 3.0` 虽然不像 `Vue.js 2.x` 那样通过类的方式去实例化组件，但内部也通过对象的方式去创建了当前渲染的组件实例。

其次设置组件实例，`instance` 保留了很多组件相关的数据，维护了组件的上下文，包括对 `props`、插槽，以及其他实例的属性的初始化处理。

创建和设置组件实例这两个流程在后面进行详细分析。

###### `setupRenderEffect`

最后是运行带副作用的渲染函数 `setupRenderEffect`，重点来看一下这个函数的实现：

```javascript
const setupRenderEffect = (instance, initialVNode, container, anchor, parentSuspense, isSVG, optimized) => {
  // 创建响应式的副作用渲染函数
  instance.update = effect(function componentEffect() {
    if (!instance.isMounted) {
      // 渲染组件生成子树 vnode
      const subTree = (instance.subTree = renderComponentRoot(instance))
      // 把子树 vnode 挂载到 container 中
      patch(null, subTree, container, anchor, instance, parentSuspense, isSVG)
      // 保留渲染生成的子树根 DOM 节点
      initialVNode.el = subTree.el
      instance.isMounted = true
    }
    else {
      // 更新组件
    }
  }, prodEffectOptions)
}
```

**该函数利用响应式库的 `effect` 函数创建了一个副作用渲染函数 `componentEffect` （`effect` 的实现在响应式那里会具体说）。副作用，可以简单地理解为，当组件的数据发生变化时，effect 函数包裹的内部渲染函数 `componentEffect` 会重新执行一遍，从而达到重新渲染组件的目的。**

渲染函数内部也会判断这是一次初始渲染还是组件更新。这里只分析初始渲染流程。

**初始渲染主要做两件事情：渲染组件生成 `subTree`、把 `subTree` 挂载到 `container` 中。**

首先，是渲染组件生成 `subTree`，它也是一个 `vnode` 对象。这里要注意别把 `subTree` 和 `initialVNode` 弄混了（其实在 `Vue.js 3.0` 中，根据命名我们已经能很好地区分它们了，而在 `Vue.js 2.x` 中它们分别命名为 `_vnode` 和 `$vnode`）。比如说，在父组件 `App` 中里引入了 `Hello` 组件：

```vue
<template>
  <div class="app">
    <p>This is an app.</p>
    <hello></hello>
  </div>
</template>
```

在 `Hello` 组件中是 `<div>` 标签包裹着一个 `<p>` 标签：

```vue
<template>
  <div class="hello">
    <p>Hello, Vue 3.0!</p>
  </div>
</template>
```

在 `App` 组件中， `<hello>` 节点渲染生成的 `vnode` ，对应的就是 `Hello` 组件的 `initialVNode` ，为了好记，你也可以把它称作“组件 `vnode`”。而 `Hello` 组件内部整个 `DOM` 节点对应的 `vnode` 就是执行 `renderComponentRoot` 渲染生成对应的 `subTree`，我们可以把它称作“子树 `vnode`”。

我们知道每个组件都会有对应的 `render` 函数，即使你写 `template`，也会编译成 `render` 函数，而 `renderComponentRoot` 函数就是去执行 render 函数创建整个组件树内部的 `vnode`，把这个 `vnode` 再经过内部一层标准化，就得到了该函数的返回结果：子树 `vnode`。

渲染生成子树 `vnode` 后，接下来就是继续调用 `patch` 函数把子树 `vnode` 挂载到 `container` 中了。

那么我们又再次回到了 `patch` 函数，会继续对这个子树 `vnode` 类型进行判断，对于上述例子，`App` 组件的根节点是 `<div>` 标签，那么对应的子树 `vnode` 也是一个普通元素 `vnode`，那么我们**接下来看对普通 DOM 元素的处理流程。**

##### 对普通元素节点的处理

首先看处理普通 `DOM`元素的 `processElement` 函数的实现：

######  `processElement` 

```javascript
const processElement = (n1, n2, container, anchor, parentComponent, parentSuspense, isSVG, optimized) => {
  isSVG = isSVG || n2.type === 'svg'
  if (n1 == null) {
    //挂载元素节点
    mountElement(n2, container, anchor, parentComponent, parentSuspense, isSVG, optimized)
  }
  else {
    //更新元素节点
    patchElement(n1, n2, parentComponent, parentSuspense, isSVG, optimized)
  }
}
```

该函数的逻辑很简单，如果 `n1` 为 `null`，走挂载元素节点的逻辑，否则走更新元素节点逻辑。

###### `mountElement`

接着来看挂载元素的 `mountElement` 函数的实现：

```javascript
const mountElement = (vnode, container, anchor, parentComponent, parentSuspense, isSVG, optimized) => {
  let el
  const { type, props, shapeFlag } = vnode
  // 创建 DOM 元素节点
  el = vnode.el = hostCreateElement(vnode.type, isSVG, props && props.is)
  if (props) {
    // 处理 props，比如 class、style、event 等属性
    for (const key in props) {
      if (!isReservedProp(key)) {
        hostPatchProp(el, key, null, props[key], isSVG)
      }
    }
  }
  if (shapeFlag & 8 /* TEXT_CHILDREN */) {
    // 处理子节点是纯文本的情况
    hostSetElementText(el, vnode.children)
  }
  else if (shapeFlag & 16 /* ARRAY_CHILDREN */) {
    // 处理子节点是数组的情况
    mountChildren(vnode.children, el, null, parentComponent, parentSuspense, isSVG && type !== 'foreignObject', optimized || !!vnode.dynamicChildren)
  }
  // 把创建的 DOM 元素节点挂载到 container 上
  hostInsert(el, container, anchor)
}
```

**可以看到，挂载元素函数主要做四件事：创建 `DOM` 元素节点、处理 `props`、处理 `children`、挂载 `DOM` 元素到 `container` 上。**

首先是创建 `DOM` 元素节点，通过 `hostCreateElement` 方法创建，这是一个平台相关的方法，以下是它在 Web 环境下的定义：

```javascript
function createElement(tag, isSVG, is) {
  isSVG ? document.createElementNS(svgNS, tag)
    : document.createElement(tag, is ? { is } : undefined)
}
```

**它调用了底层的 `DOM API document.createElement` 创建元素，所以本质上 `Vue.js` 强调不去操作 `DOM` ，只是希望用户不直接碰触 `DOM`，它并没有什么神奇的魔法，底层还是会操作 `DOM`。**

另外，如果是其他平台比如 `Weex`，`hostCreateElement` 方法就不再是操作 `DOM` ，而是平台相关的 `API` 了，这些平台相关的方法是在创建渲染器阶段作为参数传入的。

创建完 `DOM` 节点后，接下来要做的是判断如果有 `props` 的话，给这个 `DOM` 节点添加相关的 `class`、`style`、`event` 等属性，并做相关的处理，这些逻辑都是在 `hostPatchProp` 函数内部做的，这里就不展开讲了。

接下来是对子节点的处理，我们知道 `DOM` 是一棵树，`vnode` 同样也是一棵树，并且它和 `DOM` 结构是一一映射的。

###### `hostSetElementText`

如果子节点是纯文本，则执行 `hostSetElementText` 方法，它在 Web 环境下通过设置 `DOM` 元素的 `textContent` 属性设置文本：

```javascript
function setElementText(el, text) {
  el.textContent = text
}
```

######  `mountChildren` 

如果子节点是数组，则执行 `mountChildren` 方法：

```javascript
const mountChildren = (children, container, anchor, parentComponent, parentSuspense, isSVG, optimized, start = 0) => {
  for (let i = start; i < children.length; i++) {
    // 预处理 child
    const child = (children[i] = optimized
      ? cloneIfMounted(children[i])
      : normalizeVNode(children[i]))
    // 递归 patch 挂载 child
    patch(null, child, container, anchor, parentComponent, parentSuspense, isSVG, optimized)
  }
}
```

子节点的挂载逻辑同样很简单，遍历 `children` 获取到每一个 `child`，然后递归执行 `patch` 方法挂载每一个 `child` 。注意，这里有对 `child` 做预处理的情况（后面编译优化的章节再进行详细分析）。

可以看到，`mountChildren` 函数的第二个参数是 `container`，而我们调用 `mountChildren` 方法传入的第二个参数是在 `mountElement` 时创建的 DOM 节点，这就很好地建立了父子关系。

另外，通过递归 patch 这种深度优先遍历树的方式，我们就可以构造完整的 DOM 树，完成组件的渲染。

###### `hostInsert` 

处理完所有子节点后，最后通过 `hostInsert` 方法把创建的 `DOM` 元素节点挂载到 `container` 上，它在 `Web` 环境下这样定义：

```javascript
function insert(child, parent, anchor) {
  if (anchor) {
    parent.insertBefore(child, anchor)
  }
  else {
    parent.appendChild(child)
  }
}
```

这里会做一个 `if` 判断，如果有参考元素 `anchor`，就执行 `parent.insertBefore` ，否则执行 `parent.appendChild` 来把 child 添加到 `parent` 下，完成节点的挂载。

因为 `insert` 的执行是在处理子节点后，所以挂载的顺序是先子节点，后父节点，最终挂载到最外层的容器上。

> **知识延伸：嵌套组件**
> 细心的你可能会发现，在 `mountChildren` 的时候递归执行的是 patch 函数，而不是 `mountElement` 函数，这是因为子节点可能有其他类型的 `vnode`，比如组件 `vnode`。
>
> 在真实开发场景中，嵌套组件场景是再正常不过的了，前面我们举的 `App` 和 `Hello` 组件的例子就是嵌套组件的场景。组件 `vnode` 主要维护着组件的定义对象，组件上的各种 `props`，而组件本身是一个抽象节点，它自身的渲染其实是通过执行组件定义的 `render` 函数渲染生成的子树 `vnode` 来完成，然后再 `patch` 。通过这种递归的方式，无论组件的嵌套层级多深，都可以完成整个组件树的渲染。

### 总结

以上主要分析了组件的渲染流程，从入口开始，一层层分析组件渲染。

![CgqCHl8EPLKAF8u5AAJHdNl56bM640](../image/CgqCHl8EPLKAF8u5AAJHdNl56bM640.png)

### 问题：

**问：** 我们平时开发页面就是把页面拆成一个个组件，那么组件的拆分粒度是越细越好吗？为什么呢？

**答：** 并不是拆分粒度越小越好。

原因：1、在我的日常开发中，有两种情况会去拆分组件，第一种是根据页面的布局或功能，将整个页面拆分成不同的模块组件，最后将这些模块组件拼起来形成页面；第二种是在实现第一部拆分出来的这些模块组件的时候，发现其中有一些模块组件具有相同或相似的功能点，将这些相似的功能点抽离出来写成公共组件，然后在各个模块中引用。无论是模块组件还是公共组件，拆分组件的出发点都和组件的大小粒度无关。可维护性和复用性才是拆分组件的出发点。2、对于组件的渲染，会先通过`renderComponentRoot`去生成组件的子树`vnode`，再递归`patch`去处理这个子树`vnode`。也就是说，对于同样一个`div`，如果将其封装成组件的话，会比直接渲染一个`div`要多执行一次生成组件的子树`vnode`的过程。并且还要设置并运行带副作用的渲染函数。也就是说渲染组件比直接渲染元素要耗费更多的性能。如果组件过多，这些对应的过程就越多。如果按照组件粒度大小去划分组件的话会多出很多没有意义的渲染子树和设置并运行副作用函数的过程。综上所述，并不是拆分粒度越小越好，只要按照可维护性和复用性去划分组件就好。



**问：**

```javascript
// 延时创建渲染器，当用户只依赖响应式包的时候，可以通过 tree-shaking 移除核心渲染逻辑相关的代码
function ensureRenderer() {
  return renderer || (renderer = createRenderer<Node, Element>(rendererOptions))
}
```

这段不是就加个 | 吗？跟`tree-shaking`有什么关系？

**答：**因为 `ensureRenderer` 的调用实际是用户在执行 `createApp` 的时候触发的，如果你只从 `Vue` 里引入 `reactivity` 相关 `API`，而不执行 `createApp`，也就不会执行 `ensureRenderer`，就不会创建渲染器，渲染器相关代码就会在打包过程中通过 `tree-shaking` 移除掉。

# 三、`Composition API`设计

# 四、编译过程以及优化

# 五、实用特性的实现原理

# 六、`Vue`内置组件的实现原理

# 七、`Vue Router`实现原理