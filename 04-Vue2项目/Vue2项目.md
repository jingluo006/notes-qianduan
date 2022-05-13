# Vue2实战项目

## 一、项目初始化

1、<font color='red'>码云</font>先提前创建好<font color='red'>ssh 公钥</font>，按码云提供的步骤操作即可



2、<font color='red'>数据库的导入</font>（MySQL）

```json
mysql数据导入的时候，我用老师给的phpstudy版本导入是没有任何提示和弹窗。db 的文件夹是空的，接口测试也不行，用了一天才找到解决办法：
到官网下载了最新版本phpstudy_por。安装好后启动好mysql。然后去导航栏的数据库，点+号的创建数据库，输入数据库名称mydb以及用户名和密码，点确认会创建新的一栏任务，在操作里点导入，等待一会儿等任务里提示完成，这里直接看这个导入的界面就行，跟老师弹窗提示是不一样的。
然后最重要的一步来了，去老师给的素材里\vue_api_server\config，把default.json里的"user": "root", "password": "root",改成你在phpstudy_por创建数据库的用户名和密码，点保存后去运行一下老师说的node ./app.js。如果不改密码和用户名，postman是接口测试是失败的
```

phpStudy和Postman下载最新版本即可。



## 二、登录/退出

1、<font color='red'>不涉及跨域</font>是用<font color='red'>cookie 和 session </font>验证登录，涉及<font color='red'>跨域</font>时用<font color='red'>token  </font>（token原理视频p10）



2、每实现一个功能前要先创建一个分支

```js
git status                  //创建分支前先查看当前是否干净，不干净则先提交之前的内容。

git checkout -b login       //创建并选择该分支
git branch                  //查看分支
```



3、使用<font color='red'>Element-UI 时的报错</font>

![](E:\前端学习笔记\Vue2项目\img\1.PNG)

这是因为Input<font color='red'>组件没有按需导入</font>，虽然我们用的是Form 组件里的，但不能只导入Form



4、登录时要<font color='red'>先检验验证（预校验）是否通过</font>（Element-UI在Form 中提供的validate方法）



5、通过判断<font color='red'>状态码是否为200</font>，决定<font color='red'>是否登录</font>，只有状态码<font color='red'>是200才能</font>登录并<font color='red'>保存token</font>



6、<font color='red'>token要保存的sessionStorage 中</font>，不能保存到localStorage 中，因为<font color='red'>localStorage 是持久化的</font>存储机制，<font color='red'>sessionStorage 是绘画期间的存储机制</font>，token只应在这个网站打开期间生效。



7、别忘了设置路由前置导航守卫



8、<font color='red'>表单如果要用keyup.enter 提交</font>，得**写到最上面的el-form 中**，不能写到下面的el-button

```vue
@keyup.enter.native="login"
//.native是原生事件
```



9、**最后提交代码**(<font color='red'>一定要先合并再提交</font>)

```js
将代码提交到码云
新建一个项目终端,输入命令‘git status’查看修改过的与新增的文件内容
将所有文件添加到暂存区：git add .
将所有代码提交到本地仓库：git commit -m "添加登录功能以及/home的基本结构"
查看分支： git branch  发现所有代码都被提交到了login分支
将login分支代码合并到master主分支，先切换到master：git checkout master
在master分支进行代码合并：git merge login
将本地的master推送到远端的码云：git push

推送本地的子分支到码云，先切换到子分支：git checkout 分支名
然后推送到码云：git push -u origin 远端分支名
```



## 三、用户列表

1、每一个**Element 中提供的组件就是一个类名**，可以用类选择器

```css
.el-header {
    background-color: pink
}
```



2、<font color='red'>axios请求拦截器</font>，添加token，保证拥有获取数据的权限

在main.js 中配置

```js
axios.interceptors.request.use(config => {
    //为请求头，添加token验证的Authorization 字段
    config.headers.Authorization = sessionStorage.getItem('token')
    //必须return
    return config
})
```



3、**布尔值的切换**

```js
this.isCollapse = !this.isCollapse
```



4、表格里面<font color='red'>根据布尔值来渲染这一行中根据自己需求定义的样式</font>（开关，删除等按钮），用**作用域插槽**

