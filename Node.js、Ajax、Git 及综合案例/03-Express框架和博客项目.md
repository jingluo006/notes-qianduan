# 一、 Express框架简介及初体验(P201)

## 1.1 Express框架是什么 

Express是一个基于<font color=red>Node平台</font>的<font color=red>web应用开发框架</font>，它提供了一系列的强大特性，帮助你<font color=red>创建各种Web应用</font>。我们可以使用 <font color=red>npm install express</font> 命令进行下载。



## 1.2 Express框架特性 

* 提供了方便<font color=red>简洁的路由定义</font>方式
* 对获取HTTP<font color=red>请求参数</font>进行了<font color=red>简化</font>处理
* 对<font color=red>模板引擎支持程度高</font>，方便渲染动态HTML页面
* 提供了<font color=red>中间件</font>机制有效<font color=red>控制HTTP请求</font>
* 拥有大量第三方中间件对功能进行扩展



## 1.3  原生Node.js与Express框架<font color=red>对比之路由</font>

``` javascript
//原生
app.on('request', (req, res) => {
     // 获取客户端的请求路径
     let { pathname } = url.parse(req.url);
     // 对请求路径进行判断 不同的路径地址响应不同的内容
     if (pathname == '/' || pathname == 'index') {
        res.end('欢迎来到首页');
     } else if (pathname == '/list') {
        res.end('欢迎来到列表页页');
     } else if (pathname == '/about') {
        res.end('欢迎来到关于我们页面')
     } else {
        res.end('抱歉, 您访问的页面出游了');
     }
 });

//express
// 当客户端以get方式访问/时
 app.get('/', (req, res) => {
     // 对客户端做出响应
     res.send('Hello Express');
 });

 // 当客户端以post方式访问/add路由时
 app.post('/add', (req, res) => {
    res.send('使用post方式请求了/add路由');
 });
```



## 1.4  原生Node.js与Express框架<font color=red>对比</font>之<font color=red>获取请求参数</font>

``` javascript
//原生
app.on('request', (req, res) => {
    // 获取GET参数
    let {query} = url.parse(req.url, true);
    // 获取POST参数
    let postData = '';
    req.on('data', (chunk) => {
        postData += chunk;
    });
    req.on('end', () => {
        console.log(querystring.parse(postData)
    })); 
 });

//express
 app.get('/', (req, res) => {
    // 获取GET参数
    console.log(req.query);
 });

 app.post('/', (req, res) => {
    // 获取POST参数
    console.log(req.body);
 }) 
```



## 1.5 Express初体验（send 方法）

使用Express框架创建web服务器及其简单，<font color=red>调用express模块返回的函数即可。（这个返回值express是一个方法）</font>

``` javascript
// 引入Express框架(先 npm install express)  
 const express = require('express');
 // 使用框架创建web服务器
 const app = express();
 // 当客户端以get方式访问/路由时
 app.get('/', (req, res) => {
    // 对客户端做出响应 send方法会根据内容的类型自动设置请求头
    res.send('Hello Express'); // <h2>Hello Express</h2> {say: 'hello'}
 });
 // 程序监听3000端口
 app.listen(3000);
```

**注：**

<font color=red>send方法的好处</font>：

								1. send方法会内部会检测响应内容的类型
								2. 会自动设置http状态码
								3. 会帮我们自动设置响应的内容类型及编码



# 二、 Express中间件 

## 2.1 什么是中间件

* 中间件就是**一堆方法**，可以**接收**客户端发来的**请求**、可以**对请求做出响应**，也可以**将请求**继续**交给下一个中间件继续处理**。（中间件就相当于一条水流中的一道道**阀门**） 

![15](img/15.PNG) 



* 中间件主要由两部分构成，<font color=red>中间件方法</font>以及<font color=red>请求处理函数。</font>
  中间件方法由Express提供，负责拦截请求，请求处理函数由开发人员提供，负责处理请求。

