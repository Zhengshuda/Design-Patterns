# 基于前端的设计模式

## 什么是设计模式

设计模式是一种书写代码的方式，日常中我们经常使用，只是我们不知道我们所写的代码能与设计模式扯上关系

它是一种为了解决一类或某类问题给出的简洁而优化的解决方案。 <b>它并不能解决所有问题。</b>

## 设计模式五大原则

所有设计模式都以下面五个原则为方向设计的

### 单一责任原则SRP

1. 一个类或一个方法只做一件事（SRP 原则是所有原则中最简单也是最难正确运用的原则之一
2. 如果功能过于复杂就拆分，每个部分都保持独立

### 开闭原则OCP

1. 对拓展开放，对修改封闭
2. 增加新需求时，拓展新代码，而非修改已有的代码

### 最少知识原则LKP

1. 软件实体应该尽可能少地与其他实体发生相互作用（外观模式、中介者模式）

### 接口分离原则ISP

1. 保持接口的独立单一，避免出现‘胖接口’

### 依赖反转原则DIP

1. 面向接口编程，依赖于抽象而不依赖于具体
2. 使用方法只关注接口而不关注具体类的实现

## 设计模式分类

创建性设计模式：工厂、单例、建造者、原型

结构化设计模式：外观、适配器、代理、装饰器、享元、桥接、组合

行为型设计模式：策略、模版方法、观察者、迭代器、责任链、命令、备忘录、状态、访问者、终结者、解释器

## 设计模式详解

### 工厂模式

工厂模式是最常见的一种模式，工厂模式可以根据抽象程度分成以下三种：<b>简单工厂</b>，<b>工厂方法</b>，<b>抽象工厂</b>

1. 简单工厂

   主要用来创建同一类对象，我们可以使用工厂模式来创建一个权限模块：

   ```js
   //User类
   class User {
     //构造器
     constructor(opt) {
       this.name = opt.name;
       this.viewPage = opt.viewPage;
     }
   
     //静态方法
     static getInstance(role) {
       switch (role) {
         case 'superAdmin':
           return new User({ name: '超级管理员', viewPage: ['首页', '通讯录', '发现页', '应用数据', '权限管理'] });
           break;
         case 'admin':
           return new User({ name: '管理员', viewPage: ['首页', '通讯录', '发现页', '应用数据'] });
           break;
         case 'user':
           return new User({ name: '普通用户', viewPage: ['首页', '通讯录', '发现页'] });
           break;
         default:
           throw new Error('参数错误, 可选参数:superAdmin、admin、user')
       }
     }
   }
   
   //调用
   let superAdmin = User.getInstance('superAdmin');
   let admin = User.getInstance('admin');
   let normalUser = User.getInstance('user');
   ```

   简单工厂的优点是只需要正确的参数，就能够获取需要的对象。无需知道创建的细节。不过这种工厂模式并不好：

   1.1 当增加一个角色的时候，我们需要修改getInstance方法，违反了开放-封闭原则。

   1.2 职责不单一，User类既要做判断，又要创建对象

   **所以，简单工厂只能作用于创建的对象数量较少，对象的创建逻辑不复杂时使用**。

2. 工厂方法

   工厂方法模式的本意是将**实际创建对象的工作推迟到子类**中，这样核心类就变成了抽象类。

   ```js
   class User {
     constructor(name = '', viewPage = []) {
       if(new.target === User) {
         throw new Error('抽象类不能实例化!');
       }
       this.name = name;
       this.viewPage = viewPage;
     }
   }
   
   class UserFactory extends User {
     constructor(name, viewPage) {
       super(name, viewPage)
     }
     create(role) {
       switch (role) {
         case 'superAdmin': 
           return new UserFactory( '超级管理员', ['首页', '通讯录', '发现页', '应用数据', '权限管理'] );
           break;
         case 'admin':
           return new UserFactory( '普通用户', ['首页', '通讯录', '发现页'] );
           break;
         case 'user':
           return new UserFactory( '普通用户', ['首页', '通讯录', '发现页'] );
           break;
         default:
           throw new Error('参数错误, 可选参数:superAdmin、admin、user')
       }
     }
   }
   
   let userFactory = new UserFactory();
   let superAdmin = userFactory.create('superAdmin');
   let admin = userFactory.create('admin');
   let user = userFactory.create('user');
   ```

   2.1 当新增一个对象，我们不用去修改User类

   2.2 User类职责单一，只用来创建对象