```html
<el-table-colum>
    <template slot-scope="scope">
        {{scope.row}}     这可以拿到这一行的数据
    </template>
</el-table-colum>
```



5、<font color='red'>禁止表单</font>输入直接加一个 **disable** 属性即可。



6、<font color='red'>对话框</font>关闭有个 <font color='red'>close</font> 事件，监听这个事件，重置对话框中表单的数据用Element-UI 的`this.$refs.editRef.resetFields` ，但必须每个form-item 项的prop 得和数据项对应。



7、css中除了用百分比，比如100%， <font color='red'>100vh表示沾满这个屏幕。</font>



8、侧边栏动画或过渡



9、User 主体区域用**卡片**组件



10、**NavMenu导航菜单组件**：侧边导航栏<font color='red'>路由地址</font>是<font color='red'>绑定给</font>里面的<font color='red'>index </font>属性，也就是index属性的值就是路由链接地址，如何再去声明路由规则，记得一一对应。（<font color='cornflowerblue'>得先给Menu开启router</font>）



11、表格第<font color='red'>一列要加索引</font>可以增加一列`el-table-column`，设置`type`属性为`index`即可显示从 1 开始的索引号。（参照element_ui即可）



12、<font color='red'>分页栏中的页数发生改变</font>会触发事件，这个事<font color='red'>件会自动传递参数</font>过去（参考element-ui），在methods节点中声明方法时<font color='red'>记得接收</font>这个参数<font color='red'>并改变data中的数据后</font>再发送axios请求



13、<font color='red'>左侧导航默认哪个高亮</font>，`el-menu`中有个<font color='red'>default-active 属性</font>，该属性的值<font color='cornflowerblue'>对应index值</font>，因为我们是通过`el-menu-item :index="'/home/' + subitem.path" `来指定路由链接，所以我们可以直接`:default-active="this.$route.path"`来指定当前页面的默认高亮。



14、<font color='red'>switch开关</font>状态改变的<font color='red'>change事件会传默认值</font>，但如果传参数的时候我们要传id，<font color='red'>记得加第二个参数$event</font>, methods中对应函数的第<font color='cornflowerblue'>二个形参值就是新状态。</font>



15、**添加用户**的时候不要用messageBox弹出框，<font color='cornflowerblue'>用Dialog对话框</font>



16、<font color='red'>同一元素添加多个click 点击事件</font>, 分号隔开即可，但记得加()

```vue
<el-button type="primary" @click="addDialogVisible = false; addUser()">确 定</el-button>
```



17、当value值改变的时候就会触发<font color='red'>input事件</font>，而<font color='red'>change事件</font>是在input失去焦点的时候才触发的。



18、点击某个按钮，比如修改按钮，通过点击修改按钮传递过去的数据，比如说<font color='red'>id， 在后续的修改弹框时要用到</font>，所以可以把这个id <font color='red'>先保存到修改相关的data 数据</font>中。



19、`letter-space: 0.2em `表示字体间的距离。



20、切记，过渡一定是加给css有变化的元素。



21、Vue 提供了 `transition` 的封装组件，<font color='red'>在下列情形中</font>，可以给任何元素和组件添加进入/离开过渡

- 条件渲染 (使用 `v-if`)
- 条件展示 (使用 `v-show`)
- 动态组件
- 组件根节点



22、<font color='red'>**防抖函数为什么用闭包的理解：**</font>

**实例参考用户状态改变时的功能**

```js
滚动事件绑定的实际上是debounce函数return的那个匿名函数，就是个闭包，实现了对变量timer的静态化（每次调用都可以引用到之前的timer），和局部化（避免了对全局命名空间的污染），所以才能实现清空上次定时器防抖的功能。

那么闭包是怎么定义的，大多数就是一句话：有权访问另一个函数作用域内变量的函数都是闭包。举个例子：
function test(){
  var count = 0;
  function counter(){
    count++; 
    console.log(count);
  }
  return counter;
}
var someCount = test();
someCount(); 
someCount(); 
这里打印出来的分别是1、2，
var someCount = test()这一句中，test()返回的是counter，可以这么理解someCount = counter；（只是这么理解，并不能这么用！）
那么someCount()相当于counter()；（ ()是执行函数的意思 ）
所以，变量count并没有被清掉，每次调用返回的都是叠加后的值。

总结：上面提到过的，闭包实现了对变量的静态化和局部化。
```