``` javascript
app.get('请求路径', '处理函数')   // 接收并处理get请求
 app.post('请求路径', '处理函数')  // 接收并处理post请求
```



* 可以针对**同一个请求设置多个中间件**，对同一个请求进行多次处理。
  默认情况下，请求**从上到下依次匹配中间件**，**一旦匹配成功，终止匹配。**
  可以调用**next方法**将请求的**控制权交给下一个中间件**，直到遇到结束请求的中间件。

``` javascript
app.get('/request', (req, res, next) => {
     req.name = "张三";
     next();
 });
 app.get('/request', (req, res) => {
     res.send(req.name);
 });
```



## 2.2  app.use中间件(匹配所有请求)

app.use <font color=red>匹配所有的请求方式</font>，可以**直接传入请求处理函数**，代表**接收所有的请求**。

``` javascript
app.use((req, res, next) => {
     console.log(req.url);
     next();
 });
```

app.use 第一个参数**也可以传入请求地址**，代表不论什么请求方式，只要是这个请求地址就接收这个请求。

``` javascript
app.use('/admin', (req, res, next) => {
     console.log(req.url);
     next();
 });
```



## 2.3 中间件应用

1. <font color=red>路由保护</font>，客户端在访问需要登录的页面时，可以先使用中间件判断用户登录状态，用户如果未登录，则拦截请求，直接响应，禁止用户进入需要登录的页面。

2. <font color=red>网站维护公告</font>，在所有路由的最上面定义接收所有请求的中间件，直接为客户端做出响应，网站正在维护中。

3. <font color=red>自定义404页面</font> （这个路由放最后面，说明前面的中间件都已经走过了，没找到相应地址）

   ​	(res.status(404).send('当前访问页面不存在'))    //可以链式调用status方法，修改状态码然后反映提示信息



## 2.4 错误处理中间件

在程序执行的过程中，不可避免的会出现一些无法预料的错误，比如文件读取失败，数据库连接失败。
<font color=red>错误处理中间件是一个集中处理错误的地方。</font>(当程序出错时，如果是同步API就会自动调用这个中间件，如果是异步API则采用下面的方法)

eg： throw new Error('程序发生错误') 就是同步任务

``` javascript
 app.use((err, req, res, next) => {
     res.status(500).send('服务器发生未知错误');
 });
//err.message 就是错误信息
```

当程序出现错误时，调用next()方法，并且将错误信息通过参数的形式传递给next()方法，即可触发错误处理中间件。

``` javascript
app.get("/", (req, res, next) => {
     fs.readFile("/file-does-not-exist", (err, data) => {
         if (err) {
            next(err);
         }
     });
});
```



## 2.5 捕获错误

在node.js中，异步API的错误信息都是通过回调函数获取的，支持Promise对象的异步API发生错误可以通过catch方法捕获。
异步函数执行如果发生错误要如何捕获错误呢？



<font color=red>try catch 可以捕获异步函数以及其他同步代码在执行过程中发生的错误</font>，但是**不能其他类型的API发生的错误**(像异步API，回调函数，promise对象等就不行)。

``` javascript
app.get("/", async (req, res, next) => {
     try {
         await User.find({name: '张三'})  
     }catch(ex) {
         next(ex);   //如果try 里面的代码执行没问题，则不会执行catch里的next（ex），否则执行，去往错                        误处理中间件
     }
 });
```



# 三、Express框架请求处理（P207）

## 3.1 构建模块化路由

1. 基础代码

``` javascript
const express = require('express') 
 // 创建路由对象
 const home = express.Router();
 // 将路由和请求路径进行匹配
 app.use('/home', home);
  // 在home路由下继续创建二级路由
 home.get('/index', () => {
          //  /home/index 通过这个网址来访问
         res.send('欢迎来到博客展示页面');
 });
```

