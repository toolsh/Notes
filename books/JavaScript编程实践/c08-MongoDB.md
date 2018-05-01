# Nosql�ĺô�
* ��չ��������Ƭ��
* ���ԡ�

# MongoDB����

## ��װ

### https://docs.mongodb.com/manual/installation/
* ubuntu 16.04
    ```shell
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
    echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list
    sudo apt-get update
    sudo apt-get install -y mongodb-org

## ����
* ubuntu 16.04
    ```shell
    sudo service mongod start
    cat /var/log/mongodb/mongod.log
    sudo service mongod stop
    sudo service mongod restart
    mongo --host 127.0.0.1:27017
    ```

## MongoDBģ��
* MongoDB��ԭ��Node.js������ʹ��JavaScript���ԵĻ���MongoDB֧��
    ```npm install mongodb```
* Mongoose���ṩ�����ϵӳ��(ORM)�Ķ���ģ����
    ```npm install mongoose```

## �������ݿ�
* ����
    ```js
    var mongodb = require('mongodb');
    var dbServer = new mongodb.Server('localhost', 27017, {auto_reconnect: true});
    var db = new mongodb.Db('mydb', dbServer, {w:1});
    // db.authenticate(user, password, function() { /* callback */ });
    ```

# MongoDB�е�CRUD

* ��������ȡ�����¡�ɾ��
* MongoDB���ĵ������ݿ⣬�������ļ�¼�����ĵ���
* ���������ݿ��У�����MySQL�������ñ���ʾ��MongoDB�ü��ϣ�����ı�����ʽΪ�򵥵�JSON����


## ��������
* ����
    ```js
    db.open( function(err, conn) {
        // ���һ�����ϵ����ݿ���
        db.collection('chatroomMessages', function(err, collection) {
            // ����һ���ĵ���������
            data = {a: 'my item'}
            collection.insert(data, function(err, result) {
                console.log(result);
                db.close();
            });
        });
    });
    ```
* Ψһ��ʶ����_id

## ��ȡ����
* ����
    ```js
    db.open( function(err, conn) {
        // ���һ�����ϵ����ݿ���
        db.collection('chatroomMessages', function(err, collection) {
            // ѡ���ĵ�
            collection.find({num: {$gt: 1, $lt: 4}}, {sort:[['_id', 'desc']], limit: 10}).toArray( function(err, results) {
                console.log(result);
                db.close();
            });
        });
    });
    ```


## ��������
* ����
    ```js
    db.open( function(err, conn) {
        // ���һ�����ϵ����ݿ���
        db.collection('chatroomMessages', function(err, collection) {
            // ѡ���ĵ�
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

## ɾ������
* ����
    ```js
    collection.remove({num:1}, function(err) { /* call back */})
    collection.findAndRemove({num: 0}, [['_id', 'asc']], function(err, result) { /* call back */})
    db.dropCollection('myNewCollection', function(err, result) { /* call back */})
    ```


# Mongoose

## ��װ
* ����
    ```shell
    npm install mongoose
    ```
    ```js
    var mongoose = require('mongoose');
    //�������ݿ�
    mongoose.connect('localhost', 'mydb');
    //���ݿ�����
    var db = mongoose.connection;
    //����ص�
    db.on('error', function(msg) {
        console.log('Connection Error: %s', msg);
    });
    // db.on('error', console.error.bind(console, 'Connection error:'));
    //�ɹ��ص�
    db.once('open', function callback(){
        //�ɹ�ʱ
        console.log('Database opened successfully');
    });
    ```


## ����ģ��

* ���� Schema
    ```js
    var fruitSchema = mongoose.Schema({
        name: {type: String, require: true, trim: true, unique: true},
        color: {type: String, require: true},
        quantity: Number,
        ripe: Boolean
    });
    ```

* ����ģ�Ͳ�����ʵ��
    ```js
    var Fruit = mongoose.model('fruit', fruitSchema);
    var apple = new Fruit({
        name: 'apple',
        color: 'red',
        quantity: 3,
        ripe: true
    });
    ```

* ����ģ��
    ```js
    apple.save(function(err, apple){
        if(err) {
            console.log(err);
        } else {
            console.log(apple);
        }
    });
    ```

## ��ȡ����

* ����
    ```js
    Fruit.find({name: 'orange', name: /e$/, quantity: {$lt: 4}}, function(err, fruit){
        if(err){
            console.log(err);
        } else {
            console.log('I have ' + fruit.quantity + ' ' + fruit.color + ' ' + fruit.name + (fruit.quantity != 1 ? 's':''));
        }
    });
    ```

# �������ݿ�

* Node��Ҳ��MySQL
* Redis