3. 抽象工厂

   抽象工厂并不是用来创建实例的，而是对产品类进行分类，如下面的例子：

   ```js
   function getAbstractUserFactory(type) {
     switch (type) {
       case 'wechat':
         return UserOfWechat;
         break;
       case 'qq':
         return UserOfQq;
         break;
       case 'weibo':
         return UserOfWeibo;
         break;
       default:
         throw new Error('参数错误, 可选参数:superAdmin、admin、user')
     }
   }
   
   let WechatUserClass = getAbstractUserFactory('wechat');
   let QqUserClass = getAbstractUserFactory('qq');
   let WeiboUserClass = getAbstractUserFactory('weibo');
   
   let wechatUser = new WechatUserClass('微信');
   let qqUser = new QqUserClass('QQ');
   let weiboUser = new WeiboUserClass('微博');
   ```

   这里刚好讲一下js中抽象类如何实现，当我们<b>定义</b>（因为js中没有抽象类一说）一个类为抽象类（或方法）时，我们可以throw new Error('')抛出一个异常，用于告知编码者，这是一个抽象类，需要重写该类（或方法）。

### 建造者模式

建造者模式就是将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。

这个没找到比较合适的例子。直接讲好处：

1. 将产品本身与创建过程解耦，可以使用相同的过程来创建不同的对象。
2. 可以将创建的各个步骤分解在不同的方法中，使创建过程更加清晰。
3. 符合开放封闭原则，新增加过程无需改动原有的那些方法。

### 原型模式

js中的原型继承特效来创建一个对象就是使用了原型模式。也就是创建一个对象作为另一个对象的prototype属性值。

```js
function Book = {
  getName: function () {
    return 'book name:' + this.name;
  }
}

// 方法一
function ComicBook = Object.create(Book, {
  id: {
    value: '1'
  },
  name: {
    value: 'comic'
  }
});

// 方法二
function ComicBook (name) {
  function F() {};
  F.property = Book;
  var f = new F();
  return f;
}
```

### 桥接模式

桥接模式就是将抽象部分与它的实具体现部分分离，使它们都可以独立地变化。

比较典型的例子就是forEach函数的实现

```js
let forEach = function (arr, fn) {
  for (let i = 0;i < arr.length; i++) {
    var item = arr[i];
    fn.call(item, ...arguments);
  }
}
```

具体实现就是fn，桥接模式不关心fn的具体实现，fn里面的逻辑也不会被影响到，这也是符合开放-封闭原则的设计模式，可以随意替换fn函数。

### 责任链模式

例如koa2的中间件，我们使用app.use，我们会不断往下传递，这就类似一条责任链。

在app.use的时候，我们会把中间件加入middleware里面，然后在listen的时候逐步运行。有next就一直往下执行。

![image-20201207223004034](./image-20201207223004034.png)

再举一个例子，一个密码校验，我们需要校验的条件有三个：长度大于8位、以字母开头、以数字结尾

我们能很快写出这样的例子：

```js
function lengthValid (val) {
  if (val < 8) {
    return '长度最少为8位';
  }
  
  return true;
}

function letterValid (val) {
  if (/^[a-zA-Z]/.test(val)) {
    return '必须以字母开头';
  }
  
  return true;
}

function numberValid (val) {
  if (/[0-9]$/.test(val)) {
    return '必须以数字结尾';
  }
  
  return true;
}

// 方法一
function valid (val) {
  if (lengthValid(val) !== true) {
    return false;
  }
  
  if (letterValid(val) !== true) {
    return false;
  }
  
  if (numberValid(val) !== true) {
    return false;
  }
  
  return true;
}

// 方法二
function valid2 (val) {
	let chains = [lengthValid, letterValid, numberValid];
  
  for (let i = 0;i < chains.length; i++) {
    let fn = chains[i];
    let result = fn(val);
    if (result !== true) {
      console.log(result);
      break;
    }
    
    // 可不用，这里只是用于表示责任链
    continue;
  }
}
```

