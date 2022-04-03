# Map数据结构

### 1、基本使用

Map数据结构类<font color='red'>似于对象</font>，是键值对的集合，传统的键值只限于字符串，但<font color='red'>Map的键不限于字符串</font>

**<font color='red'>特性：当一个键同时被赋值的时候，后面的值会覆盖前面的值</font>**

```js
//创建Map对象
let map = new Map()

//设置Map的值：set 方法
map.set('id', 1001)
map.set({skill: js}, 1)  //键可以是对象等

//size属性: 返回成员总数
map.size

//get方法: 获取某个value
map.get('key')

//has方法，返回布尔值
map.has('key')

//delete 删除某个key
map.delete('key')

//对象里面有Object.keys() 获取所有的键值返回一个数组
map.keys()  //和对象一样，但返回的是一个伪数组
map.values()   //所有的value值，同样是伪数组

//遍历
//1、for...of
for(let key of map.keys()) {}
//2、forEach 方法，
map.forEach((value,key) => {})  //注意第一个参数是value

//entries()  返回键值对的遍历器
for(let item of map.entries()) {}   //返回的是数组形式，按插入顺序返回
```



### 2、应用案例

#### 1、数组去重

```js
const num = [1,2,11,3,2,12,1]
const map = new Map()

//数组值设置为key， 索引设置为值
num.forEach((item,index) => {
    map.set(item,index)
})
//将得到的伪数组转化为真正的数组
const arr = Array.from(map.keys())
```

#### 2、两数之和

leetcode算法第一题