2. **模块化构建**

   ``` javascript
   // home.js
    const home = express.Router(); 
    home.get('/index', () => {
        res.send('欢迎来到博客展示页面');
    });
    module.exports = home;
   
   // admin.js
    const admin = express.Router();
    admin.get('/index', () => {
        res.send('欢迎来到博客管理页面');
    });
    module.exports = admin;
   
   // app.js
    const home = require('./route/home.js');
    const admin = require('./route/admin.js');
   //匹配路由（是这个app.js 要用到这些路由，所以要在这里匹配）
    app.use('/home', home);
    app.use('/admin', admin);
   ```

   

## 3.2 GET参数的获取

Express框架中使用<font color=red>req.query即可获取GET参数</font>，框架内部会<font color=red>将GET参数转换为对象并返回。</font>

``` javascript
// 接收地址栏中问号后面的参数
 // 例如: http://localhost:3000/?name=zhangsan&age=30
 app.get('/', (req, res) => {
    console.log(req.query); // {"name": "zhangsan", "age": "30"}
 });
```



## 3.3 POST参数的获取

Express中接收post请求参数需要借助第三方包 body-parser。

``` javascript
// 引入body-parser模块（返回了一个模块对象）（npm install body-parser）
 const bodyParser = require('body-parser');
 // 配置body-parser模块（拦截所以中间件然后用bodyParser下的urlencoded方法对请求进行处理，方法内部会检测当前请求是否包含了请求参数，如果有，则会将参数转化为对象格式，并为req添加一个属性，名字为body，然后将参数的值作为对象格式赋值给body属性。最后在方法内部调用了next方法将控制权交给下一个中间件）
 
app.use(bodyParser.urlencoded({ extended: false }));  //extended为false 是会用querystring                                    来解析接收的参数，如果为true，则用qs来解析，false就足够我们用了
 // 接收请求
 app.post('/add', (req, res) => {
    // 接收请求参数
    console.log(req.body);
 }) 
```

注：上面bodyParser.urlencoded({extended:false}) 他的返回值是一个函数，所以app.use的括号里才可以放他



## 3.4 Express路由参数

``` javascript
app.get('/find/:id', (req, res) => {             //:id其实是一个占位符，表示当前路由要传递一                                                              个id作为参数
     console.log(req.params); // {id: 123}     //req.params是一个对象，里面放着这些参数
 });
```

``` javascript
localhost:3000/find/123
```

``` javascript
//如果要传递多个参数
'/find/:id/:name/:age'      //输入网址的时候，所以参数都得输入，一个不能少
```



## 3.5 静态资源的处理

通过Express内置的<font color=red>express.static</font>可以方便地托管静态文件，例如img、CSS、JavaScript 文件等。

``` javascript
app.use(express.static('public'));    //参数就是静态资源文件，这个路径最好写绝对路径
```

``` javascript
//我们可以为静态资源指定一个虚拟路径
app.use('/static',express.static('public')); 
//访问时：
http://localhost:3000/static/css/style.css 即可
```

判断客户端发来的请求是否为静态资源请求，如果是，直接将静态资源响应给客户端，然后终止这一次请求，如果不是，则自动调用next（）将控制权交给下一个中间件

现在，public 目录下面的文件就可以访问了。

eg: 

* http://localhost:3000/images/kitten.jpg
* http://localhost:3000/css/style.css

* http://localhost:3000/images/bg.png
* http://localhost:3000/js/app.js    等等，只要是public目录下有的就行



# 四、express-art-template模板引擎(P214)

## 4.1 模板引擎

* 为了使art-template模板引擎能够更好的和Express框架配合，模板引擎官方在原art-template模板引擎的基础上封装了express-art-template。
* 使用<font color=red>npm install art-template express-art-template</font>命令进行安装。(同时下载这两个第三方模块)