**<font color='red'>注：</font>**

**<font color='red'>这里必须先调test() 然后赋值给someCount ，后续调用直接调用someCount，这样，上面闭包中的var count = 0 才不会重复被执行，也就实现了变量的静态化和局部化。</font>**



23、<font color='red'>开启sourceMap</font>：在vue.config.js 中配置如下。

```js
const debug = process.env.NODE_ENV !== 'production'
module.exports = {
  lintOnSave: false,
  devServer: {
    // 开发环境下设置为编译好以后直接打开浏览器浏览
    open: true
  },
  configureWebpack: (config) => {
    if (debug) {
      // 开发环境配置
      config.devtool = 'source-map'
    }
  },
  css: {
    // 查看CSS属于哪个css⽂件
    sourceMap: true
  }
}

// vue-cli版本如果低于3.0 就用下面的方式， vue -V查看版本
module.exports = {
  lintOnSave: false,
  devServer: {
    // 开发环境下设置为编译好以后直接打开浏览器浏览
    open: true
  },
  configureWebpack: (config) => {
    // 调试JS
    config.devtool = 'source-map'
  },
  css: {
    // 查看CSS属于哪个css⽂件
    sourceMap: true
  }
}
```



24、**<font color='red'>刷新页面要想当前页的数据和数据条数不发生改变</font>**，就要在pagesize 和pagenum **<font color='cornflowerblue'>变化时保存的sessionStorage 中</font>**，然后页面一上来获取用户列表的时候，判断sessionStorage 中是否有这两项值，有就赋值给请求参数，再发请求获取数据。



25、提交代码并创建新分支。

```xml
创建user子分支，并将代码推送到码云
A.创建user子分支  git checkout -b user
B.将代码添加到暂存区 git add .
C.将代码提交并注释 git commit -m '添加完成用户列表功能'
D.将本地的user分支推送到码云 git push -u origin user
E.将user分支代码合并到master:
切换到master   git checkout master
合并user       git merge user
F.将本地master分支的代码推送到码云  git push

创建rights子分支
A.创建rights子分支 git checkout -b rights
B.将本地的rights分支推送到码云 git push -u origin rights
```



## 四、用户权限

1、作用域插槽通过scope.row 拿到的数据可以放到<pre>{{ scope.row }}</pre> 中渲染，数据更清晰。



2、v-bind 支持<font color='red'>class 属性绑定一个数组</font>

```html
<div :clsss="['class1', 'class2', a = 0 ? '' : 'class3']"></div>
```





3、删除权限后如果掉函数获取列表页<font color='red'>会重新渲染</font>页面会自动合上权限列表，我们可以给角色(role) 的children <font color='red'>重新赋值</font>（请求返回的数据）`role.children = res.data`



4、<font color='red'>树形展示数据</font>用Tree 组件。里面的props属性是指向一个对象数据，是指用哪个属性进行嵌套，这里是用children，该对象还有一个label 就是文字字段。



5、**退出分配权限对话框的时候要清空数组，否则里面的ID 越来越多**



6、我们是通过给不同的角色分配不同的权限，然后是用户对应哪些角色。



7、渲染每个角色权限的时候，<font color='red'>要渲染角色列表而不是权限列表</font>，该角色下的<font color='cornflowerblue'>第一个children 是一级权限。</font>

```js
渲染的时候，一共三级权限，三重for 循环，第一、二个都是渲染行el-row， 三级权限下面没有children了，就直接渲染列el-col里面的tag标签。
```



8、<font color='red'>**删除完某一个权限的时候不要重新请求数据刷新页面**</font>，而是将删除后返回的新数据赋值给整个角色的children

```js
async deleteRight(role, rightId) {
      const { data: res } = await axios.delete(`roles/${role.id}/rights/${rightId}`)
      if (res.meta.status !== 200) return this.$message.error('权限删除失败')
      // 重新给该角色赋值新的权限，不要给整个this.roleList赋值
      role.children = res.data
    }
}
data中有整个角色列表roleList，不要给他重新赋值，给这个角色也就是传递过来的这个role重新赋值即可
```



