## 1、什么是模块化

+ 存在文件作用域
+ 兼顾导入`import`以及`export`功能

	## 2、Exports

+ **<u>nodejs</u>**提供`exports`用来导出

  ```javascript
  // 导出属性
  exports.name = '123';
  // 导出方法
  exports.add = function (x , y){
     return x + y;
  }
  ```

+ **nodejs**默认每一个文件都返回一个`module.exports`对象，默认返回`{}`

+ `exports`只是指向`module.exports`

  ```javascript
  // 如果指向导出一个变量或者方法，可以这么写
  // 导出变量
  module.exports = "123"
  module.exports = function (x , y){
      return x + y;
  }
  ```

  ```javascript
  // 如果修改了exports的指向，则exports无效
  exports.name = '123';
  exports = {
      age:'1234'
  }
  // 这种情况下，导出的属性只有name。因为exports在后续的代码中更换了引用指向。
  ```

## 3、require

+ **require**默认是递归式查找

  + a.js

    ```javascript
  require('./b')
  console.log('a end')	 
    ```

  + b.js

  ```javascript
  console.log('b start')
  ```

  最后得到的结果

  ```javascript
  b start
  a end
  ```

  