``` javascript
 // 当渲染后缀为art的模板时 使用express-art-template（告诉express框架，使用什么模板引擎渲染什么后缀的模板文件）
 app.engine('art', require('express-art-template'));       //engine就是引擎的意思
  // 设置模板存放目录
 app.set('views', path.join(__dirname, 'views'));   //第一个参数是定死的
  // 渲染模板时不写后缀 默认拼接art后缀
 app.set('view engine', 'art');
 app.get('/', (req, res) => {
     // 渲染模板
     res.render('index', {});       //render是res下的一个方法，第一个参数是要拼接的模板路径，第二                                      个参数就是要传递的数据   
 }); 
```

res.render方法做了四件事：

1. 拼接模板路径
2. 拼接模板后缀
3. 哪一个模板和哪一个数据进行拼接
4. 将拼接结果响应给客户端



## 4.2 app.locals 对象

将变量设置到app.locals对象下面，这个<font color=red>数据在所有的模板中都可以获取到。</font>

``` javascript
app.locals.users = [{
     name: '张三',
     age: 20
 },{
     name: '李四',
     age: 20
}]
```



# 五、 博客项目（p216）

## 1. 项目环境搭建

### 1.2 案例初始化 

1. 建立项目所需文件夹
   * public 静态资源
   * model 数据库操作
   * route 路由
   * views 模板

2. 初始化项目描述文件（package.json)
   * npm init -y

3. 下载项目所需第三方模块
   * npm install express mongoose art-template express-art-template

4. 创建网站服务器

5. 构建模块化路由

6. 构建博客管理页面模板（P219）

   * <font color=red>这个模板引擎里面的外链资源，像css，JavaScript这些的路径都是相对路径，是相对于浏览器当中的请求路径的（因为这些css，js是由浏览器来解析的），要改成绝对路径，<font color=blue>/ </font>代表绝对路径</font>

   eg：原来login.art里外链的css是 href= "css/base.css", 要改成绝对路径， "/admin/css/base.css", 这里的admin是静态资源public目录下的文件，base.css放在这里
   
   * 子模板，把公共部分抽离出去，用{{include '../common/header.art'}} 引入，这里的相对路径是相对当前文件的，因为他是由模板引擎来解析的，而不是浏览器
   * 继承HTML骨架：{{extend '../common/layout.art'}},  然后再用{{block ...}} {{/block}}填坑



## 2.  项目功能实现

（在Schema 中有个unique：true 表示不能存在重复）

### 2.1  登录

1. 创建用户集合，初始化用户
   1. 连接数据库
   2. 创建用户集合
   3. 初始化用户

2. 为登录表单项设置请求地址、请求方式以及表单项name属性
3.  当用户点击登录按钮时，客户端验证用户是否填写了登录表单
4.  如果其中一项没有输入，阻止表单提交
5. 服务器端接收请求参数，验证用户是否填写了登录表单
6.  如果其中一项没有输入，为客户端做出响应，阻止程序向下执行
7.  根据邮箱地址查询用户信息
8. 如果用户不存在，为客户端做出响应，阻止程序向下执行
9.  如果用户存在，将用户名和密码进行比对
10. 比对成功，用户登录成功
11.  比对失败，用户登录失败
12. 保存登录状态
13.  密码加密处理 



**注：**

1. 要阻止表单的默认提交，直接return: false 即可。（表单的提交事件为submit）（给form表单注册这个事件，而不是提交按钮）
2. **获取表单中用户输入的信息**，jQuery下的**serializeArray() 方法**，它返回一个数组，数组里面存着多个对象，对象个数取决于表单控件的个数。对象里两个属性 [{ name: '控件的name',  value: '用户输入的信息'}]，用$(this).serializeArray() 调用这个方法（方法前面是要获取信息的表单）（可以封装一个函数，让他完全变成对象的形式）
3. trim() 方法去除字符串两边空格。
4. 在定时器中可以加 **location.href = "路径"** ，就可以隔几秒跳转页面
5. express 下的**从定向：res.redirect('/admin/user')**   （参数为从定向去到的页面）
6. 用户拦截：没有登入是不能看到管理页面的，用app.use 去拦截，判断session 下是否有登录的用户名，没有就从定向到登入页面，有就用next（）放行