9、**<font color='red'>el-table-column type="expand" 展开之后的宽度默认是100%，通过底部设置css的margin等操作不生效</font>**，比如展开之后放的是layout布局，直接在el-row标签里设置`style="margin-left: 50px; margin-right: 30px;"`展开之后的宽度。



10、<font color='red'>**勾选默认权限时没有根据id 查对应角色权限，使用在HTML中要用 scope.row 把这个角色传过去。再去递归遍历他有没有children**</font>， 没有children 说明就是最后一级权限，把最后一级权限的id 放到数组中就行。

```js
这里一定要选重新获取权限列表，因为el-tree 的data绑定的是 rightsList, 如果不重新获取，就会一直显示第一次渲染的列表。
//node就是该角色信息（role）,defKeys是我们自己定义的数组，绑定到tree上
getDefKeys(node, defKeys) {
  if (!node.children) {
    return defKeys.push(node.id)
  }
  node.children.forEach((item) => {
    this.getDefKeys(item, defKeys)    //注意这里传item，不要传item.children
  })

}
```



11、select 最好绑定的是对应角色的ID，:label 来呈现要显示的文字。



12、**提交代码**

A.将代码推送到暂存区 git add .
B.将代码提交到仓库 git commit -m '完成了权限功能开发'
C.将rights分支代码推送到码云 git push
D.将代码合并到master 
    git checkout master
    git merge rights
E.将master代码推送到码云
    git push



## 五、分类管理

1、分类数据<font color='red'>表格中涉及三级树形结构的展示</font>，element-ui 中没有对应的组件，要借助第三方插件，<font color='red'>vue-table-with-tree-grid</font>（里面的:column 一定要有，对应的prop也一定要声明）



2、添加分类是<font color='red'>父级的级联选中框</font>element-ui 中模板中没有添加<font color='cornflowerblue'>props属性， 但一定得加</font>，这样才能知道嵌套关系和怎样展示数据。



3、如果<font color='red'>只是数据展示不出来</font>，没有其他问题，可能只是**<font color='red'>单词拼写错误</font>**，和后端返回的不一致，仔细检查



4、提交代码

制作完添加分类之后，将代码提交到仓库，推送到码云,将goods_cate分支合并到master
git add .
git commit -m '完成商品分类'
git push
git checkout master
git merge goods_cate



## 六、参数管理

1、用<font color='red'>v-for 生成 tag 标签</font>，我们要<font color='red'>动态切换标签和input 表单时</font>，通过 `v-if="inputVisible"`和 `v-model="inputValue"`来动态绑定数据，但这样<font color='red'>所有分类都被绑定了同一份数据</font>，就会有问题，所以，我们应该在<font color='red'>拿到数据的时候遍历给他们每个item 添加自定义属性</font>inputVisible 和 inputValue，等渲染的时候就可以通过<font color='red'>scope.row.inputVisible</font> 和<font color='red'> scope.row.inputValue</font> 来渲染。



2、我们拿到所有数据后要遍历每一项，将其属性名通过split(',')分割成数组后出现赋值给该属性名，在HTML中我们v-for 生成tag 时是遍历scope.row.attr_name



3、点击 +new Tag 按钮切换成input 表单时自动获取焦点。

```js
this.$nextTick(_ => {
    this.$refs.saveTagInput.$refs.input.focus()
})
// 后面这个$refs.input 是为了获取原生表单。
```



4、<font color='red'>**如果在 A 函数中调用的 B 函数是异步函数，则 A函数调用B函数这行代码之后的代码会先执行。**</font>

``` js
(在methods节点中)
A() {
    this.B()
},
async B() {
    await axios...
}
解决方案：(A函数也用async修饰，如何await 等B执行完)
async A() {
    await this.B()
}
```



5、<font color='red'>删除Tag 标签时</font>，因为Tag 是用v-for 动态生成的，所有可以根据index索引，用<font color='red'>`row.attr_vals.splice(index,1)` </font>删除这项，在发送请求更新数据库数据



## 七、商品列表

1、全局时间过滤器

