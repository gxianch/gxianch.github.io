---
title: Vue学习笔记2-组件
date: 2020-12-07 16:50:46
categories: [vue]
toc: true
mathjax: true
top: 100
tags:
  - 笔记

---

Vue学习笔记2-组件
<!-- more -->


## 组件：基础的基础

* 组件（Component,Portlet）

### 组件

组件就是页面上的一小块区域内容，完成一个小的页面功能。

## 综合例

~~~html
<div id="myApp">
    <today-weather></today-weather>
</div>
<script>
    Vue.component('today-weather', {
        template: '<div>今天下雨，出不去啦，干什么呢？看Youtube吧!</div>'
    });
    var myApp = new Vue({
        el: '#myApp', 
    });
</script>
~~~

## 组件：局部的组件

* 组件的局部注册

### 组件

Vue.js的组件不仅可以单独声明注册使用，还可以在Vue实例中进行局部注册使用。

## 综合例

~~~html
<div id="myApp">
    <my-weather></my-weather>
</div>
<script>
    var WeatherComponent = {
        template: '<div>今天下雨，随它去吧！</div>'
    };
    var myApp = new Vue({
        el: '#myApp', 
        data: {
        },
        components: {
            'my-weather': WeatherComponent
        },
    });
</script>
~~~

## 组件：表行组件

* 制作表行组件

### 表行组件

为自己的页面表格编写表行组件。

## 综合例

~~~html
<div id="myApp">
    <h1>2017年最期待的游戏是：</h1>
    <table border="1">
        <tr>
            <td>编号</td>
            <td>游戏名称</td>
        </tr>
        <my-row1></my-row1>
        <my-row2></my-row2>
        <my-row3></my-row3>
    </table>
</div>
<script>
    Vue.component('my-row1', {
        template: '<tr><td>(1)</td><td>塞尔达传说:荒野之息(3/3)</td></tr>'
    });    
    Vue.component('my-row2', {
        template: '<tr><td>(2)</td><td>新马里奥赛车(4/28)</td></tr>'
    });    
    Vue.component('my-row3', {
        template: '<tr><td>(3)</td><td>喷射乌贼娘2代</td></tr>'
    });    
    var myApp = new Vue({
        el: '#myApp', 
        data: {
        },
        methods: {
        },
    });
</script>
~~~

## 组件：数据

* 组件的数据函数

### 组件的数据

为Vue.js组件添加数据，使组件可以动态显示各种数据，注意是数据函数，不是数据属性。

## 综合例

~~~html
<div id="myApp">
    <div>今天的天气是<today-weather/></div>
</div>
<script>
    Vue.component('today-weather', {
        template: '<strong>{{todayWeather}}</strong>',
        data: function(){
            return {
                todayWeather: '雨加雪'
            };
        }
    });
    var myApp = new Vue({
        el: '#myApp', 
    });
</script>
~~~

## 组件：传递数据

* 为组件传递数据

### 组件数据传递

制作可接受参数的组件。

## 综合例

~~~html
<div id="myApp">
    <h1>您的成绩评价</h1>
    <test-result :score="50"></test-result>
    <test-result :score="65"></test-result>
    <test-result :score="90"></test-result>
    <test-result :score="100"></test-result>
</div>
<script>
    Vue.component('test-result', {
        props: ['score'],
        template: '<div><strong>{{score}}分，{{testResult}}</strong></div>',
        computed: {
            testResult: function() {
                var strResult = "";
                if (this.score < 60)
                    strResult = "不及格";
                else if (this.score < 90)
                    strResult = "中等生";
                else if (this.score <= 100)
                    strResult = "优秀生";
                return strResult;
            }
        }
    });
    var myApp = new Vue({
        el: '#myApp', 
    });
</script>
~~~

## 组件：传递变量

* 为组件传递变量数据

### 组件的数据

制作可接受变量参数的组件。

## 综合例

~~~html
<div id="myApp">
    <div>请输入您的名字：<input v-model="myname"></div>
    <hr/>
    <say-hello :pname="myname" />
