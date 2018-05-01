# Node.js 简介

## 为什么是Node

* 实时
* 能优雅的处理大量IO，归因于它的多并发连接请求的处理机制
* 用一个带有事件循环的非阻塞线程处理请求。多个请求排在队列中，用异步的回调函数按顺序处理。
* 非常擅长处理异步函数，既有setTimeout这样的计时事件，也有onclick这样的界面事件
* V8 JavaScript引擎，在运行代码之前先编译为本地机器码，编译过的代码在运行时进一步优化，动态缓存部分代码，省略昂贵的运行时属性。


## 安装Node


## 入门

* 创建服务器、添加内容、打包、运行脚本、简化脚本、使用REPL(read-eval-print loop)

## 模块

* ```npm update -g npm```
* ```npm install underscore express grunt supervisor serialport -g --unsafe-perm```
* ```npm view express dependencies```
* ```npm search grunt```

* 路径：```/usr/lib/node_modules/```


## Node模式

### 模块和全局变量
* 创建自己的模块：
    ```js
    var myModule = require('/path-to/my-module.js')
    ```
* 前端：定义时没有var关键字的变量就是全局变量
    ```js
    globalVar = true
    var localVar = false
    ```
* 后端：全部定义的所有变量只能在那个模块内访问。因为模块是直接对应到文件的，所以变量也只能用在既定文件中。尽量避免下面的做法。
    ```js
    GLOBAL.gobalVar = true; // 尽量避免这种做法
    ```
* 全局对象，可以在REPL中查看global对象
* 使用导出。在node中通常应该避免定义全局变量，并在需要时手工共享变量作用域。要共享作用域，必须在模块中使用exports显示声明要共享什么。
* 导出整个作用域。```exports.sayHello``` => ```module.exports```，一次性导出整个对象，包括变量和函数
```js
module.exports = function(name, age, gender) {
    this.name = name;
    this.age = age;
    this.gender = gender;
    this.about = function() {
        return this.name + ' is a ' + this.age + ' year old ' + this.gender;
    }
}

var User = require('./user.js')
var user = new User('Jon', 30, 'man');
console.log(user.about());
```

### 异步模式
* 为了避免阻塞执行，大多数Node原生方法默认都是异步的。异步请求对于加快Node服务器的速度和提升程序性能有很大帮助。
* 异步调用。异步方法一有机会就会被调用，也就是说即便在函数b之前调用函数a，也不能保证它先完成。不过Node中的大多数方法也提供了同步的版本。比如：```fs.readFile() => fs.readFileSync()```
* 嵌套回调。降低嵌套层级的方法：把脚本的一部分分割出来，作为单独的回调传入；或者用模块处理执行顺序(https://github.com/caolan/async)
* 流。```fs.createReadStream()```，在文件加载时使用流处理其中的数据。
    ```js
    var http = require('http'),
        fs = require('fs');

    var server = http.createServer(function(request, response) {
      // create the stream
      var stream = fs.createReadStream('my-file.txt');

      // handle any errors
      stream.on('error', function(err) {
        response.statusCode = 500;
        response.end(String(err));
      });

      // pipe the response to the client
      stream.pipe(response);
    });

    server.listen(8000);
    ```

### 事件
* 事件在node中占了很大一部分，EventEmitter是Node中事件的主要组件。只要见到用on()处理的事件，实际上那就是EventEmitter。
    ```js
    var events = require('events');
    var em = new events.EventEmitter();
    em.on('my-event', function(data) {
        // 处理事件
    });
    em.emit('my-event');
    ```

### 子进程
* 使用子进程
    ```js
    // include the child_process module
    var spawn = require('child_process').spawn;

    // spawn a child process for ls
    var ls = spawn('ls');

    // handle standard output
    ls.stdout.on('data', function(data) {
      console.log(data.toString());
    });

    // handle error
    ls.stderr.on('data', function(data) {
      console.log('Error: ' + data);
    });

    // handle exit
    ls.on('exit', function(code) {
      console.log('child process exited with code ' + code);
    });
    ```
* 给子进程传入变量： ```var ls = spawn('ls', ['-a']);```

