# Nosql的好处
* 扩展能力。分片。
* 简单性。

# MongoDB入门

## 安装

### https://docs.mongodb.com/manual/installation/
* ubuntu 16.04
    ```shell
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
    echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

## 运行
* ubuntu 16.04
    ```shell
    sudo service mongod start
    cat /var/log/mongodb/mongod.log
    sudo service mongod stop
    sudo service mongod restart
    mongo --host 127.0.0.1:27017
    ```

## MongoDB模块
* MongoDB，原生Node.js驱动，使用JavaScript语言的基础MongoDB支持
    ```npm install mongodb```
* Mongoose，提供对象关系映射(ORM)的对象建模工具
    ```npm install mongoose```

## 创建数据库
* 代码
    ```js
    var mongodb = require('mongodb');
    var dbServer = new mongodb.Server('localhost', 27017, {auto_reconnect: true});
    var db = new mongodb.Db('mydb', dbServer, {w:1});
    // db.authenticate(user, password, function() { /* callback */ });
    ```

# MongoDB中的CRUD

* 创建、读取、更新、删除
* MongoDB是文档型数据库，所以它的记录叫做文档。
* 在其他数据库中，比如MySQL，数据用表格表示。MongoDB用集合，具体的表现形式为简单的JSON对象。


## 创建集合
* 代码
    ```js
    db.open( function(err, conn) {
        // 添加一个集合到数据库中
        db.collection('chatroomMessages', function(err, collection) {
            // 插入一个文档到集合中
            data = {a: 'my item'}
            collection.insert(data, function(err, result) {
                console.log(result);
                db.close();
            });
        });
    });
    ```
* 唯一标识符：_id

## 读取数据
* 代码
    ```js
    db.open( function(err, conn) {
        // 添加一个集合到数据库中
        db.collection('chatroomMessages', function(err, collection) {
            // 选择文档
            collection.find({num: {$gt: 1, $lt: 4}}, {sort:[['_id', 'desc']], limit: 10}).toArray( function(err, results) {
                console.log(result);
                db.close();
            });
        });
    });
    ```


## 更新数据
* 代码
    ```js
    db.open( function(err, conn) {
        // 添加一个集合到数据库中
        db.collection('chatroomMessages', function(err, collection) {
            // 选择文档
            collection.update({num: 2}, {$set: {desc: 'favorite number'}}, {safe: true, upsert:true}, function(err) {
                if (err) {
                    console.log(err);
                } else {
                    console.log('successfully updated');
                }
                db.close({});
            });
        });
    });
    ```
    ```js
    collection.findAndModify({num: 4}, [['_id', 'asc']], {num: 25}, {safe: true}, function (err, result) { /* call back */})
    ```

## 删除数据
* 代码
    ```js
    collection.remove({num:1}, function(err) { /* call back */})
    collection.findAndRemove({num: 0}, [['_id', 'asc']], function(err, result) { /* call back */})
    db.dropCollection('myNewCollection', function(err, result) { /* call back */})
    ```


# Mongoose

## 安装
* 代码
    ```shell
    npm install mongoose
    ```
    ```js
    var mongoose = require('mongoose');
    //连接数据库
    mongoose.connect('localhost', 'mydb');
    //数据库连接
    var db = mongoose.connection;
    //错误回调
    db.on('error', function(msg) {
        console.log('Connection Error: %s', msg);
    });
    // db.on('error', console.error.bind(console, 'Connection error:'));
    //成功回调
    db.once('open', function callback(){
        //成功时
        console.log('Database opened successfully');
    });
    ```


## 创建模型

* 创建 Schema
    ```js
    var fruitSchema = mongoose.Schema({
        name: {type: String, require: true, trim: true, unique: true},
        color: {type: String, require: true},
        quantity: Number,
        ripe: Boolean
    });
    ```

* 定义模型并创建实例
    ```js
    var Fruit = mongoose.model('fruit', fruitSchema);
    var apple = new Fruit({
        name: 'apple',
        color: 'red',
        quantity: 3,
        ripe: true
    });
    ```

* 保存模型
    ```js
    apple.save(function(err, apple){
        if(err) {
            console.log(err);
        } else {
            console.log(apple);
        }
    });
    ```

## 读取数据

* 代码
    ```js
    Fruit.find({name: 'orange', name: /e$/, quantity: {$lt: 4}}, function(err, fruit){
        if(err){
            console.log(err);
        } else {
            console.log('I have ' + fruit.quantity + ' ' + fruit.color + ' ' + fruit.name + (fruit.quantity != 1 ? 's':''));
        }
    });
    ```

# 其他数据库

* Node中也有MySQL
* Redis