### 2.2  新增用户（P236）

1. 为用户列表页面的新增用户按钮添加链接

2. 添加一个连接对应的路由，在路由处理函数中渲染新增用户模板

3. 为新增用户表单指定请求地址、请求方式、为表单项添加name属性

4. 增加实现添加用户的功能路由

5. 接收到客户端传递过来的请求参数

6. 对请求参数的格式进行验证(参考3.3，用Joi)

7.  验证当前要注册的邮箱地址是否已经注册过

8. 对密码进行加密处理

9. 将用户信息添加到数据库中

10. 重定向页面到用户列表页面

    

<font color=red>**注**</font>： 代码优化（p240）

1. 将验证代码放到专门数据处理的文件中
2. 错误处理放到一块（错误处理中间件）（将对象转化为字符串的方法：JSON.stringify()， JSON.parse()方法是将字符串转化为对象类型）

3. _id用{{$value.__id}} 输出是带引号， 在$前加一个@原文输出即可
4. 谷歌插件 jsonformatter (node.js第七天素材)



### 2.3 数据分页

当数据库中的数据非常多是，数据需要分批次显示，这时就需要用到数据分页功能。

![image-20211130150254563](img/image-20211130150254563.png) 

分页功能核心要素：

1. 当前页，用户通过点击上一页或者下一页或者页码产生，客户端通过get参数方式传递到服务器端
2. 总页数，根据总页数判断当前页是否为最后一页，根据判断结果做响应操作

总页数：Math.ceil（总数据条数 / 每页显示数据条数）（向上取整）

``` javascript
limit(2) // limit 限制查询数量  传入每页显示的数据数量
skip(1) // skip 跳过多少条数据  传入显示数据的开始位置  这个参数是从0开始的

```

数据开始查询位置=（当前页-1）* 每页显示的数据条数

eg： await User.find({}).limit(pagesize).skip(start)

<font color=red size=4>**注：**</font>

1. 查询数据库中的数据条数： User.countDocuments({ '查询条件'})      //User为集合名
2. P243,244 具体操作



### 2.4 用户信息修改

1.  将要修改的用户ID传递到服务器端
2. 建立用户信息修改功能对应的路由
3. 接收客户端表单传递过来的请求参数 
4. 根据id查询用户信息，并将客户端传递过来的密码和数据库中的密码进行比对（bcrypt 下的 compare方法）
5.  如果比对失败，对客户端做出响应（p247）
6.  如果密码对比成功，将用户信息更新到数据库中（p248）（updateOne方法更新，密码不能更新）



<font color=red size=4>注：</font>

1. 通过get参数在a链接中把id 传过去 （eg：<a href="/admin/user-edit?id={{@$value.id}}"）
2. option 下有个selected属性， 有他就是被选中  （P245）



### 2.5 用户信息删除(P249)

1. 在确认删除框中添加隐藏域( type = "hidden" )用以存储要删除用户的ID值
2. 为删除按钮添自定义属性用以存储要删除用户的ID值
3. 为删除按钮添加点击事件，在点击事件处理函数中获取自定义属性中存储的ID值并将ID值存储在表单的隐藏域中
4. 为删除表单添加提交地址以及提交方式
5. 在服务器端建立删除功能路由
6. 接收客户端传递过来的id参数
7. 根据id删除用户



### 2.6 文章管理 (P250)

1. 如果表单涉及文件上传，form表单就必须有个enctype 属性：enctype属性是指定表单数据的编码类型，默认值是"application/x-www-form-urlencoded", 就会解析成"name=zhangsan&age=18" 这样的格式，如果要上传文件就要改成"multipart/form-data" 来将表单数据编码成二进制类型。
2. 解析二级制传过来的数据（参考3.4）



### 2.7 开发环境与生产环境(P261)

**<font size=4>什么是开发环境与生产环境</font>**