使用责任链模式，我们保证了每个对象都有执行的机会。

### 适配器模式

适配器模式是将一个类（或对象）的接口（方法或属性）转化成客户希望的另外一个接口（方法或属性），适配器模式主要是让原本不兼容而不能一起工作的类（或对象）可以一起工作。

一般我们用在数据处理上，例如bff层，还有我们项目中使用的structure，就是将一些数据转化成我们想要的样子以适配复杂多变的场景。bff层还能实现跨段的数据处理，假设我们现在有pc端和移动端，两边两套代码依赖一个接口想要获得不同的数据结构，这时候就可以借助适配器，将数据格式转换成我们希望的数据输出给不同的前端设备。

还有浏览器兼容的问题，我们也可以借助适配器模式来解决，例如下面这一段代码：

```js
function on(target, event, callback) {
    if (target.addEventListener) {
        // 标准事件监听
        target.addEventListener(event, callback);
    } else if (target.attachEvent) {
        // IE低版本事件监听
        target.attachEvent(event, callback)
    } else {
        // 低版本浏览器事件监听
        target[`on${event}`] = callback
    }
}
```

我们希望调用on事件就能实现监听，但是不同浏览器的api可能不一样，这时候就可以封装一个函数，作为适配器。

适配器模式经常与下面几种模式做对比：

| 模式                     | 区别                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 适配器模式 vs 代理模式   | 代理模式是为了管控原有的对象，代理并不是为了兼容，并且代理希望与本体一样对外接口保持一致。<br>适配器模式是一个转换器，目的是为了兼容。并不会去处理请求，而是将请求原封不动的交给本体去处理。 |
| 适配器模式 vs 装饰器模式 | 装饰器模式是为对象添加功能，可添加多次，形成一条装饰链。<br>适配器模式只会包装一次。 |
| 适配器模式 vs 外观模式   | 外观模式提供对子系统各元件功能的简化为共同层次的调用接口，它主要起到"简化作用"。<br>适配器模式主要用于适配。 |

适配器模式是"**转换行为**"，代理模式是“**控制访问行为**”，装饰器模式是“**新增行为**”，外观模式是一种"**简化行为**"。

### 代理模式

适配器模式与代理模式最相似，同样都是创建一个新对象（包装一次），实现对本体的调用。 

如果能够合理使用代理模式，我们将很好地体现单一责任原则和开放-封闭原则。

代理模式也不难，例如Vue里面用到的Object.defineProperty还有es6提供的proxy，都能够很方便地帮我们实现代理模式。

前端使用代理模式基本上有以下三个场景：<b>缓存代理</b>,<b>验证代理</b>,<b>实现私有属性</b>

1. 缓存代理

   缓存代理能将一些开销很大的函数或方法的结果进行缓存，再次调用该函数时，如果参数一致，那么直接返回结果，不需要进行计算。例如前端分页（实际上不会这么做，因为列表可能是一直在变化的）。

   写一段yy的代码作为例子，看个乐

   ```js
   let urlPool = {};
   function getAjax ({url, type='get'}) {
     
     	// 如果请求过了，那么就调用上一次的结果 
     	if (urlPool[url]) {
         return urlPool[url];
       }
     
     	// 没有请求过则发送一个新的请求
     	let data = ajax.get(url);
     	urlPool[url] = data;
     	return data;
   }
   ```

2. 验证代理

   vue中的computed就能很方便地帮我们进行验证，还是写段代码看个乐

   ```js
   export default {
     data: {
       return {
       	girlAge: 18
    		};
     }
     computed: {
       age: {
         get: function () {
           return this.girlAge;
         },
         set: function (newValue) {
           
           // 女人至死是18
           if (newValue > 18) {
             console.log(`你才${newValue}，你全家都${newValue}!!!`);
             this.girlAge = 18
           }
           
           this.girlAge = newValue;
         }
       }
     }
   }
   ```

   通过验证代理在设置值的时候进行校验，如果超过18岁，则骂一句然后置为18。比18小则以小的为准。