</div>
<script>
    Vue.component('say-hello', {
        props: ['pname'],
        template: '<div>你好，<strong>{{pname}}</strong>！</div>',
    });
    var myApp = new Vue({
        el: '#myApp', 
        data: {
            myname: 'Koma'
        }
    });
</script>
~~~

## 组件：参数验证

* props:组件参数验证语法

### 组件的数据

为组件中接受到的变量进行逻辑验证。

## 综合例

~~~html
<div id="myApp">
    <h1>身世之谜</h1>
    <show-member-info name="koma" :age="25" :detail="{address:'earth', language:'世界语'}"></show-member-info>
</div>
<script>
    Vue.component('show-member-info', {
        props: {
            name: {
                type: String,
                required: true,
            },
            age: {
                type: Number,
                validator: function (value) {
                    return value >= 0 && value <= 130;
                }                
            },
            detail: {
                type: Object,
                default: function() {
                    return {
                        address: 'US',
                        language: 'English',
                    };
                }
            },
        },
        template: '<div>姓名：{{this.name}}<br/>' 
            + '年龄：{{this.age}}岁<br/>'
            + '地址：{{this.detail.address}}<br/>'
            + '语言：{{this.detail.language}} </div>',
    });
    var myApp = new Vue({
        el: '#myApp', 
    });
</script>
~~~

## 组件：事件传递

* v-on
* $emit

### v-on

侦听组件事件，当组件触发事件后进行事件处理。

### $emit

触发事件，并将数据提交给事件侦听者。

## 综合例

~~~html
<div id="myApp">
    <h1>人生加法</h1>
    <add-method :a="6" :b="12" v-on:add_event="getAddResult"></add-method>
    <hr/>
    <h3>{{result}}</h3>
</div>
<script>
    Vue.component('add-method', {
        props: ['a', 'b'],
        template: '<div><button v-on:click="add">加吧</button></div>',
        methods: {
            add: function(){
                var value = 0;
                value = this.a + this.b;
                this.$emit('add_event', {
                    result:value
                });
            }
        },
    });
    var myApp = new Vue({
        el: '#myApp', 
        data: {
            result: 0
        },
        methods: {
            getAddResult: function(pval){
                this.result = pval.result;
            }
        },
    });
</script>
~~~

## 组件：slot插槽

* slot

### slot

slot是父组件与子组件的通讯方式，可以将父组件的内容显示在子组件当中。

## 综合例

~~~html
<div id="myApp">
    <say-to pname="koma">
        你的视频做的太差了。
    </say-to>
    <say-to pname="mike">
        你千万不要学koma。
    </say-to>
    <say-to pname="john">
        你教教他们两个吧。
    </say-to>
</div>
<script>
    Vue.component('say-to', {
        props: ['pname'],
        template: '<div>'
            + '你好，<strong>{{pname}}</strong>！'
            + '<slot></slot>'
            + '</div>',
    });
    var myApp = new Vue({
        el: '#myApp',
    });
</script>
~~~


## 组件：组合slot

* slot命名

### slot命名

在子组件中通过为多个slot进行命名,来接受父组件的不同内容的数据。

## 综合例

~~~html
<div id="myApp">
    <nba-all-stars c="奥尼尔" pf="加内特">
        <span slot="sf">皮尔斯</span>
        <span slot="sg">雷阿伦</span>
        <span slot="pg">隆多</span>
    </nba-all-stars>
</div>
<script>
    Vue.component('nba-all-stars', {
        props: ['c', 'pf'],
        template: '<div>'
            + '<div>中锋：{{c}}</div>'
            + '<div>大前：{{pf}}</div>'
            + '<div>小前：<slot name="sf"></slot></div>'
            + '<div>分卫：<slot name="sg"></slot></div>'
            + '<div>控卫：<slot name="pg"></slot></div>'
            + '</div>',
    });
    var myApp = new Vue({
        el: '#myApp',
    });
</script>
~~~

