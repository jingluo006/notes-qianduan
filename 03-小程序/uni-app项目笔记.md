**项目文档地址：https://www.escook.cn/docs-uni-shop**



## 一、首页

顶部的搜索框要实现<font color='red'>吸顶效果</font>同 position 中的stick ，然后设置 top: 0, z-index: 999



## 二、搜索页面

<font color='red'>一行显示不了禁止换行也溢出的部分用小圆点表示</font>

```css
.item {
    white-space: nowarp;
    overflow: hidden;
    // 文本溢出后用 ... 代替
    text-overflow: ellipsis
}
```



 解决<font color='red'>关键字前后顺序</font>的问题

```js
computed: {
  historys() {
    // 注意：由于数组是引用类型，所以不要直接基于原数组调用 reverse 方法，以免修改原数组中元素的顺序
    // 而是应该新建一个内存无关的数组，再进行 reverse 反转
    return [...this.historyList].reverse()
  }
}
```

解决<font color='red'>关键词重复</font>的问题

```js
// 保存搜索关键词为历史记录
saveSearchHistory() {
  // this.historyList.push(this.kw)

  // 1. 将 Array 数组转化为 Set 对象
  const set = new Set(this.historyList)
  // 2. 调用 Set 对象的 delete 方法，移除对应的元素
  set.delete(this.kw)
  // 3. 调用 Set 对象的 add 方法，向 Set 中添加元素
  set.add(this.kw)
  // 4. 将 Set 对象转化为 Array 数组
  this.historyList = Array.from(set)
}
```

**<font color='red'>注：</font>**<font color='cornflowerblue'>先移除再添加</font>是为了保证顺序，保证是添加到最后一个，然后再通过计算属性来翻转。



## 三、商品列表页

onPullDownRefresh下拉刷新的时候，从新请求数据后要uni.stopPullDownRefresh，这个可以通过以函数参数的形式传过去

```js
this.getGoodsList(() => uni.stopPullDownRefresh)
// 在getGoodsList中
getGoodsList(cb) {
    cb && cb()
}
```



后端返回的数据数html，里面的图片都几像素空隙，需要给img 加display:block

```js
// 使用字符串的 replace() 方法，为 img 标签添加行内的 style 样式，从而解决图片底部空白间隙的问题
  res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g, '<img style="display:block;" ')
```

<font color='red'>注：</font>先用正则表达式选出，再替换。如果后续还有替换，可以在后面链式.replace()



## 四、购物车页面

谁使用mixin 时，谁就可以直接通过 this. 来调用里面的方法和使用里面的数据。



## 五、登录

登录前的准备不再用getUserInfo来获取了，用 uni.getUserProfile，也不用指定button的open-type了

```js
uni.getUserProfile({
    desc: "",
    success: (res) => {
        console.log(res.userInfo)
    }
})
```