3. 实现私有属性

   类似于闭包

   ```js
   var animal = new Proxy(Animal, {
       get(target, key){
           return target[key]
       },
   
       set(target,key,value){
           if(key !=='food'){
               target[key] = value;
           }
       }
   })
   ```

   

###  装饰器模式

高版本es中就有装饰器这样一个属性，通过@xxx注入，就像java一样。我们在使用ts-vue中，也会用到vue提供给我们的装饰器来对一些内部对象进行装饰，例如@Prop,@Watch,@Emit等等

下面有一段例子是我前段时间在运维管理平台一时想到写下的，就是登录模块，我们原本是指用调用/ticket接口进行校验就好的，但是后面由于安全问题，我们需要对用户输入的密码进行加盐，原本/ticket接口已经有几十行代码里，如果混入加盐的代码，这串代码可读性将会非常差。我的做法是保持原有的校验，在校验前使用装饰器对formData进行加工，然后返回给/ticket接口处理。这个装饰器的作用就是判断是否合法并对数据进行加工。

为什么想到用装饰器模式：因为我不确定后面是否还会有类似的问题出现，假设一个很坏的情况，我们的加盐加密算法被破解了（大概率不会，除非私钥泄漏了），我们需要在原有的基础上再加一次盐或对加盐算法进行改进，那我们还可以再使用一个装饰器，与责任链模式一样一步一步往下传，直到最后调用/ticket接口。

### 外观模式

外观模式其实很好理解，就是把一堆对象包装成一个接口抛出，看下面一段代码就能理解了

```js
// user.js
import person from 'person';
import student from 'student';

/* 假设person和student下有这样的代码 */
person.sayName = function() {
  console.log('我是person');
};

student.sayName = function() {
  console.log('我是student');
};

export default {
  person,
  student
};
```

这样我们就可以通过下面的方法：

```js
import user from 'user.js';

user.person.sayName();
user.student.sayName();
```

这里就能体现外观模式的作用，实际上就是把两个对象结合成一个对象，通过一个外观模式就可以调用这两个对象的函数。

很好理解！这是我们很常写的写法，但我们可能不知道这竟然还用到了外观模式。

### 模版方法模式

模版方法模式分成两部分组成，一个是抽象的父类，一个是具体实现的子类。

可以看下面一段代码:

```js
/* PersonMixin.js */
export default {
  data: {
    return {
    	name: '',
    	age: 0,
    	area: ''
  	};
  },
  
  // 我们在created的时候调用下面三个方法，但这三个方法都是一个固定值，我们并没办法改变，扩展性-1-1-1
  created () {
		this.initName();
    this.initAge();
    this.initArea();
	};

  methods: {
    initName () {
      this.name = 'zsd';
    },
      
    initAge () {
      this.age = 18;
    },
      
    initArea () {
      this.area = '汕头';
    }
  }
}

/* son.vue */
export default {
  
  mixins: [PersonMixin]

  methods: {
    initName () {
      this.name = 'son';
    },
      
    initAge () {
      this.age = 1;
    },
      
    initArea () {
      this.area = '深圳';
    }
  }
}
```

上面的PersonMixin的created就是我们的模版，son就是具体实现的子类。由于created里面调了三个函数，我们只需要重写这三个函数来覆盖父类的抽象方法即可。如果使用ts-vue我们还可以使用abstract关键字来修饰。

vue中的template也是模版，我们写的插槽就是一种模版方法模式，我定义一个插槽，我并不用关心插槽里面的内容是什么，爱是什么是什么，我就提供这样一个模版，你传进来就好了。这也符合一种开放-封闭原则，外面只需要覆盖掉我这个slot就可以了，扩展性极高。

### 备忘录模式

可以恢复到对象之前的状态。刚才讲的那个缓存代理其实也是一个备忘录模式。也是使用在分页中，如果我们加载过第一页的数据，现在切到第二页再切回第一页，我们希望是不再下发请求，这时候就可以使用备忘录模式，“把之前的数据从备忘录里面拿出来”。这里就不展开详讲了。

