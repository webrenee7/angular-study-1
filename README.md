# angular控制器，模块，指令学习
## 什么是AngularJS？
> AngularJS是一个javaScript框架，可通过 script 标签添加到网页中
它通过指令扩展了HTML，且通过表达式绑定数据到页面。

## 框架与类库
* 类库：是一些函数的集合，能够帮助你写web应用，起主导作用的是你，由你决定何时使用类库，常见类库有jquery，zepto
* 框架：是一种特殊的，已经实现了的web应用，你只需要对它填充具体的业务逻辑，这里框架是起主导作用的，由它来根据具体的应用逻辑来调用你的代码。

## 为什么使用AngularJS？
为了克服HTML在构建应用上的不足而设计的，使得开发现代的单页应用程序（SPA：Single Page Applications）变得更加容易。

## AngularJS的核心特性有哪些？
* MVVM 双向数据绑定(Model-View-ViewModel)model变化，view也变化
* 模版 将html文件解析到DOM中
* 模块化与依赖注入
* 指令可以用来创建自定义的标签，也可以用来装饰元素或者操作DOM属性。

# 模块
模块是应用程序中不同部分的容器。

你可以通过 AngularJS 的 angular.module 函数来创建模块：
```html
<div ng-app="appModule">...</div>
```

```js
//第一个参数：模块名称
//第二个参数：模块依赖列表，如果没有，则为空数组
var app = angular.module("appModule", []);
```

# 控制器
控制器的特点：
* 1.定义一个控制器，就产生一个作用域，自带$scope参数（这个参数名是固定的不能变），表示当前作用域
* 2.子能访问父，父不能访问子
* 3.控制器和DOM是平行的
* 4.可以嵌套

```html
<div ng-controller="parentCtrl">
    {{age}}<!--子能访问父，父不能访问子  所以这里不能输出内容-->
    <div ng-controller="childCtrl">
        {{name}}
    </div>
</div>
```

```js
var app=angular.module('appModule',[]);
app.controller('parentCtrl',function ($rootScope,$scope) {//$rootScope是根作用域,$scope是当前作用域
    $rootScope.a=1;
    $scope.name="xy";
});
app.controller('childCtrl',function ($rootScope,$scope) {
    $scope.age="123";
})
```

使用控制器的注意事项：
* 1.不要在控制器中操作dom,能不操作dom就不要操作dom
* 2.为了防止压缩，第二个参数一般写成数组形式

# 表达式
AngularJS 表达式写在双大括号内：{{ expression }}。
AngularJS 表达式 很像 JavaScript 表达式：它们可以包含文字、运算符,变量，函数执行。
与 JavaScript 表达式不同，AngularJS 表达式可以写在 HTML 中。
与 JavaScript 表达式不同，AngularJS 表达式不支持条件判断，循环及异常。
与 JavaScript 表达式不同，AngularJS 表达式支持过滤器。

双花括号支持的表达式：
- 赋值运算
```js
{{name=1}}
```

- 简单运算
```js
{{2*3}}
```

- 三元运算
```js
{{name?name:'没有值'}}
```

# 指令
## 内置的ng指令
### ng-app
指定angular应用程序的控制范围,产生$rootScope根作用域
```html
<html lang="en" ng-app='appModule'>
```

### ng-controller
指定控制器的范围
```html
<div ng-controller="appCtrl"></div>
```

### ng-bind/ng-bind-template/ng-bind-html
绑定数据
```html
<!-- 双花括号绑定数据 -->
{{name}}
<!-- 绑定一个变量 -->
<div ng-bind='name'></div>
<!-- 绑定多个变量 -->
<div ng-bind-template='{{name}}{{age}}'></div>
<!-- 绑定html -->
<div ng-bind-html='h1'></div>
```

```js
var app=angular.module('appModule',[]);
app.controller('appCtrl',['$scope','$sce',function($scope,$sce){
    $scope.name='xy';
    $scope.age=26;
    $scope.h1=$sce.trustAsHtml('<h1>欢迎来到书城！</h1>');
}]);
```

### ng-cloak
页面加载时防止应用闪烁
```css
[ng-cloak]{
    display:none;
}
```

```html
<body ng-cloak>
...
</body>
```

### ng-init
初始化数据
```html
<div ng-controller="appCtrl">
    <input type="text" ng-init="{name=xy;age=26}"/>
</div>
```

一般我们不这样初始化，直接到控制器中初始化
```js
var app=angular.module('appModule',[]);
app.controller('appCtrl',['$scope',function($scope){
    $scope.name='xy';
    $scope.age=26;
}]);
```

