# Express入门

## 安装
* ```sudo npm install express -g```

## 创建express程序
* express --css less --sessions
* express --css sass
* express --css stylus

## 启动express程序
* node app

# 设置路由

## 路由目录
    ```js
    app.get('/', routes.index);
    app.get('/about', routes.about);
    app.get('/contact', routes.contact);
    app.post('/contact', routes.contactPostHandler);
    app.get('/users', user.list);
    ```
## 渲染函数
* 第一个参数是模板文件的文件名，对应./views目录下的contact.jade文件。express默认使用jade模板，当然也可以使用underscore模板。
    ```js
    res.render('contact', {
        title: 'Contact Us',
        description: 'Send us a message and we\'ll get back to you'
    });
    ```
## POST, PUT, DELETE
* express是围绕REST模式设计的，因此也可以使用```app.post(), app.put(), app.delete()```


# 渲染视图

## jade模板和underscore模板

* Jade模板是express的一部分，能大量缩减模板中的字符数量
* 启用underscore模板
    ```shell
    npm install uinexpress
    npm install underscore
    ```
    ```js
    // all environments
    app.engine('html', require('uinexpress').__express);
    app.set('view engine', 'html');
    // app.set('view engine', 'jade');
    ```

## 创建视图
* 首页及各个页面
* Layout 模板，可以添加要出现在所有页面上的东西，比如Google Analytics追踪代码，或者网站的页脚。


# 处理表单数据

## 创建POST路由

### 接收

*
    ```js
    app.post('/contact', routes.contactPostHandler);
    exports.contactPostHandler = function(req, res) {
        var templateVars = {
            description: response,
            success: success
        };

        // error output
        if ( !success ) {
            console.log( response );

            templateVars.title = 'Contact Us';
            templateVars.name = req.body.name;
            templateVars.email = req.body.email;
            templateVars.message = req.body.message;

            // render the template
            res.render('contact', templateVars);
        else {
            templateVars.title = 'Message Posted successfully'

            // render the template
            res.render('contact', templateVars);
      }

        }
    };
    ```

### 反馈

* 校验
    ```npm install validator```
    ```html
    <div class="form-item">
        <label for="email">Email:</label>
        <input type="email" name="email" id="email" required <%= typeof email != 'undefined' ? 'value="' + email + '"' : '' %>/>
    </div>
    ```
    ```js
    // validate required fields
    function isFilled(field) {
        return field !== undefined && field !== '';
    }

    // validate email address
    var emailRegex = /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;

    function isValidEmail(email) {
        return emailRegex.test(email);
    }

    // check required fields
    missing = required.filter(function(prop) {
        return !isFilled(req.body[prop]);
    });

    if ( missing.length ) {
        response = 'Please fill out all required fields (' + missing.join(', ') + ')';
    }

    // check email
    else if ( !isValidEmail( req.body.email ) ) {
        response = 'Please enter a valid email address';
    }

    else {
        success = true;
    }
    ```
* 在模板中渲染反馈
    ```html
    <% // error message
    if (typeof success != 'undefined' && !success) { %>
    <p style="color: red"><%=description %></p>
    <% }

    else { %>
    <p><%=description %></p>
    <% } %>

    <% // only output the form if not posted, or unsuccessful
    if (typeof success == 'undefined' || !success) { %>

    // form details

    <% } %>
    ```

* 向模板中传入新的变量，参见[接收]部分

# 发封邮件

## 安装
* ```npm install emailjs```

## 连接服务器
* 代码
    ```js
    var emailjs = require('emailjs');
    var server = emailjs.server.connect({
        user: 'username',
        password: 'password',
        host: 'smtp.gmail.com',
        ssl: true
    });
    ```

##  构建Email消息
* 代码
    ```js
    var emailBody = 'From: ' + req.body.name + ' <' + req.body.email + '>' + "\n";
    emailBody += 'Message: ' + req.body.message + "\n\n";
    emailBody += 'Sent automatically from Node server on ' + Date();
    ```

## 发送邮件
* 代码
    ```js
    server.send({
        from:    'Node Server <no-reply@localhost>',
        to:      'Server Admin <admin@localhost>',
        subject: 'Contact form submission',
        text: emailBody
    }, function(err, message) {
        console.log(err || message);

        // if smtp error
        if ( err ) {
            res.send('Sorry, there was an error sending your message, please try again later');
        }
        // otherwise show success message
        else {

        }
    }
    ```