环境，就是指项目运行的地方，当项目处于开发阶段，项目运行在开发人员的电脑上，项目所处的环境就是开发环境。当项目开发完成以后，要将项目放到真实的网站服务器电脑中运行，项目所处的环境就是生产环境。



**<font size=4>为什么要区分开发环境与生产环境</font>**

因为在不同的环境中，项目的配置是不一样的，需要在项目代码中判断当前项目运行的环境，根据不同的环境应用不同的项目配置。



**<font size=4>如何区分开发环境与生产环境</font>**

通过电脑操作系统中的系统环境变量区分当前是开发环境还是生产环境。

![19](img/19.PNG) 



``` javascript
if (process.env.NODE_ENV == 'development') {
     // 开发环境
    //可用morgan打印请求信息，参考下面
 } else {
     // 生产环境
 }
//process是global全局对象下的的一个属性对象，env就是environment，process.env是获取系统环境变量，返回值是对象。
```



**<font size=4>第三方模块：morgan</font>**

先下载引入，然后因为他是express下的一个中间件

app.use(morgan('dev'))    //这个参数是固定的，这就代码目的是在开发环境中，将客户端向服务器端发送的请求信息打印到控制台中



### 2.8 第三方模块config(P263)

作用：允许开发人员将不同运行环境下的应用配置信息抽离到单独的文件中，模块内部自动判断当前应用的运行环境，并读取对应的配置信息，极大提供应用配置信息的维护成本，避免了当运行环境重复的多次切换时，手动到项目代码中修改配置信息.

<font size=4>**使用步骤**</font>

1. 使用npm install config命令下载模块

2. 在项目的根目录下新建config文件夹

3. 在config文件夹下面新建default.json、development.json、production.json文件

4. 在项目中通过require方法，将模块进行导入

5. 使用模块内部提供的get方法获取配置信息(config.get() ，config会根据环境自动找对应的json文件)

   （如果在development.json或production.json找不到对应属性就回去default.json中找）

   

eg: 

``` javascript
//development.json文件下
{
    "db": {
        "user": "itcast",
    	"pwd":"itcast"
    }
}
//其他文件要用时
config.get("db.user")
```



<font size=4>**将敏感配置信息存储在环境变量中**</font>

1. 在config文件夹中建立custom-environment-variables.json文件

2. 配置项属性的值填写系统环境变量的名字

3. 项目运行时config模块查找系统环境变量，并读取其值作为当前配置项属于的值

   ![20](img/20.PNG) 

   

### 2.9 前台页面（P266）

**注：**

1. 多集合联合查询，用populate方法（p268）

2. 匹配HTML标签并替换成空用js内置的replace方法，第一个参数为要匹配的HTML标签，我们用正则表达式匹配

   ``` javascript
   /<[^>]+>/g  来匹配所有HTML标签，第二个参数为
   ```

3. 截取字符串，**substr(0,150)**方法，第一个参数为开始截取的位置，第二个参数为截取的数量（p269）



### 2.10 文章评论（P272）

1. 创建评论集合

2. 判断用户是否登录，如果用户登录，再允许用户提交评论表单

3. 在服务器端创建文章评论功能对应的路由

4. 在路由请求处理函数中接收客户端传递过来的评论信息

5. 将评论信息存储在评论集合中

6. 将页面重定向回文章详情页面

7. 在文章详情页面路由中获取文章评论信息并展示在页面中

   

### 2.11 用户退出功能（P277）

基本思路：向服务器端发送一个请求，服务器端接收到请求后删除session和cookie，重定向到登录页面即可。

``` javascript
//删除session,(调用destroy方法即可)
req.session.destroy(function() {                  //session删除成功后调用这个回调函数
    //删除cookie(是res下的一个方法clearCookie)
    res.clearCookie('connect.sid')   //括号里的名字是express自定义的名字，在浏览器application中                                        可以查到，要删哪个cookie就写对应名字即可
    //重定向到要去的页面即可
    res.redirect('')
})
```