```js
Vue.filter('dataFormat', function(originVal) {
    const dt = new Date(originVal)
    const y = dt.getFullYear()
    const m = (dt.getMonth + 1 + '').padStart(2, '0')  //先拿到当前月，然后转化为字符						串，padStart方法第一个参数是指几位，这里是两位，不足两位则前面补字符串0
    //const m = ('0' + dt.getMonth() + 1).slice(-2) 这样也行
    const d = (dt.getDate() + '').padStart(2, '0')
    
    const hh = (dt.getHours() + '').padStart(2, '0')
    const mm = (dt.getMinutes() + '').padStart(2, '0')
    const ss = (dt.getSeconds() + '').padStart(2, '0')
    
    return `${y}-${m}-${d} ${hh}:${mm}:${ss}`
})
```



2、添加商品页面进度条和下面tabs侧边栏的联动效果，进度条是通过`:active`属性来变化当前进度，只要在el-tags 侧边栏v-model 成 :active 的属性值，然后下面的el-tab-pane 的name 属性指定与上面对应的值。（核心就是进度条和侧边栏共用一个数据项）



3、input 表单的<font color='cornflowerblue'>type 指定为number 就是只能输入数字</font>



4、**<font color='red'>阻止侧边tab 栏的切换</font>**， 要用到Tab 里的一个属性<font color='red'> :before-leave="beforeTabLeave" </font>**绑定的是一个函数**，具体用法查看element-ui



5、**<font color='red'>我们图片upload 上传的时候没有用到axios</font>**，所以axios 拦截器配置的Authorization 字段对它无效，因此上传图片的时候服务器返回的数据显示**<font color='red'>无效的token</font>**。

upload 组件有个headers 属性，接收一个对象，在里面配置请求头就行了。



6、<font color='red'>删除图片时</font>要根据删除事假得到的图片路径去数组中找对应的索引值，然后根据索引值用splice 方法删除。<font color='red'>找索引用findIndex( item => item.pic === filePath) ，</font>如果找到了，该方法就会返回索引值，我们用一个变量接收即可。



7、<font color='red'>富文本编辑器插件：vue-quill-editor </font>（可以在vue ui 界面去安装，也可以直接去github中搜索）



8、最后提交商品信息的时候，表单里面有一项<font color='red'>goods_cate 也就是选择的商品id </font>要求是字符串，用，拼接，但这一项是v-model 绑定在级联选择器上，是个数组，如果我们<font color='red'>直接用join 将数组拼接成字符串然后重新赋值给他，会报错，因为级联选择器必须绑定的是数组。</font>

解决方法：<font color='red'>**拼接之前先将表单数据进行深拷贝**</font>

```js
lodash cloneDeep(obj)    这个包可以实现深拷贝
```

```js
import _ from 'lodash'   接收lodash习惯用_ , 就更$ 接收jQuery一个意思。
const form = _.cloneDeep(obj)
```

**另一个方法，来自评论区，不知是否可靠**

```js
JSON.parse(JSON.stringify(obj))
```



9、商品搜索框节流的防抖的用法**<font color='red'>未解决</font>**



10、添加商品的表单得包含在侧边栏外面，因为el-tabs 的下一级只能是el-tabs-pane



## 八、订单管理



## 九、数据统计

1、绘制图表用 <font color=red size=4>**echarts**</font>



2、导入echarts 用<font color='red'> improt * as echarts form 'echarts'</font>



3、初始化和配置项等要写在mounted() 生命周期函数中。



4、用lodash 合并两个对象。

```js
_.merge(obj1, obj2)

Object.assign(obj1, obj2) 和扩展运算符... 只适用于对象的属性是基本数据类型。
```





## 十、优化上线(P190)

**<font color='red'>实现步骤：</font>**
A.生成打包报告，根据报告优化项目
B.第三方库启用CDN
C.Element-UI组件按需加载
D.路由懒加载
E.首页内容定制



1、顶部展示进度条 nprogress 第三方包(github)

```js
里面的start() 事件在axios 中的request 拦截器中调用，
done() 事件在axios 的response 拦截器中使用，表示axios 请求成功，关闭进度条
```



2、项目<font color='red'>build 阶段</font>不允许有 console.log() ， 否则会报警告，我们需要配置一个babel插件：

<font color='red'>babel-plugin-transform-remove-console</font>（搜索打开npm文档）

可以不用按官方文档配置的那个文档，可以配置在babel.config.js 中，但这个插件是全局生效，在开发阶段也会生效。