### 状态模式

状态模式是对象行为能根据状态的变化而变化。

在我们写项目代码的过程中经常有那种一个值的变化引发一系列变化的情况，假如我现在一台云主机从开机状态变成关机状态，这时候页面要发生什么事？

1. 首先关机按钮置灰
2. 按钮的icon要变化
3. 如果有任务在进行，要停掉该任务
4. 进度条变红

根据上面列的点我们能写出一个状态模式的代码

```js'
const STATUS_TASK {
	off: function () {
		this.btn.disabled = true;
		this.icon = 'close';
		if (this.task) {
			this.task.abort();
		}
		this.progress.color = 'red';
	},
	on: function () {
		// 上面的操作全反过来
	}
}

STATUS_TASK[status]();
```

这样就是一个简单的策略模式，通过状态的改变触发一系列的变化，我们把它封装好，后面直接调用就好，如果有新的状态我们可以直接加到策略表里面，符合开放封闭原则。

### 策略模式

策略模式是指我们定义一系列算法，并且把他们封装起来。

讲到策略模式，任何一篇文章都会以奖金计算为例子：

```js
var calculateBouns = function(salary,level) {
    if(level === 'A') {
        return salary * 4;
    }
    if(level === 'B') {
        return salary * 3;
    }
    if(level === 'C') {
        return salary * 2;
    }
};
```

上面这个代码能解决我们当前的问题，但是存在以下问题：

1. 有太多的if-else，有的人会说用switch来替换，但实际上switch和if-else在这里的场景差别并不大，还是要写一堆判断
2. 如果新加入一个等级，我们还需要修改这个函数，不符合开放-封闭原则
3. 复用率差，如果其他地方有类似的算法，但存在一点差别，我们并不能复用。

接下来看看js中的策略模式实现，我们会发现这不就是我平时写的代码吗？

```js
var calculateMap = {
  'A': function (salary) {
    return salary * 4;
  },
  'B': function (salary) {
    return salary * 3;
  },
  'C': function (salary) {
    return salary * 2;
  }
};

var calculateBouns = function (salary, level) {
  return calculateMap[level](salary);
}
```

真香！我们还可以联想到vue里面的动态组件，假如我们要渲染一个动态组件：

```vue
<template>
	<div>
    <keep-alve>
  		<component :is="current"></component>
    </keep-alve>
  </div> 
</template>
<script>
      
// 策略模式
const COMPONENT_MAP = {
  'A': () => import('../A.vue'),
  'B': () => import('../B.vue'),
	'C': () => import('../C.vue')
};

export default {
	props: {
    type: {
      type: String,
      default: 'A'
    }
  },
  computed: {
    current () {
   		return COMPONENT_MAP[this.type];
    }
  }
};   
</script>
```

这样我们就实现了按需加载一个组件了，可以用于异步加载一个大组件提高性能。

### 观察者模式/发布订阅模式

讲到观察者模式，有些人会直接把观察者模式等同于发布订阅模式。其实这样的说法并不正确。这里放到一起讲也方便做个对比：

| 模式         | 相同点                                                       | 不同点                             |
| ------------ | ------------------------------------------------------------ | ---------------------------------- |
| 观察者模式   | 都是类似好莱坞原则“没事别找到，有事我找你”                   | 观察者与被观察者两者直接通信       |
| 发布订阅模式 | 当一个状态改变时，所有订阅（或观察）这个状态的对象能接收到改变的信号。 | 发布者和订阅者中间借助了一个中间商 |

可以借助这样一个例子：vue里面的通信方式，eventbus和父子间通信。eventbus作为一个中间商，当子节点想通知父节点的时候，可以告知eventbus，然后eventbus去通知子节点，这就是发布订阅模式。如果子节点直接抛出个事件给父节点，则是观察者模式。

**两者的区别就是“有没有中间商赚差价”**

