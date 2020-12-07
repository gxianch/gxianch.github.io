---
title: Vue学习笔记1-基础
date: 2020-12-07 16:50:46
categories: [vue]
toc: true
mathjax: true
top: 100
tags:
  - 笔记

---



Vue学习笔记1-基础
<!-- more -->

## 条件与循环

* v-if
* v-for

### v-if

条件判断式，根据表达式的真伪进行页面处理。

~~~html
<p v-if="seen">快看我！</p>
~~~

### v-for

处理数组循环，将数据循环显示到页面上。

~~~html
<ol>
  <li v-for="game in games">
    {{ game.title }}
  </li>
</ol>
~~~

## 综合例

~~~html
<div id="myApp">
    <h3>游戏列表</h3>
    <div v-if="seen">2017最新发卖</div>
    <ol>
        <li v-for="game in games">{{game.title}} / {{game.price}}元</li>
    </ol>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp',
        data: {
            seen: true,
            games: [
                {title:'勇者斗恶龙',price:400},
                {title:'超级马里奥',price:380},
                {title:'我的世界',price:99},
            ],
        },
    });
</script>
~~~

## 处理用户输入

* v-model

### v-model

为页面输入框进行数据邦定，例如：

+ input
+ select
+ textarea
+ components

### 使用例

~~~html
<div id="myApp">
    <p>您最喜欢的游戏是：{{mygame}}</p>
    <input v-model="mygame">
</div>
<script>
    var myApp = new Vue({
        el: '#myApp',
        data: {
            mygame: '超级马里奥'
        },
    });
</script>
~~~

## 按钮事件

* v-on

### v-on

为页面元素绑定各种事件。（keydown, keyup, click, dbclick, load, etd.）

~~~html
<div id="myApp">
    <p>您最喜欢的游戏是：{{mygame}}</p>
    <button v-on:click="btnClick('我的世界')">我的世界</button>
    <button v-on:click="btnClick('勇者斗恶龙')">勇者斗恶龙</button>
    <button v-on:click="btnClick('塞尔达传说')">塞尔达传说</button>
    <button @click="btnClick('魔界战记5')">魔界战记5</button>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp',
        data: {
            mygame: '超级马里奥'
        },
        methods: {
            btnClick: function(pname){
                this.mygame = pname;
            },
        },
    });
</script>
~~~

## 组件

* component

### component

定义页面的局部区域块，完成单独的页面**小**功能。

还是不明白？看个图吧！

~~~html
<div id="myApp">
  <ol>
    <game-item v-for="item in games" v-bind:game="item"></game-item>
  </ol>
</div>
<script>
/* 组件定义：game-item */
Vue.component('game-item', {
  props: ['game'],
  template: '<li>{{ game.title }}</li>'
});
/* Vue对象实例化 */
var myApp = new Vue({
  el: '#myApp',
  data: {
    games: [
      { title: '斗地主' },
      { title: '打麻雀' },
      { title: 'UNO' }
    ]
  }
});
</script>
~~~

## 过滤器

* filters

### filters

格式化变量内容的输出。（日期格式化，字母大小写，数字再计算等等）

~~~html
<div id="myApp">
    <p>{{message}}</p>
    <p>{{message | toupper }}</p>
    <hr>
    <p>现在的vue.js学习进度是{{num}}({{num | topercentage}})。</p>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp',
        data: {
            message: 'hello vue.js world.',
            num: 0.3
        },
        filters: {
            toupper: function(value){
                return value.toUpperCase();
            },
            topercentage: function(value){
                return value * 100 + '%';
            },
        },
    });
</script>
~~~
## 计算属性

* computed

### computed

处理元数据，便于进行二次利用。（比如：消费税自动计算功能）

~~~html
<div id="myApp">
    今年3月3日发卖的任天堂新一代主机Switch的价格是：{{price}}円，含税价格为：{{priceInTax}}円，折合人民币为：{{priceChinaRMB}}元。
</div>
<script>
    var myApp = new Vue({
        el: '#myApp',
        data: {
            price: 29980
        },
        computed: {
            priceInTax: function(){
                return this.price * 1.08;
            },
            priceChinaRMB: function(){
                return Math.round(this.priceInTax / 16.75);
            },
        },
    });
</script>
~~~

## 观察属性

* $watch

### $watch

与computed属性类似，用于观察变量的变化，然后进行相应的处理。（我从Angular而来）

~~~html
<div id="myApp">
    <p>今年3月3日发卖的任天堂新一代主机Switch的价格是：{{price}}円，含税价格为：{{priceInTax}}円，折合人民币为：{{priceChinaRMB}}元。</p>
    <button @click="btnClick(10000)">加价10000円</button>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp',
        data: {
            price: 29980,
            priceInTax: 0,
            priceChinaRMB: 0,
        },
        watch: {
            price: function(newVal, oldVal){
                console.log(newVal, oldVal);
                this.priceInTax = Math.round(this.price * 1.08);
                this.priceChinaRMB = Math.round(this.priceInTax / 16.75);
            },
        },
        methods: {
            btnClick: function(newPrice){
                this.price += newPrice;
            },
        },
    });