```js
解决方案：
const proPlugins = []
if(process.env.NODE_ENV === 'production') {      //判断是开发环境还是生产环境
    proPlugins.push('transform-remove-console')
}
然后在下面配置
module.exports = {
    ......
    plugins: [
        
        ...proPlugins
    ]
}
```



### **1、 项目优化策略**

#### **(1). 生成打包报告**

打包时，为了直观地发现项目中存在的问题，可以在打包时生成报告。生成报告的方式有两种：

① 通过<font color='red'>命令行参数的形式生成报告</font>

```js
// 通过 vue-cli 的命令选项可以生成打包报告
 // --report 选项可以生成 report.html 以帮助分析包内容
 vue-cli-service build --report
```

② 通过可视化的UI面板直接查看报告（<font color='red'>推荐</font>）（命令行vue ui）

 在可视化的UI面板中，通过<font color='red'>控制台</font>和<font color='red'>分析面板</font>，可以方便地看到项目中所存在的问题。



#### **(2). 通过 vue.config.js 修改 webpack 的默认配置**

通过 vue-cli 3.0 工具生成的项目，<font color='red'>默认隐藏了所有 webpack 的配置项</font>，目的是为了屏蔽项目的配置过程，让程序员把工作的重心，放到具体功能和业务逻辑的实现上。



如果程序员有修改 webpack 默认配置的需求，可以在项目根目录中，按需创建 <font color='red'>vue.config.js </font>这个配置文件，从而对项目的打包发布过程做自定义的配置（具体配置参考 https://cli.vuejs.org/zh/config/#vue-config-js）。

```js
// vue.config.js 
 // 这个文件中，应该导出一个包含了自定义配置选项的对象
 module.exports = {
 // 选项...
 }
```



#### **(3). 为开发模式与发布模式指定不同的打包入口**

默认情况下，Vue项目的<font color='red'>开发模式</font>与<font color='red'>发布模式</font>，共用同一个打包的入口文件（即 <font color='red'>src/main.js</font>）。为了将项目

的开发过程与发布过程分离，我们可以为两种模式，各自指定打包的入口文件，即：

① 开发模式的入口文件为<font color='red'> src/main-dev.js</font>

② 发布模式的入口文件为 <font color='red'>src/main-prod.js</font>



#### **(4). configureWebpack 和 chainWebpack**

在 vue.config.js 导出的配置对象中，新增 <font color='red'>configureWebpack 或 chainWebpack </font>节点，来自定义 webpack 

的打包配置。

在这里， configureWebpack 和 chainWebpack 的<font color='red'>作用相同</font>，唯一的区别就是它们修改 webpack 配置的方

式不同：

① chainWebpack 通过<font color='red'>链式编程</font>的形式，来修改默认的 webpack 配置

② configureWebpack 通过<font color='red'>操作对象</font>的形式，来修改默认的 webpack 配置

两者具体的使用差异，可参考如下网址：

https://cli.vuejs.org/zh/guide/webpack.html#webpack-%E7%9B%B8%E5%85%B3



#### **(5). 通过 chainWebpack 自定义打包入口**

代码示例如下：(写入vue.config.js)

```js
module.exports = {
 chainWebpack: config => {
     //发布模式
 config.when(process.env.NODE_ENV === 'production', config => {
 config.entry('app').clear().add('./src/main-prod.js')
 })
     //开发模式
 config.when(process.env.NODE_ENV === 'development', config => {
 config.entry('app').clear().add('./src/main-dev.js')
 })
} }
```

写完之后要在src 下<font color='red'>新建这两个文件</font> main-dev.js 和 main-prod.js，内容就是将原来main.js 中所有的代码赋值过去，<font color='red'>并删除main.js </font>



#### **(6). 通过 externals 加载外部 CDN 资源**

**默认情况下，通过 import 语法导入的第三方依赖包，最终会被打包合并到同一个文件中**，从而导致打包成功后，单文件体积过大的问题。



为了解决上述问题，可以通过 webpack 的<font color='red'> externals 节点</font>，来配置并加载外部的 CDN 资源。凡是声明在

externals 中的第三方依赖包，都不会被打包。

**原理：p200**



具体配置代码如下：(一样写在vue.config.js)