我们都知道vue源码中用到了发布订阅模式，就是通过Object.defineProperty对数据进行拦截，转换成getter和setter。然后在初始化data的时候对数据进行监听，当有订阅者时，就把该订阅者加入一个Dep数组中，作为依赖收集，当数据发生变化时，就去遍历Dep，把事件发布给各个订阅者。

### 组合模式

组合模式又叫部分-整体模式，它将对象组合成一个树状结构，我们只需要调用根节点就可以对所有的成员做相同的操作。父类和子类必须具有相同的接口（方法），只不过它们相同的方法具有的功能不相同。

可以看看组件库中对组合模式的使用：

表单校验中，我们可以调用validate方法对表单进行校验，表单里面有很多子项，每个子项例如（sd-select、sd-textarea等）都有一个validate方法，我们在校验表单的时候只需要调用sd-form的validate方法，就能触发所有子项的校验。写个伪代码：

```js
class Form {
  validate () {
    Select.validate();
    TextArea.validate();
    ...
  }
}

class Select {
  static validate () {
    console.log('校验select');  
  }
}

class TextArea {
  static validate () {
  	console.log('校验textarea');  
  }
}
```

可以看到sd-form就是将子项全部组合起来调用，使用者只用关注form的validate方法就好。这样设计的好处是如果后面我们需要自己写一个方法，就能跳过父级直接调用子级的同名方法，减少了阅读源码的困难。（个人感受）

### 命令模式

请求以命令的模式包裹在对象中，并传给调用对象。调用对象寻找可以处理该命令的合适的对象，使该对象执行该命令。

有时候我们需要向某些对象发送请求，但我们不知道请求的接受者是谁，也不知道被请求的操作是什么，如果我们希望以一种松耦合的方式来设计程序，那么使用命令模式再合适不过了。

下面我举一个例子来讲解一下命令模式：我们系统中的概览页，上面有各种各样的组件，比如柱状图、条形图、折线图、详情展示等等...这些组件各自的请求都不一样，但都依赖于用户id（userID），这时候我们就可以写出下面的概览页代码。

```vue
<template>
	<div>
    <A :userId="userID" ref="A"></A>
    <B :userId="userID" ref="B"></B>
    <C :userId="userID" ref="C"></C>
    <D :userId="userID" ref="D"></D>
  </div>
</template>

<script>
export default {
  data: {
    return {
    	userID: ''
  	};
  },
  
  computed: {
    refList () {
      return [this.$refs.A, this.$refs.B, this.$refs.C, this.$refs.D];
    }
  },
  
  watch: {
    userID (val) {
      this.refList.forEach(ref => {
        ref.$emit('show', val);// 或ref.show();
      });
    }
  },
  
  mounted () {
  
  	// 假装有个获取id的请求
  	this.api().then(data => {
      this.userID = data.id;
    });
	}
}
</script>
```

我们向组件A、B、C、D分别发送show命令，然后里面监听到show命令之后就开始初始化数据。这样我们就可以将各个组件的解耦开，各个组件互不影响。如果后期这个页面还要增加E、F、G组件，我们能很方便地扩展，只要对应的组件监听相同的命令‘show’就可以了。这也是符合开放封闭原则的，具体逻辑函数我们不需要改动。

### 享元模式

享元模式是一种天生为系统优化而生的设计模式。享元模式要求将对象的属性划分为内部状态和外部状态，目标是尽量减少共享对象的数量，也就是节约内存。（资源重复利用）

看过这样一个例子，我开淘宝店，有100套衣服，50套男装和50套女装，我可以找100个模特来拍照片。但是这样我将花费大量的钱（内存），后面如果再进一批货就更顶不住了。

拆开想一想，其实可以找一男一女两个模特就好了，让他们穿50套，虽然耗费的时间多了，但是我省钱了（时间换空间）。

我们把性别划分为内部属性，内部属性一般是存储在对象内部，可以被共享，通常不会改变的。外部属性就是衣服，外部属性不能被共享。这样就可以构造一个享元模式了。

这里我们可以和单例模式做一下对比：