</script>
~~~

## 设定计算属性

* setter

### setter

设置计算属性，同步更新元数据的值。（又是令人费解的描述）

~~~html
<div id="myApp">
    <p>今年3月3日发卖的任天堂新一代主机Switch的价格是：{{price}}円，含税价格为：{{priceInTax}}円，折合人民币为：{{priceChinaRMB}}元。</p>
    <button @click="btnClick(10800)">把含税改价为10800円</button>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp',
        data: {
            price: 29980
        },
        computed: {
            priceInTax: {
                get:function(){
                    return this.price * 1.08;
                },
                set: function(value){
                    this.price = value / 1.08;
                }
            },
            priceChinaRMB: function(){
                return Math.round(this.priceInTax / 16.75);
            },
        },
        methods: {
            btnClick: function(newPrice){
                this.priceInTax = newPrice;
            },
        },
    });
</script>
~~~

## Class绑定

* v-bind:class

### v-bind:class

为html标记绑定样式单class属性。

~~~html
<div id="myApp">
    <div v-bind:class="{active:isActive}">红色文本1</div>
    <div :class="{active:isActive}">红色文本2</div>
    <button @click="btnClick">改变class吧</button>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp',
        data: {
            isActive: true,
        },
        methods: {
            btnClick: function(){
                this.isActive = false;
            },
        },
    });
</script>
~~~

## Class对象绑定

* v-bind:class

### v-bind:class

为html标记绑定样式单class对象。

~~~html
<div id="myApp">
    <div :class="myClass">红色文本</div>
    <button @click="btnClick">改变class吧</button>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp',
        data: {
            myClass: {
                active: true,
                big: true,
            },
        },
        methods: {
            btnClick: function(){
                this.myClass.big = false;
            },
        },
    });
</script>
~~~

## 条件渲染

* v-if
* v-else-if
* v-else

### v-if

判断vue.js的变量的值，然后执行页面渲染逻辑。（if-elseif-else）

~~~html
<div id="myApp">
    <h1 v-if="result == 0">成绩未公布</h1>
    <h1 v-else-if="result < 60">{{result}}分 - 考试不及格</h1>
    <h1 v-else-if="result < 80">{{result}}分 - 还需努力</h1>
    <h1 v-else>{{result}}分 - 考得不错，玩游戏吧！</h1>
    <button @click="btnClick">考试成绩</button>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp', 
        data: {
            result: 0
        },
        methods: {
            btnClick: function(){
                this.result = Math.round(Math.random() * 100);
            },
        },
    });
</script>
~~~

## 元素显示

* v-show

### v-show

标记是否显示html元素。(注意：v-show设置的标记在html DOM中不会消失)

~~~html
<div id="myApp">
    <h1 v-show="result">任天堂新一代主机Switch</h1>
    <button @click="btnClick">考试成绩</button>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp', 
        data: {
            result: true
        },
        methods: {
            btnClick: function(){
                this.result = !this.result;
            },
        },
    });
</script>
~~~

## 列表渲染

* v-for

### v-for

循环数组元素，整理内容显示到页面上。

~~~html
<div id="myApp">
    <ul>
        <li v-for="(game, index) in games">({{index}}) {{game.title}} / 售价：{{game.price}}元</li>
    </ul>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp', 
        data: {
            games: [
                {title:"勇者斗恶龙",price:500},
                {title:"库跑卡丁车",price:400},
                {title:"马里奥世界",price:550},
            ]
        },
    });
</script>
~~~

## JS对象迭代

* v-for

### v-for

循环JS对象，把对象内容循环显示到页面上。

~~~html
<div id="myApp">
    <h1>JS对象迭代</h1>
    <div v-for="(value, key) in mygame">
        {{ key }} : {{ value }}
    </div>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp', 
        data: {
            mygame: {
                title: "乌贼娘2代",
                price: 350,
                pubdate: "2017年夏季",
                category: "射击",
                agerange: "全年龄",
            },
        },
    });
</script>
~~~

## 事件处理器

* v-on:(event)/@(event)

### v-on:(event)

页面元素的事件绑定。（click,keyup,load等等）

~~~html
<div id="myApp">
    <h1>事件处理器</h1>
    <input id="txtName" v-on:keyup="txtKeyup($event)">
    <button id="btnOK" v-on:click="btnClick($event)">OK</button>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp', 
        data: {},
        methods: {
            txtKeyup: function(event){
                this.debugLog(event);
            },
            btnClick: function(event){
                this.debugLog(event);
            },
            debugLog:function(event){
                console.log(
                    event.srcElement.tagName, 
                    event.srcElement.id, 
                    event.srcElement.innerHTML, 
                    event.key?event.key:""
                );
            },
        },
    });
</script>
~~~

## 表单控件绑定

* v-model
* input[type="text"]

### v-model

为表单控件元素创建双向数据绑定。（将JS变量的值“快速”设定到控件上，然后将用户输入的内容“快速”设置回JS变量）