```js
写在发布模式的config 中
config.set('externals', {
	vue: 'Vue', 
	'vue-router': 'VueRouter',
	axios: 'axios',
	lodash: '_',
	echarts: 'echarts',
	nprogress: 'NProgress', 
    'vue-quill-editor': 'VueQuillEditor'
})
```

同时，需要在 public/index.html 文件的头部，添加如下的 CDN 资源引用：（这样，在src/main-prod.js 中的那些样式表就可以删除）

```js
<!-- nprogress 的样式表文件 --> <link rel="stylesheet" href="https://cdn.staticfile.org/nprogress/0.2.0/nprogress.min.css" />
<!-- 富文本编辑器 的样式表文件 -->
<link rel="stylesheet" href="https://cdn.staticfile.org/quill/1.3.4/quill.core.min.css" />
<link rel="stylesheet" href="https://cdn.staticfile.org/quill/1.3.4/quill.snow.min.css" />
<link rel="stylesheet" href="https://cdn.staticfile.org/quill/1.3.4/quill.bubble.min.css" />
```

同时，需要在 public/index.html 文件的头部，添加如下的 CDN 资源引用：(这些文件有依赖，注意顺序)

```js
<script src="https://cdn.staticfile.org/vue/2.5.22/vue.min.js"></script>
<script src="https://cdn.staticfile.org/vue-router/3.0.1/vue-router.min.js"></script>
<script src="https://cdn.staticfile.org/axios/0.18.0/axios.min.js"></script>
<script src="https://cdn.staticfile.org/lodash.js/4.17.11/lodash.min.js"></script>
<script src="https://cdn.staticfile.org/echarts/4.1.0/echarts.min.js"></script>
<script src="https://cdn.staticfile.org/nprogress/0.2.0/nprogress.min.js"></script>
<!-- 富文本编辑器的 js 文件 -->
<script src="https://cdn.staticfile.org/quill/1.3.4/quill.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vue-quill-editor@3.0.4/dist/vue-quill-editor.js"></script>
```

<font color=red size=5>注：</font>

如果此时报错找不到$router, 是因为在index 和 路由的.js 文件中都使用的注册了VueRouter

```js
解决方案：
可在public/index.html 中加入if判断：

<!-- 只有prod(发布阶段)生效 -->
<% if(htmlWebpackPlugin.options.isProd){ %> 
    ...
  
    <script src="https://cdn.bootcdn.net/ajax/libs/vue/2.6.11/vue.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/vue-router/3.2.0/vue-router.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/axios/0.24.0/axios.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/lodash.js/4.17.21/lodash.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/echarts/5.2.2/echarts.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/nprogress/0.2.0/nprogress.min.js"></script>
 
    <!-- element-ui 的 js文件 -->
    <script src="https://cdn.bootcdn.net/ajax/libs/element-ui/2.15.6/index.js"></script>
 	...
    <% } %>
<!-- 只有prod(发布阶段)生效 -->
```





#### **(7). 通过 CDN 优化 ElementUI 的打包**

虽然在开发阶段，我们启用了 element-ui 组件的按需加载，尽可能的减少了打包的体积，但是那些被按需加载的组件，还是占用了较大的文件体积。此时，我们可以将 element-ui 中的组件，也通过 CDN 的形式来加

载，这样能够进一步减小打包后的文件体积。

具体操作流程如下：

① 在 main-prod.js 中，注释掉 element-ui 按需加载的代码

② 在 index.html 的头部区域中，通过 CDN 加载 element-ui 的 js 和 css 样式

```js
<!-- element-ui 的样式表文件 --> <link rel="stylesheet" href="https://cdn.staticfile.org/element-ui/2.8.2/theme-chalk/index.css" />
<!-- element-ui 的 js 文件 -->
<script src="https://cdn.staticfile.org/element-ui/2.8.2/index.js"></script>
```



#### **(8). 首页内容定制**

不同的打包环境下，首页内容可能会有所不同。我们可以通过插件的方式进行定制，插件配置如下：

```js
chainWebpack: config => {
 config.when(process.env.NODE_ENV === 'production', config => {
 config.plugin('html').tap(args => {
 args[0].isProd = true
 return args
 })
 })
 config.when(process.env.NODE_ENV === 'development', config => {
 config.plugin('html').tap(args => {
 args[0].isProd = false
 return args
 })
 })
}
```

