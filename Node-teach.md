
# Table of Contents

1.  [Node 简介](#org1b217e9)
    1.  [Node历史](#org099eccd)
    2.  [nodejs和chrome浏览器的组件构成](#org24b9111)
2.  [Node 特点](#org2f3fe7b)
    1.  [异步IO](#org53d192f)
    2.  [异步编程](#org4425b85)
    3.  [单线程](#orgbf892d4)
    4.  [跨平台](#org01a0b70)
3.  [Node 使用场景](#orgf74120b)
    1.  [IO密集型](#org83cc6db)
    2.  [Nodejs是否不擅长CPU密集型业务](#orgd89d1ab)
    3.  [分布式应用](#org7d81872)
4.  [Nodejs模块机制](#org5a1addb)
    1.  [优先从缓存加载](#orga87d012)
    2.  [文件定位](#orgc77dc44)
    3.  [文件模块编译](#orgae416bb)
        1.  [javascript模块编译](#org9effa9c)
        2.  [C/C++ 模块的编译](#org2f6ef5a)
        3.  [JSON文件编译](#org3acd77e)
    4.  [核心模块的编译](#orgdb1956b)
        1.  [javascript核型模块编译](#org8311ce2)


<a id="org1b217e9"></a>

# Node 简介


<a id="org099eccd"></a>

## Node历史

   Ryan Dahl 一名资深的C++程序员，在创造Nodejs之前主要工作是设计高性能web服务器，在经过一系列的失败之后，
他找到了设计高性能web服务器的几个要点：事件驱动，非阻塞IO。
   C、Lua、Ruby、Haskell, 最后选中js的原因是，比C语言开发门槛低，比Lua历史包袱少（已经有很多阻塞库），
js在后端一直没有什么市场，为其导入非阻塞IO没有额外阻力。另外JS在浏览器中有广泛的事件驱动方面的应用，
暗合Ryan的喜好。当时Chrome的V8摘得性能桂冠，而且是BSD许可证发布。
   起初项目叫web.js，就是一个web服务器，但是项目的发展超过了预期，变成了网络应用的基础框架。 


<a id="org24b9111"></a>

## nodejs和chrome浏览器的组件构成

Chrome和nodejs组件的构成如下图所示，浏览器除了V8作为Javascript的引擎之外，还后一个webkit布局引擎。
[Chrome和nodejs组件的架构](https://www.processon.com/view/link/5bc6a1fbe4b0d4d65c31124a)


<a id="org2f3fe7b"></a>

# Node 特点


<a id="org53d192f"></a>

## 异步IO

在Node中，绝大多数的操作都是以异步的方式进行调用。Ryan Dahl在底层构建了很多异步IO的API。

    var fs = require('fs');
    
    fs.readFile('Chrome_vs_Nodejs.png', function (err, file) {
        console.log('读取文件完成');
    });
    console.log('发起读取文件');

[异步I/O调用时序图](https://www.processon.com/view/link/5bc7e510e4b0d4d65c32ff18)


<a id="org4425b85"></a>

## 异步编程


<a id="orgbf892d4"></a>

## 单线程

   Nodejs是单线程的，单线程的好处是不用像多线程变成那样处处在意状态同步问题，这里没有死锁的存在，也没有线程上下文
切换的性能开销。
   同时单线程也有一下弱点：
   \*无法利用多核CPU。
   \*错误会引起整个应用的退出，应用的健壮性值得考验。
   \*大量计算占用CPU导致无法继续调用异步IO。
   Nodejs采用了与Web Workers相同的思路来解决单线程中大计算量的问题：child<sub>process</sub>。子进程的出现，意味着Nodejs
可以从容应对单线程在健壮性和无法利用多核CPU方面的问题。通过将计算发放到各个子进程，可以将大量计算分解掉，然后再通过
进程之间的事件消息来传递结果。


<a id="org01a0b70"></a>

## 跨平台

   起初，Nodejs只能在\*nix平台上运行，从v0.6.0版本开始，node以ing可以在windows平台上运行了。兼容性主要是靠libuv
在多平台上的实现。


<a id="orgf74120b"></a>

# Node 使用场景


<a id="org83cc6db"></a>

## IO密集型

Nodejs面向网络且擅长并行IO，能够有效地组织起更多的硬件资源，从而提供更多的服务。
IO密集的优势主要在于Node利用事件循环的处理能力，而不是启动每一个线程为每一个请求服务，占用资源极少。


<a id="orgd89d1ab"></a>

## Nodejs是否不擅长CPU密集型业务

V8的执行效率是非常高的，如下列表分别是使用各种语言进行了n=40的斐波那契数列计算。

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />

<col  class="org-right" />
</colgroup>
<tbody>
<tr>
<td class="org-left">语言</td>
<td class="org-left">耗时</td>
<td class="org-right">排名</td>
</tr>


<tr>
<td class="org-left">C</td>
<td class="org-left">0m0.202s</td>
<td class="org-right">1</td>
</tr>


<tr>
<td class="org-left">Nodejs(C++模块)</td>
<td class="org-left">0m1.001s</td>
<td class="org-right">2</td>
</tr>


<tr>
<td class="org-left">java</td>
<td class="org-left">0m1.305s</td>
<td class="org-right">3</td>
</tr>


<tr>
<td class="org-left">Go</td>
<td class="org-left">0m1.667s</td>
<td class="org-right">4</td>
</tr>


<tr>
<td class="org-left">Scala</td>
<td class="org-left">0m1.808s</td>
<td class="org-right">5</td>
</tr>


<tr>
<td class="org-left">LuaJIT</td>
<td class="org-left">0m2.579s</td>
<td class="org-right">6</td>
</tr>


<tr>
<td class="org-left">Nodejs</td>
<td class="org-left">0m2.872s</td>
<td class="org-right">7</td>
</tr>


<tr>
<td class="org-left">Lua</td>
<td class="org-left">0m40.709s</td>
<td class="org-right">8</td>
</tr>


<tr>
<td class="org-left">PHP</td>
<td class="org-left">1m17.728s</td>
<td class="org-right">9</td>
</tr>


<tr>
<td class="org-left">Python</td>
<td class="org-left">1m17.979s</td>
<td class="org-right">10</td>
</tr>


<tr>
<td class="org-left">Perl</td>
<td class="org-left">2m41.259s</td>
<td class="org-right">11</td>
</tr>


<tr>
<td class="org-left">Ruby</td>
<td class="org-left">3m35.135s</td>
<td class="org-right">12</td>
</tr>
</tbody>
</table>

   CPU密集型应用给Nodejs带来的主要挑战是：由于javascript单线程的原因，如果有长时间运行的计算，将会导致时间片不能释放，
使得后续IO无法发起。但是适当分解大型运算任务为多个小任务，使得运算能够适时释放，不阻塞IO调用的发起，这样即可同时
享受到并行异步IO的好处，又能充分利用CPU。
   Nodejs可以通过编写C++扩展的方式更高效地利用CPU，将一些V8不能做到性能极致的地方通过C++来实现。


<a id="org7d81872"></a>

## 分布式应用

   案例：阿里巴巴的数据平台对Nodejs的应用，开发来中间层应用NodeFox，ITier，将数据库集做了划分和映射，查询调用依旧
针对单张表进行SQL查询，中间层分解查询SQL，并行地去多台数据库中获取数据并合并。NodeFox能实现对多台数据库查询，如同
查询一台MySQL一样，而ITier更强大，查询多个数据库（不同类型，并不都是MySQL）如同查询一台数据库一样。
   这个案例其实是高效利用并行IO充分压榨硬件资源的过程。


<a id="org5a1addb"></a>

# Nodejs模块机制

  在Nodejs引入模块，需要经历如下3个步骤。
1）路径分析
2）文件定位
3）编译执行
  在Nodejs中模块分为两类：一类是Nodejs提供的模块，称为核心模块；另一类是用户编写的模块，称为文件模块。

-   核型模块部分在源代码的编译过程中，编译进了二进制可执行文件。在Nodejs进程启动时，部分核心模块就被直接家在进来
    内存，所以这部分核型模块引入时，文件定位和编译这两个步骤可以省略掉，并在路径分析中优先判断，所以它的加载速度是
    最快的。
-   文件模块则是在运行时动态加载，需要问政的路径分析、文件定位、编译执行过程，速度比核心模块慢。


<a id="orga87d012"></a>

## 优先从缓存加载

   Nodejs对引入的模块都会进行缓存，Nodejs缓存的是编译和执行之后的对象。不论是核心模块还是文件模块，require()
方法对相同模块的二次加载都一律采用缓存优先的方式，这是第一优先级。不同的是核型模块的缓存检查优先于文件模块的缓存
检查。


<a id="orgc77dc44"></a>

## 文件定位

   1.模块标识符分析，
   模块标识符就是require方法传递进去的参数，主要分为以下几类。
   -核心模块，如：http fs paths
   -.或者..开头的相对路径文件模块。
   -绝对路径文件模块
   -非路径形式的文件模块
   核型模块从内存中加载，相对路径的文件模块从require会转换成绝对路径加载，非文件的自定义模块会按照一定的搜索路
径来查找。

    console.log(module.paths);

[ '/Users/yuzhoujun/Desktop/repl/node<sub>modules</sub>',
  '/Users/yuzhoujun/Desktop/node<sub>modules</sub>',
  '/Users/yuzhoujun/node<sub>modules</sub>',
  '/Users/node<sub>modules</sub>',
  '*node<sub>modules</sub>',
  '/Users/yuzhoujun*.node<sub>modules</sub>',
  '*Users/yuzhoujun*.node<sub>libraries</sub>',
  '/usr/local/lib/node' ]

2.文件定位
Nodejs按照.js .json .node次序依次补足扩展名。

  3.目录分析和包
  require通过上一步分析文件扩展名后没有找到对应的文件，却得到一个同名的目录，此时Nodejs将此目录当作一个包来处理
（npm下载的包就是如此）。Nodejs在当前目录找package.json，去除卖弄属性指定的文件。如果main属性缺少扩展名，则按
照步骤2的扩展名顺序进行分析。


<a id="orgae416bb"></a>

## 文件模块编译

在Nodejs中每个模块都是一个对象，定义如下：

    function Module (id, parent) {
       this.id = id;
       this.parent = parent;
       this.exports = {};
       if (parent && parent.children) {
          parent.children.push(this);
       }
    
       this.file = null;
       this.loaded = fails;
       this.children = [];
    }


<a id="org9effa9c"></a>

### javascript模块编译

Nodejs对JavaScript文件进行了头尾封装

    (function (exports, require, module, __filename, __dirname) {
       ...
       ...
    });

模块里的变量在funciton scope里的，不会污染模块外的代码。注意exports和module.exports的区别。


<a id="org2f6ef5a"></a>

### C/C++ 模块的编译

   Nodejs调用process.dlopen()方法加载编译后的object文件，dlopen是libuv中的方法，在各平台下有各自的实现，
C/C++模块的优势是效率高，缺点是开发门槛高。


<a id="org3acd77e"></a>

### JSON文件编译

JSON文件编译最简单，直接调用Javascript库函数JSON.parse()，将生成的js对象传给module.exports对象。

    Module._extension['.json'] = function (module, filename) {
        var content = NativeModule.require('fs').readFileSync(filename, 'utf8');
        try {
            module.exports  = JSON.parse(stripBOM(content));
        } catch (err) {
            err.message = filename + ': ' + err.message;
            throw err;
        }
    };

Module.<sub>extensions会赋值给require.extensions可以看以下require.extensions</sub>

    console.log(require.extensions);


<a id="orgdb1956b"></a>

## 核心模块的编译


<a id="org8311ce2"></a>

### javascript核型模块编译

    先转存为C/C++代码，用了V8的js2c.py工具，将所有的内置的JavaScript代码专程C++数组，javascript以
字符串形式存在node命名空间中，这样Node启动时将js代码直接加载进内存中，无需读磁盘。
    编译javascript核型模块，原理同javascript模块编译。编译成功后缓存到NativeModule.<sub>cache对象上</sub>，文件模块则
缓存在Module.<sub>cache对象上</sub>。

