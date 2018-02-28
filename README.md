# MVVM
实现一个MVVM框架
预览： https://majianyang666.github.io/MVVM/index.html

## 原理：
1.使用Object.defineProperty的get和set方法进行数据劫持
2.使用发布订阅模式
3.单项绑定和双向绑定

如下代码，data 里的name会和视图中的{{name}}一一映射，修改 data 里的值，会直接引起视图中对应数据的变化。
```

<body>
  <div id="app" >{{name}}</div>

  <script>
    function mvvm(){
        //todo...
    }
    var vm = new mvvm({
      el: '#app',
      data: { 
          name: 'jirengu' 
      }
    })
  </script>
<body>
```
## 如何实现上述 mvvm 呢？

观察者模式和数据监听：

主题(subject)是什么？
观察者(observer)是什么？
观察者何时订阅主题？
主题何时通知更新？
上面的例子中，主题应该是data的 name 属性，观察者是视图里的{{name}}，当一开始执行mvvm初始化(根据 el 解析模板发现{{name}})的时候订阅主题，当data.name发生改变的时候，通知观察者更新内容。 我们可以在一开始监控 data.name （Object.defineProperty(data, 'name', {...})），当用户修改 data.name 的时候调用主题的 subject.notify


## 思路：
定义一个mvvm类，
class mvvm {
    constructor (opts) {
        this.init(opts)
        observe(this.$data)
        this.compile()
    }
    init () {}
    compile () {}
}
在用它构造一个实例的时候主要会用到observe和compile方法。
observer进行数据的劫持，
compile会在编译的时候对每一个属性创建一个观察者，当在需要的时候观察者会订阅主题。
当数据被修改的时候，主题就会发布跟新，每一个订阅的观察者都会进行修改。

## 技术难点：
何时订阅：
设定一个currentobsever 。在合适的时间，在我需要看这个值的时候，我给他设置currentobsever = this，我再去调用它的属性，他会调用get，就会进行订阅。普通情况下。当用户去获取值的时候（下次）已经绑定了，就不会有currentobsever（每用一次设currentobsever = null）,只有再new时会有。

## 小技巧：
 用Object.defineProperty把$data 中的数据直接代理到当前 vm 对象
 用bind让 this.$methods 里面的函数中的 this，都指向 vm

