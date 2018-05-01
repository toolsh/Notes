# Node.js ���

## Ϊʲô��Node

* ʵʱ
* �����ŵĴ������IO�����������Ķಢ����������Ĵ������
* ��һ�������¼�ѭ���ķ������̴߳������󡣶���������ڶ����У����첽�Ļص�������˳����
* �ǳ��ó������첽����������setTimeout�����ļ�ʱ�¼���Ҳ��onclick�����Ľ����¼�
* V8 JavaScript���棬�����д���֮ǰ�ȱ���Ϊ���ػ����룬������Ĵ���������ʱ��һ���Ż�����̬���沿�ִ��룬ʡ�԰��������ʱ���ԡ�


## ��װNode


## ����

* ������������������ݡ���������нű����򻯽ű���ʹ��REPL(read-eval-print loop)

## ģ��

* ```npm update -g npm```
* ```npm install underscore express grunt supervisor serialport -g --unsafe-perm```
* ```npm view express dependencies```
* ```npm search grunt```

* ·����```/usr/lib/node_modules/```


## Nodeģʽ

### ģ���ȫ�ֱ���
* �����Լ���ģ�飺
    ```js
    var myModule = require('/path-to/my-module.js')
    ```
* ǰ�ˣ�����ʱû��var�ؼ��ֵı�������ȫ�ֱ���
    ```js
    globalVar = true
    var localVar = false
    ```
* ��ˣ�ȫ����������б���ֻ�����Ǹ�ģ���ڷ��ʡ���Ϊģ����ֱ�Ӷ�Ӧ���ļ��ģ����Ա���Ҳֻ�����ڼȶ��ļ��С��������������������
    ```js
    GLOBAL.gobalVar = true; // ����������������
    ```
* ȫ�ֶ��󣬿�����REPL�в鿴global����
* ʹ�õ�������node��ͨ��Ӧ�ñ��ⶨ��ȫ�ֱ�����������Ҫʱ�ֹ��������������Ҫ���������򣬱�����ģ����ʹ��exports��ʾ����Ҫ����ʲô��
* ��������������```exports.sayHello``` => ```module.exports```��һ���Ե����������󣬰��������ͺ���
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

### �첽ģʽ
* Ϊ�˱�������ִ�У������Nodeԭ������Ĭ�϶����첽�ġ��첽������ڼӿ�Node���������ٶȺ��������������кܴ������
* �첽���á��첽����һ�л���ͻᱻ���ã�Ҳ����˵�����ں���b֮ǰ���ú���a��Ҳ���ܱ�֤������ɡ�����Node�еĴ��������Ҳ�ṩ��ͬ���İ汾�����磺```fs.readFile() => fs.readFileSync()```
* Ƕ�׻ص�������Ƕ�ײ㼶�ķ������ѽű���һ���ַָ��������Ϊ�����Ļص����룻������ģ�鴦��ִ��˳��(https://github.com/caolan/async)
* ����```fs.createReadStream()```�����ļ�����ʱʹ�����������е����ݡ�
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

### �¼�
* �¼���node��ռ�˺ܴ�һ���֣�EventEmitter��Node���¼�����Ҫ�����ֻҪ������on()������¼���ʵ�����Ǿ���EventEmitter��
    ```js
    var events = require('events');
    var em = new events.EventEmitter();
    em.on('my-event', function(data) {
        // �����¼�
    });
    em.emit('my-event');
    ```

### �ӽ���
* ʹ���ӽ���
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
* ���ӽ��̴�������� ```var ls = spawn('ls', ['-a']);```