可以设置cookie的过期时间（p277）



## 3、项目包含的知识点

### 3.1  密码加密 bcrypt

**哈希(算法)加密**是**单程**加密方式：1234 => abcd （即**只能加密不能解密**）

在加密的密码中加入**随机字符串**可以增加密码被破解的难度。

``` javascript
// 导入bcrypt模块
const bcrypt = require('bcrypt');
// 生成随机字符串 gen => generate 生成 salt 盐
let salt = await bcrypt.genSalt(10);    //参数值越大，生成随机字符串的复杂度越高，反之越低
// 使用随机字符串对密码进行加密
let pass = await bcrypt.hash('明文密码', salt);
```

``` javascript
// 密码比对
let isEqual = await bcrypt.compare('明文密码', '加密密码');
//这个方法返回的是布尔值，比对成功为true， 否则为false
```





<font color=red>bcrypt依赖的其他环境：</font>

1.  python 2.x  （安装好后要在环境变量中添加目录）
2. node-gyp
       npm install -g node-gyp
3. windows-build-tools
        npm install --global --production windows-build-tools   （这个时间有点久）

4. 最后下载 bccrypt 这个第三方模块即可(记住得重新打开命令行窗口在安装)



### 3.2 cookie与session(P232)

<font color=red size=5>cookie</font>：浏览器在电脑硬盘中开辟的一块空间，主要供服务器端存储数据。

* cookie中的数据是以**域名**的形式进行**区分**的。
* cookie中的数据是有**过期时间**的，超过时间数据会被浏览器自动删除。
* cookie中的数据会随着请求被**自动发送**到服务器端。

![16](img/16.PNG) 



<font color=red size=5>session</font>：实际上就是一个对象，存储在服务器端的内存中，在session对象中也可以存储多条数据，每一条数据都有一个**sessionid做为唯一标识**。



![17](img/17.PNG)



在node.js中需要<font color=red>借助express-session实现session功能。</font>（npm install express-session）

``` javascript
const session = require('express-session');
app.use(session({ secret: 'secret key' }));
//session 方法下的这个secret 是指密钥，值可以自定义，这个密钥的值是用来加密cookie信息的，向客户端存储数据时是要加密的，服务器端接收到cookie时需要使用这个密钥进行解密（客户端是不知道这个密钥的，客户端查询到的cookie就是一堆加密的字符串）
```



### 3.3  Joi（P237）

JavaScript对象的**规则描述语言**和**验证器**。

示例代码，具体参考文档

``` javascript
const Joi = require('joi');
//制定验证规则（验证的对象有什么属性，schema就应该有什么属性）
const schema = {
    username: Joi.string().alphanum().min(3).max(30).required().error(new Error(‘错误信息’)),  
    password: Joi.string().regex(/^[a-zA-Z0-9]{3,30}$/),
    access_token: [Joi.string(), Joi.number()],
    birthyear: Joi.number().integer().min(1900).max(2013),
    email: Joi.string().email()，
    role: Joi.string().valid('normal', 'admin')  //valid 方法是限定只能输入括号的部分  
};
//validate 方法返回promise对象， 第一个参数为要验证的对象，第二个为规则
Joi.validate({ username: 'abc', birthyear: 1994 }, schema);

//一般用异步函数方法，用try{ await Joi.validate({ username: 'abc', birthyear: 1994 }, schema); } catch(ex) { console.log(ex.message)} 来获取错误信息
```

错误信息可以通过重定向 把错误信息放地址栏后面  ... ?message= ex.message , 然后用req.query 获取



### 3.4 formidable （P252）

<font color=red>作用：解析表单，支持get请求参数，post请求参数、文件上传。</font>