### ng-repeat
遍历数组或者对象
```html
<ul>
    <li ng-repeat="value in arr track by $index">
        {{value}}
    </li>
</ul>
<ul>
    <li ng-repeat="(key,book) in books">
        {{key}}  {{book.name}}
        {{key}}  {{book.price}}
        <!-- {{odd}} 奇数项 -->
        <!-- {{even}} 偶数项 -->
        <!-- {{first}} 是否是第一项(布尔值) -->
        <!-- {{last}} 是否是最后一项(布尔值) -->
        <!-- {{middle}} 是否是中间项(布尔值) -->
    </li>
</ul>
```

```js
var app=angular.module('appModule',[]);
app.controller('myCtrl',['$scope',function ($scope) {
    $scope.arr=[1,1,3,4];
    $scope.books=[
        {name:'angular教程',price:30,id:1},
        {name:'node教程',price:60,id:2},
        {name:'vue教程',price:50,id:3}
    ];
}]);
```

注意：
track by $index：解决重复数据遍历的错误
- 1>.用ng-repeat指令遍历一个javascript数组，当数组中有重复元素的时候,AngularJS会报错,这是因为ng-Repeat不允许collection中存在两个相同Id的对象。
- 2>.如果是javascript对象类型数据，那么就算内容一摸一样，ng-repeat也不会认为这是相同的对象。

### ng-class
给html元素动态绑定一个或多个类
```css
.red{ background: red; color: #fff;}
.bg1{ background: black;}
.bg2{ background: purple;}
.bg3{ background: lightgreen;}
.size{ font-size: 60px;}
```

```html
<!--1、根据flag判断用哪个class-->
<!--class 和ng-class不冲突 动态的永远比静态的优先级高-->
<div class="red" ng-class="{true:'bg1',false:'bg2'}[flag]">根据flag判断用哪个class</div>

<!--2、根据属性值判断是否生效-->
<!--ng-style比ng-class优先级高-->
<div class="red" ng-class="{bg3:true,size:true}" ng-style="{background:'lightblue',fontSize:'100px'}">根据属性值判断是否生效</div>
```

```js
var app=angular.module('appModule',[]);
app.controller('myCtrl',['$scope',function ($scope) {
    $scope.flag=true;
}]);
```

### ng-show/ng-hide/ng-if
- ng-show 在表达式为true时显示
- ng-hide 在表达式为true时隐藏
- ng-if 在表达式为 false 时移除 HTML 元素，表达式为true时添加HTML元素

### ng-src
添加图片地址

### ng-options与ng-repeat
ng-repeat 指令是通过数组来循环 HTML 代码来创建下拉列表
ng-options 指令更适合创建下拉列表，它有以下优势：
- 使用 ng-options 的选项的一个对象
- ng-repeat 是一个字符串。

```html
<!-- 使用ng-repeat -->
<select ng-model="selectedSite">
<option ng-repeat="x in sites" value="{{x.url}}">{{x.site}}</option></select>
<h1>你选择的是: {{selectedSite}}</h1>

<!-- 使用ng-options -->
<select ng-model="selectedSite" ng-options="x.site for x in sites"></select>
<h1>你选择的是: {{selectedSite.site}}</h1>
<p>网址为: {{selectedSite.url}}</p>
```

```js
var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope) {
   $scope.sites = [
	    {site : "Google", url : "http://www.google.com"},
	    {site : "Runoob", url : "http://www.runoob.com"},
	    {site : "Taobao", url : "http://www.taobao.com"}
	];
});
```

## 自定义指令
```html
<!--指令的四种使用方法-->
标签：<app-dir n="{{name}}">标签形式展现</app-dir>
属性：<div app-dir a="age">属性形式展现</div>
注释：<!-- directive:app-dir --><!--注释前后必须有空格，否则不显示-->
样式名：<div class="app-dir" fn="say(text)">样式名形式展现</div>
```

