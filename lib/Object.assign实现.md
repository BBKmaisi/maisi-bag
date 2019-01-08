### Object.assign 模拟实现

实现一个 Object.assign 大致思路如下：

1、判断原生 Object 是否支持该函数，如果不存在的话创建一个函数 assign，并使用 Object.defineProperty 将该函数绑定到 Object 上。

2、判断参数是否正确（目标对象不能为空，我们可以直接设置{}传递进去,但必须设置值）。

3、使用 Object() 转成对象，并保存为 to，最后返回这个对象 to。

4、使用 for..in 循环遍历出所有可枚举的自有属性。并复制给新的目标对象（使用 hasOwnProperty 获取自有属性，即非原型链上的属性）。

实现代码如下，这里为了验证方便，使用 assign2 代替 assign。注意此模拟实现不支持 symbol 属性，因为ES5 中根本没有 symbol 。

	if (typeof Object.assign2 != 'function') {
	  // Attention 1
	  Object.defineProperty(Object, "assign2", {
	    value: function (target) {
	      'use strict';
	      if (target == null) { // Attention 2
	        throw new TypeError('Cannot convert undefined or null to object');
	      }

	      // Attention 3
	      var to = Object(target);
	        
	      for (var index = 1; index < arguments.length; index++) {
	        var nextSource = arguments[index];

	        if (nextSource != null) {  // Attention 2
	          // Attention 4
	          for (var nextKey in nextSource) {
	            if (Object.prototype.hasOwnProperty.call(nextSource, nextKey)) {
	              to[nextKey] = nextSource[nextKey];
	            }
	          }
	        }
	      }
	      return to;
	    },
	    writable: true,
	    configurable: true
	  });
	}