``` javascript
// 引入formidable模块（先 npm install formidable）
 const formidable = require('formidable');
 // 创建表单解析对象
 const form = new formidable.IncomingForm();
 // 设置文件上传路径(如果只是简单的传参数，不传文件就不要设置路径)
 form.uploadDir = "/my/dir";     //尽量写绝对路径
 // 是否保留表单上传文件的扩展名(默认是false，我们得改为true，不然无法解析文件)
 form.keepExtensions = true;
 // 对表单进行解析
 form.parse(req, (err, fields, files) => {
     // fields 存储普通请求参数
         // files 存储上传的文件信息
 });
```

<font color=red>注:</font>

 这个配置写在路由里面，不用写在app.js主文件中



### 3.5 文件读取 FileReader （P254）

``` javascript
var reader = new FileReader();        //FileReader是js的内置构造函数
 reader.readAsDataURL('文件');   //读取二级制文件，该方法是异步函数方法，不能直接获取返回值，要通过监                                    听事件来获取结果
 reader.onload = function () {      //读取完成就会调用这个事件，reader.result就是下面的编码
     console.log(reader.result); 
 }
```

![18](img/18.PNG) 

注：左边是我们看到的，右边是读取出来的文件编号，给电脑看的，这两者是一致的，可以在img下的src加上这段编码就可以显示图片

用户选择完文件后的事件为<font color=red>onchange</font> 事件



### 3.6 数据分页 mongoose-sex-page (P258)

``` javascript
const pagination = require('mongoose-sex-page');
pagination(集合构造函数).page(1) .size(20) .display(8) .exec();
//想查询哪一个集合的数据就传该集合构造函数
//page为当前页，size为每一页显示的数据条数，display为分页栏显示多少页，exec方法是向数据库发生查询请求
```

返回的结果：

``` javascript
{
    "page": 1,    //当前页
    "size": 2,    //每页显示的数据条数
    "total": 8,   //总共的数据条数
    "records": [
        //查询出来的具体数据
        {
            "_id": "5c3e.....",
            "title": "测试文章"
        }
    ],
    "pages": 4,     //总共的页数
    "display": [1, 2, 3 ,4]     //客户端显示的页码
}
```



### 3.7 mongoDB数据库添加账号（P260）

数据库管理员分**超级管理员和普通管理员**，超级管理员可以操控所以数据库，普通管理员只能操控某一个数据库。

1. 以系统管理员的方式运行powershell

2. 连接数据库 mongo

3. 查看数据库 show dbs  （database缩写）

4. 切换到admin数据库 use admin     （admin是数据库中默认的一个数据库）

5. 创建超级管理员账户 db.createUser()

   createUser()接收一个对象作为参数，{ user: 'root', pwd: 'root', roles: ['root']},  这三个是必须写的，user表示用户，pwd为密码，roles里面是root代表超级管理员。

   

6. 切换到blog数据 use blog

7. 创建普通账号 db.createUser()

   也是接收一个对象，{ user: 'itcast', pwd: 'itcast', roles: ['readWrite']}，readWrite表示可读写，也是普通管理员。（然后exit退出数据库）

8. 卸载mongodb服务

   1. 停止服务 net stop mongodb
   2. mongod --remove

9. 创建mongodb服务
             mongod --logpath="C:\Program Files\MongoDB\Server\4.1\log\mongod.log" --dbpath="C:\Program          Files\MongoDB\Server\4.1\data" --install –-auth

   **注**：--logpath 是数据库有一些日志要输出，这个是指定日志输出到哪个位置（记得把\mongod.log输进去）。--dbpath是指定数据库的存储目录。--auth表示验证，加上这个参数表示不能在没有账号密码的时候对数据库进行操作。

   

10. 启动mongodb服务 net start mongodb

11. 项目中使用账号连接数据库
              mongoose.connect('mongodb://user:pass@localhost:port/database')

    **注：**user(用户名):pass(密码)，port为端口(默认27017，也可以不写)，database则为数据库名字

    

**<font color=red>注</font>**: 

1. exit命令为退出数据库操作
2. 用compass登入时输入：mongodb://username:pwd@localhost/database       (端口默认是27017，不用输入端口)