```js
var app=angular.module('appModule',[]);

//angular初始化完成后就有$rootScope,可以在这里声明全局变量
app.run(function ($rootScope) {
    $rootScope.name='xy';
    $rootScope.age=123;
    $rootScope.say=function(text){
        alert(text);
    }
});

//指令名称如果是驼峰式命名，使用时先转化为小写，然后在第二个单词前面加"-"
app.directive('appDir',function () {
    return {
        require:'xxx',//要交互的指令
        replace:true,//是否替换掉指令所在容器本身，默认不替换，当值为true时，模板的顶级元素只能有一个
        template:'<div ng-click='fn({text:name})'><b ng-transclude></b>我是指令</div>',//指令模板
        //templateUrl:'panel.html',//使用模板地址
        restrict:'EAMC',//==>element attribute  comment  class 指令的显示形式，默认显示EA
        transclude:true,//保留指令容器里面的内容，并且通过ng-transclude显示位置
        //scope:true,//产生独立作用域，但是能获取到父级变量
        scope:{//产生独立的作用域，与父级断绝关系
            //也就是说写一个指令，就产生一个独立的作用域
            //如果要使用父级的变量，需要一个中间人，这个中间人可以是"@" "=" "&"这三个符号
            name:"@n",//@传递的是字符串类型的，如果@后面和scope属性名一样，可以省略不写
            age:"=a",//=传递的是变量
            say:"&fn"//&传递的是函数
        },
        //link函数：用来链接作用域和DOM视图，在插入指令时执行
        link:function(scope,element,attrs,xxxCtrl){
            //scope  当前指令所在作用域
            //element  当前指令所在元素  是个JQ对象
            //attrs  当前指令所在元素的属性集合
            //xxxCtrl  依赖于xxx的控制器注入进来的  当这个link执行时，会调用xxx的link函数
        }
    }
});
```

## 指令之间的交互
```html
<!-- 需求：点击girl，输出love-shopping，love-cry -->
<girl love-shopping love-cry>女孩</girl>
```

```js
var app = angular.module('appModule',[]);
app.directive('girl',function () {
    return {
        controller:function ($scope) {//$scope代表当前girl的作用域
            $scope.arr = [];//父亲用一个容器来存储传进来的内容
            this.add = function (kind) { //在控制器上增加一个add方法
                $scope.arr.push(kind);
            }
        },
        link:function (scope,element,attrs) {
            element.on('click',function () {
                console.log(scope.arr);
            });
        }
    }
});
app.directive('loveCry',function () {
    return {
        require:'girl',
        //第四个参数是依赖于girl的控制器实例化后注入进来的
        link:function (scope,element,attrs,girlCtrl) { //当指令执行时会调用link函数
            girlCtrl.add('loveCry');
        }
    }
});
app.directive('loveShopping',function () {
    return {
        require:'girl',
        link:function (scope,element,attrs,girlCtrl) {
            girlCtrl.add('loveShopping');
        }
    }
});
```

# AngularJS事件
AngularJS 支持以下事件:
- ng-click
- ng-dbl-click
- ng-mousedown
- ng-mouseenter
- ng-mouseleave
- ng-mousemove
- ng-keydown
- ng-keyup
- ng-keypress
- ng-change

ng-click实例：
```html
<button ng-click="fn($event)">老谭酸菜</button>
<button ng-click="fn($event)">康师傅</button>
```

```js
var app=angular.module('appModule',[]);
app.controller('myCtrl',['$scope',function ($scope) {
    $scope.fn=function(e){
        alert(e.target.innerHTML)
    }
}]);
```

# 过滤器
## 内置过滤器
过滤器可以使用一个管道字符（|）添加到表达式和指令中。

### uppercase/lowercase
格式化为大写/小写
```html
{{'aBcD'|uppercase}}
{{'aBcD'|lowercase}}
```

### currency
格式化为货币形式
```html
{{100|currency}}
```

### date
格式化时间
```html
{{1486267313587|date:'yy年mm月dd日 hh:mm:ss'}}
```

### limitTo
对象的截取
```html
{{'afdsafds'|limitTo:5}}
```

### number
将数字格式化为千分符形式，并设置保留几位小数：
```html
{{341321|number:5}}
```

### json
将json对象格式化为json字符串
```html
{{{name:'xy',age:123}|json}}
```

### filter
查询过滤器
```html
<div>{{ [{name:'张三',age:12},{name:'李四',age:23}] | filter:{name:'张三'} }}</div>

<div>{{ [{name:'张三',age:12},{name:'李四',age:23}] | filter:2 }}</div>
```

### orderBy
排序  true是降序，false是升序,默认是false
```html
{{ [{name:'张三',age:12,score:123},{name:'李四',age:23,score:78}] | orderBy:"score":false}}
```

## 自定义过滤器
```html
<div>{{150|mycurrency:'$':'*'}}</div>
```

```js
app.filter('mycurrency',function () {
    return function (data,param1,param2) {
        if(typeof param1=="undefined"){
            return 'xy'+data;
        }else{
            return param1+data+param2;
        }
    }
})
```

# watch/apply
ng-model原理：用watch监控数据，如果数据发生变化 用apply去改变视图

## watch 监控数据
```html
<input type="text" ng-model="count"><br>
单价 {{price}} <br>
邮费 {{post}} <br>
总价 {{total()+post}} <br>
```