| 模式                 | 对比                                                         |
| -------------------- | ------------------------------------------------------------ |
| 享元模式 vs 单例模式 | 单例一个类只能有一个对象，目的为了共享。<br>享元模式可以理解成一组共享的对象集合。享元模式可以有多个对象，目的为了节约内存。 |

资源池其实就是一个享元，初始化的时候构造好几个资源放入资源池，在有线程调用的时候把该资源**借给**线程，用完了就丢回资源池，等待下一个调用。

nodejs的线程池也是这样，nodejs不是单线程的吗？只是主线程为单线程，实际上io操作会交给线程池里的线程去处理，用完之后放回线程池等待下一个io指令。

### 单例模式

单例模式是一种很常见的模式，就是保证全局只有一个实例。

优点：

1. 对于频繁使用的对象，可以省略创建对象所花费的时间.

2. 由于 new 操作的次数减少，对系统内存的使用频率也会降低.

3. 全局唯一性,可以保证全局数据和功能的共享.

常见的单例：

1. 浏览器的window对象
2. 类库中的全局对象，例如vuex
3. 全局变量

单例模式分成懒汉式和饿汉式

懒汉式：

```js
var single = function () {
  let instance;
  var Person = function () {
    this.name: = 'zsd';
  }
  
  return {
    getInstance: function () {
    	if (!instance) {
        instance = new Person()
      }
      
      return instance;
  	}
  }
}
```

饿汉式：

```js
var single = function () {
  let instance;
  var Person = function () {
    this.name: = 'zsd';
  }
  
  if (!instance) {
    instance = new Person();
  }
  
  return instance;
}
```

可以看到，懒汉式会延迟执行，直到你调用初始化方式时才实例化一个对象，饿汉式不一样，在你调用函数时立刻就会初始化一个对象出来，体现了有多‘饿’

## 怎么写好代码

那肯定是多使用设计模式。但往往我们使用设计模式是在代码优化阶段而不是第一次编写就能够使用各种各样的设计模式来优化我们的代码。

### 提炼函数

1. 避免出现超大函数
2. 提炼出来的函数能够进行复用
3. 提炼出来的函数容易被覆写
4. 提炼出来的函数如果有个好的命名能够起到注释的作用

### 合并重复的条件片段

如果函数内有多个条件分支语句，而条件语句内有重复代码，我们就可以把其提炼出来

```js
function judge (val) {
  let a = null;
  if (val < 0) {
    a = null;
    log(a);
  } else if (val === 0) {
    a = 0;
    log(a);
  } else {
    a = 1;
    log(a);
  }
}
```

提炼后：

```js
function judge (val) {
  let a = null;
  if (val < 0) {
    a = null;
  } else if (val === 0) {
    a = 0;
  } else {
    a = 1;
  }
  
  log(a);
}
```

### 把条件分支提炼成函数

条件分支在我们迭代过程中肯定会越变越长，如果纯靠if-else来判断的话，后期可读性很差

```js
function fn (val, type, isMaster) {
  
  // 如果值大于0且是admin登录且当前scp是主节点
	if (val > 0 && type === 'admin' && isMaster && ...) {
    
  }
}
```

改动后：

```js
function fn (val, type, nodeType) {
  
  // 如果值大于0且是admin的主节点
	if (val > 0 && isAdminMaster()) {
    
  }
}

// 是否是admin的主节点
function isAdminMaster (type, nodeType) {
  return type === 'admin' && nodeType === 'master'l
}
```

### 合理利用循环

### 函数提前返回

### 传递参数使用对象代替过长的参数列表

一般来说，超过2-3个参数，我们就要考虑改为对象传参了，如果在设计阶段，我们并不能确定后面会有多少参数，就提前设为对象。

### 减少参数数量

可以给参数默认值，如果能通过其他参数算出来的值也不必传

### 合理使用三目运算

太复杂的条件语句我们还是使用if-else或switch来替代，太复杂的语句我们使用三目就增大了阅读难度

### 合理使用链式调用

链式调用虽然方便，比如弹窗，我们可以let win = this.xxx.show().$on('',() => {}).$on('', () =>{})

使用起来虽然很方便，但是遇到错误时我们将会很难定位

### 使用return退出多重循环