在 public/index.html 首页中，可以根据 isProd 的值，来决定如何渲染页面结构：

```js
<!– 按需渲染页面的标题 -->
<title><%= htmlWebpackPlugin.options.isProd ? '' : 'dev - ' %>电商后台管理系统</title>
<!– 按需加载外部的 CDN 资源 -->
<% if(htmlWebpackPlugin.options.isProd) { %>
<!—通过 externals 加载的外部 CDN 资源-->
<% } %>
```



#### **(9). 路由懒加载**

当打包构建项目时，JavaScript 包会变得非常大，影响页面加载。如果我们能把不同路由对应的组件分割成

不同的代码块，然后当路由被访问的时候才加载对应组件，这样就更加高效了。

具体需要 3 步：

① 安装 <font color='red'>@babel/plugin-syntax-dynamic-import</font> 包。

② 在<font color='red'> babel.config.js </font>配置文件中声明该插件。

③ 将路由改为按需加载的形式，示例代码如下：

```js
const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')
const Bar = () => import(/* webpackChunkName: "group-foo" */ './Bar.vue')
const Baz = () => import(/* webpackChunkName: "group-boo" */ './Baz.vue')

//webpackChunkName 表示分组的意思，同一组的文件被打包到同一个js中，请求其中一个的时候，这个组的全部组件会被加载出来
```

关于路由懒加载的详细文档，可参考如下链接：

https://router.vuejs.org/zh/guide/advanced/lazy-loading.html



### 2、项目上线(205)

 **项目上线相关配置**

1. 通过 node 创建 web 服务器。

2. 开启 gzip 配置。

3. 配置 https 服务。

4. 使用 pm2 管理应用。



#### **1. 通过 node 创建 web 服务器**

创建 node 项目（记得npm init --y），并安装 express，通过 express 快速创建 web 服务器，将 vue 打包生成的 dist 文件夹，

托管为静态资源即可，关键代码如下：

(和vue_shop 文件夹同目录下创建 vue_shop_serve文件夹，将vue_shop 中的打包好的dist 文件夹复制过来)

```js
//在app.js下
const express = require('express')
// 创建 web 服务器
const app = express()
// 托管静态资源
app.use(express.static('./dist'))
// 启动 web 服务器
app.listen(80, () => {
 console.log('web server running at http://127.0.0.1')
})
```



#### **2. 开启 gzip 配置**

使用<font color='red'> gzip</font> 可以减小文件体积，使传输速度更快。

② 可以通过服务器端使用 Express 做 gzip 压缩。其配置如下：

```js
// 安装相应包
 npm install compression -S
 // 导入包
 const compression = require('compression');
 // 启用中间件(这一行必须写在静态资源托管那行代码之前)
 app.use(compression());
```



#### **(3). 配置 HTTPS 服务**（了解）

<font color='red'>为什么要启用 HTTPS 服务？</font>

* 传统的 HTTP 协议传输的数据都是明文，不安全

* 采用 HTTPS 协议对传输的数据进行了加密处理，可以防止数据被中间人窃取，使用更安全

<font color='red'>**申请 SSL 证书（https://freessl.org）**</font>

① 进入 https://freessl.cn/ 官网，输入要申请的域名并选择品牌。 

② 输入自己的邮箱并选择相关选项。

③ 验证 DNS（在域名管理后台添加 TXT 记录）。

④ 验证通过之后，下载 SSL 证书（ full_chain.pem 公钥；private.key 私钥）。



**在后台项目中导入证书**

```js
 const https = require('https');
 const fs = require('fs');
 const options = {
 cert: fs.readFileSync('./full_chain.pem'),
 key: fs.readFileSync('./private.key')
 }
 https.createServer(options, app).listen(443);
```



#### **(4). 使用 pm2 管理应用**

① 在服务器中安装 pm2：npm i pm2 -g 

② 启动项目：pm2 start 脚本 --name 自定义名称

③ 查看运行项目：pm2 ls 

④ 重启项目：pm2 restart 自定义名称

⑤ 停止项目：pm2 stop 自定义名称

⑥ 删除项目：pm2 delete 自定义名称