```js
<!--需求：满百包邮 即满100邮费变为0-->
var app = angular.module('appModule',[]);
app.controller('myCtrl',['$scope',function ($scope) {
    $scope.count = 10;
    $scope.price = 20;
    $scope.post = 8;
    $scope.total = function total() {
        return  $scope.count*$scope.price
    };
    //监控总价执行后的结果，如果结果变化会传递一个newVal
    $scope.$watch($scope.total,function (newVal,oldVal) {
        //当总价大于100 则让邮费为0
        $scope.post = newVal>=100?0:8
    });
}])
```

## apply刷新视图
数据变化但是视图未刷新，要通知angular去更新视图
angular自带的指令（服务）都可以自动调用$apply方法
```js
var app = angular.module('appModule', []);
//watch + apply = ng-model;
app.controller('myCtrl', function ($scope,$interval,$timeout) {
    $scope.aa = 100;
    //数据变化但是视图未刷新，要通知angular去更新视图
    //angular自带的指令（服务）都可以自动调用$apply方法
    var timer = $timeout(function () {
        $scope.aa++;
        //$scope.$apply();
    }, 1000);
    $timeout.cancel(timer);
    //$interval.cancel(timer);
});
```

# jsonp
$http服务上的jsonp方法    $sce上的trustAsResourceUrl
```js
var app = angular.module('appModule', []);
app.controller('myCtrl', ['$scope', '$http', '$sce', function ($scope, $http, $sce) {
    $http.jsonp(
        $sce.trustAsResourceUrl('https://sp0.baidu.com/5a1Fazu8AA54nxGko9WTAnF6hhy/su?wd=' + $scope.query),{jsonpCallbackParam: 'cb'}).then(function (res) { //成功的回调
        ...
    });
});
```

# 服务
什么是服务？
> 在 AngularJS 中，服务是一个函数或对象，可在你的 AngularJS 应用中使用。

## 内置服务
AngularJS 内建了30 多个服务。下面主要说说常用的几个内置服务

### $http
$http 是一个用于读取web服务器上数据的服务。

### $sec
SCE，即strict contextual escaping,我的理解是 严格的上下文隔离
通过$sce服务把一些地址变成安全的、授权的链接...简单地说，就像告诉门卫，这个陌生人其实是我的好朋友，很值得信赖，不必拦截它！

$sce常用的方法有：
- $sce.trustAs(type,name);
- $sce.trustAsHtml(value);
- $sce.trustAsUrl(value);
- $sce.trustAsResourceUrl(value);
- $sce.trustAsJs(value);

### $location
$location 服务，它可以返回当前页面的 URL 地址。
它可以使用 DOM 中存在的对象，类似 window.location 对象，但 window.location 对象在 AngularJS 应用中有一定的局限性。
```js
app.controller('myCtrl', function($scope, $location) {
    $scope.back=function(){
        $location.path('/');//跳转到首页
    }
});
```

### $timeout
AngularJS $timeout 服务对应了 JS window.setTimeout 函数。
```js
app.controller('myCtrl', function($scope, $timeout) {
    $scope.myHeader = "Hello World!";
    $timeout(function () {
        $scope.myHeader = "How are you today?";
    }, 2000);
});
```

### $interval
AngularJS $interval 服务对应了 JS window.setInterval 函数。

## 自定义服务
### provider
- provider 最大的服务，并且可以配置
```js
app.config(function(MyProvider){
    MyProvider.aa = 200;
})
app.provider('My',function(){
    this.aa = 100;
    this.$get = function(){
        return {name:1}
    }
})
```

### factory
- factory 声明后是一个$get函数
```js
app.factory('My',function(){
    return {name:1,age:8}
})
```

### service
- service 是factory返回值的构造函数
```js
app.service('My',function(){
    this.name = 1;
    this.age= 9;
})
```

### value
- value 是factory返回的值
```js
app.value('My',{name:1,age:8})
```

### constant
constant同value用法

# 依赖注入
什么是依赖注入？
> 依赖注入（Dependency Injection，简称DI）是一种软件设计模式，在这种模式下，一个或更多的依赖（或服务）被注入（或者通过引用传递）到一个独立的对象（或客户端）中，然后成为了该客户端状态的一部分。

> 一句话 --- 没事你不要来找我，有事我会去找你。

AngularJS 提供很好的依赖注入机制。以下5个核心组件用来作为依赖注入：
- value
- factory
- service
- provider
- constant

API参考：http://www.runoob.com/AngularJS/AngularJS-reference.html
