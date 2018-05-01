# Express����

## ��װ
* ```sudo npm install express -g```

## ����express����
* express --css less --sessions
* express --css sass
* express --css stylus

## ����express����
* node app

# ����·��

## ·��Ŀ¼
    ```js
    app.get('/', routes.index);
    app.get('/about', routes.about);
    app.get('/contact', routes.contact);
    app.post('/contact', routes.contactPostHandler);
    app.get('/users', user.list);
    ```
## ��Ⱦ����
* ��һ��������ģ���ļ����ļ�������Ӧ./viewsĿ¼�µ�contact.jade�ļ���expressĬ��ʹ��jadeģ�壬��ȻҲ����ʹ��underscoreģ�塣
    ```js
    res.render('contact', {
        title: 'Contact Us',
        description: 'Send us a message and we\'ll get back to you'
    });
    ```
## POST, PUT, DELETE
* express��Χ��RESTģʽ��Ƶģ����Ҳ����ʹ��```app.post(), app.put(), app.delete()```


# ��Ⱦ��ͼ

## jadeģ���underscoreģ��

* Jadeģ����express��һ���֣��ܴ�������ģ���е��ַ�����
* ����underscoreģ��
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

## ������ͼ
* ��ҳ������ҳ��
* Layout ģ�壬�������Ҫ����������ҳ���ϵĶ���������Google Analytics׷�ٴ��룬������վ��ҳ�š�


# ���������

## ����POST·��

### ����

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

### ����

* У��
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
* ��ģ������Ⱦ����
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

* ��ģ���д����µı������μ�[����]����

# �����ʼ�

## ��װ
* ```npm install emailjs```

## ���ӷ�����
* ����
    ```js
    var emailjs = require('emailjs');
    var server = emailjs.server.connect({
        user: 'username',
        password: 'password',
        host: 'smtp.gmail.com',
        ssl: true
    });
    ```

##  ����Email��Ϣ
* ����
    ```js
    var emailBody = 'From: ' + req.body.name + ' <' + req.body.email + '>' + "\n";
    emailBody += 'Message: ' + req.body.message + "\n\n";
    emailBody += 'Sent automatically from Node server on ' + Date();
    ```

## �����ʼ�
* ����
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