# 内置服务

## $parse
自动计算表达式，配合 $watch 使用

![](http://ww4.sinaimg.cn/large/801b780agw1f89ex9g2p9j211q09g76c.jpg)

![](http://ww3.sinaimg.cn/large/801b780agw1f89eyr3bl2j20oi078abg.jpg)

$watch 函数中的 expr 参数，为 HTML 中使用 ng-model 绑定的变量
newVal 与 oldVal 则类似于 setter 中的新值与旧值
[测试链接](http://jsbin.com/UWuLALOf/1/edit?html,js,output)

## $interpolate
在字符串模版中进行插值操作，配合 $watch 使用

```js
angular.module('myApp', [])
  .controller('MyController', 
    function($scope, $interpolate) {
      $scope.to = 'ari@fullstack.io';
      $scope.emailBody = 'Hello {{ to }},\n\nMy name is Ari too!';
      // Set up a watch
      // emailBody 是某 HTML 模版，当中夹杂 AngualrJS 的表达式
      $scope.$watch('emailBody', function(body) {
        if (body) {
          var template = $interpolate(body); // 从模版 HTML 中生成模版
          $scope.previewText = 
            template({to: $scope.to}); // 将数据绑定到模版中
        }
      });
});
```

[测试链接](http://jsbin.com/oDeFuCAW/1/edit?html,js,output)