~~~html
<div id="myApp">
    <h1>表单控件绑定</h1>
    <input type="text" v-model="message" placeholder="来呀，编辑我吧！">
    <p>Message is: {{ message }}</p>
    <textarea v-model="message" placeholder="加入多行编辑" rows="8" cols="34"></textarea>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp', 
        data: {
            message: "我爱马里奥",
        },
        methods: {
        },
    });
</script>
~~~

## 表单复选框

* v-model
* input[type="checkbox"]

### 表单复选框绑定

~~~html
<div id="myApp">
    <h1>表单复选框</h1>
    <input type="checkbox" id="生化危机7" value="生化危机7" v-model="checkedGames">
    <label for="生化危机7">生化危机7</label>
    <input type="checkbox" id="模拟飞行" value="模拟飞行" v-model="checkedGames">
    <label for="模拟飞行">模拟飞行</label>
    <input type="checkbox" id="塞尔达传说" value="塞尔达传说" v-model="checkedGames">
    <label for="塞尔达传说">塞尔达传说</label>
    <br>
    <p>您选择的游戏是: {{ checkedGames }}</p>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp', 
        data: {
            checkedGames: []
        },
        methods: {
        },
    });
</script>
~~~


## 表单单选按钮

* v-model
* input[type="radio"]

### 表单单选按钮绑定

~~~html
<div id="myApp">
    <h1>表单单选按钮</h1>

    <h3>选择性别</h3>
    <input type="radio" id="male" value="男" v-model="pickedSex">
    <label for="male">男</label>
    <br>
    <input type="radio" id="female" value="女" v-model="pickedSex">
    <label for="female">女</label>

    <h3>选择爱好</h3>
    <input type="radio" id="game" value="玩游戏" v-model="pickedHobby">
    <label for="game">玩游戏</label>
    <br>
    <input type="radio" id="movie" value="看电影" v-model="pickedHobby">
    <label for="movie">看电影</label>

    <h3>选择结果</h3>
    <p>性别: {{ pickedSex }}</p>
    <p>爱好: {{ pickedHobby }}</p>

</div>
<script>
    var myApp = new Vue({
        el: '#myApp', 
        data: {
            pickedSex: "",
            pickedHobby: "",
        },
        methods: {
        },
    });
</script>
~~~

## 表单下拉框

* v-model
* select

### 表单下拉框绑定

~~~html
<div id="myApp">
    <h3>你最喜欢的NBA球星是：</h3>
    <select v-model="likedNBAStar" style="width:210px;">
        <option>科比</option>
        <option>詹姆斯</option>
        <option>哈登</option>
        <option>库里</option>
        <option>杜兰特</option>
    </select>

    <h3>我的全明星：</h3>
    <select v-model="likedNBAStars" multiple style="width:210px;height:210px;">
        <option>阿德托昆博</option>
        <option>怀特赛德</option>
        <option>阿尔德里奇</option>
        <option>戴维斯</option>
        <option>格里芬</option>
        <option>詹姆斯</option>
        <option>杜兰特</option>
        <option>巴特勒</option>
        <option>德罗赞</option>
        <option>哈登</option>
        <option>科比</option>
        <option>韦德</option>
        <option>伦纳德</option>
        <option>库里</option>
        <option>欧文</option>
        <option>保罗</option>
        <option>林树豪</option>
    </select>

    <h3>选择结果</h3>
    <p>我最最喜欢: {{ likedNBAStar }}</p>
    <p>我的全明星: {{ likedNBAStars }}</p>

</div>
<script>
    var myApp = new Vue({
        el: '#myApp', 
        data: {
            likedNBAStar: null,
            likedNBAStars: [],
        },
        methods: {
        },
    });
</script>
~~~

## 表单修饰符

* .lazy
* .number
* .trim

### .lazy

用户输入内容时不做绑定数据的更新处理，在控件的onchange事件中更新绑定的变量。

~~~html
用户名：<input v-model.lazy="username">
~~~

### .number

将用户输入的内容转换为数值类型，如果用户输入非数值的时候，则返回NaN。

~~~html
年龄：<input v-model.number="age" type="number">
~~~

### .trim

自动去掉用户输入内容两端的空格。

~~~html
意见：<input v-model.trim="content">
~~~

## 综合例

~~~html
<div id="myApp">
    <h1>用户注册</h1>
    
    <div>
        <label for="username">用户：</label>
        <input type="text" id="username" v-model.lazy="username" @change="checkUsername">
        <span v-if="checkUsernameOK">可注册</span>
    </div>
    <div>
        <label for="age">年龄：</label>
        <input type="number" id="age" v-model.number="age">
    </div>
    <div>
        <label for="content">个人简介：</label><br/>
        <textarea id="content" v-model.trim="content" cols="55" rows="8"></textarea>
    </div>
    
    <h4>信息区</h4>
    <p>{{username}}</p>
    <p>{{age}}</p>
    <p><pre>{{content}}</pre></p>
</div>
<script>
    var myApp = new Vue({
        el: '#myApp', 
        data: {
            username: "",
            checkUsernameOK: false,
            age: "",
            content: "",
        },
        methods: {
            checkUsername: function(){
                if (this.username.length > 0) this.checkUsernameOK = true;
                else this.checkUsernameOK = false;
            },
        },
    });
</script>
~~~

