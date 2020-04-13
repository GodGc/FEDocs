# ZOOM

3月25日下午2:00面试

**ZOOM的面试经过了2轮技术面+1轮HR面，过程都很顺利，但是没有发offer，个人感觉可能是offer的价格没谈拢吧，还是比较遗憾的，因为当时对ZOOM的感觉还是不错的，很想进，可惜缘分未到**

## 一面：前端面

> 一面时长1小时，实际要多一点，因为自己提前10分钟进入zoom会议室就直接开始问答了，当然第一个就是进行自我介绍。
> 接下来是：

- es5到es6，有什么变化？
- 原型链，怎么不用instance of知道一个对象的类型。
- 正则表达式中的exce方法，
  该函数返回一个数组，其中存放匹配的结果。如果未找到匹配，则返回值为 null
  `/e/.exec("The best things in life are free!");`
- apply和call的区别。怎么用apply计算一个数组的最大值。
- bind是做什么用的，如何实现？
- typeof和instanceof的区别。
- 怎么设置一个变量的默认值。
- 继承有哪些种？答了ES5的组合继承和ES6的class继承，class继承可以继承static静态变量
- 为什么js有变量提升？逐行编译、加快编译速度、减少错误程度
- 问了parseInt()方法，答：不清楚
- arguments是什么？
- 如何给数组对象添加一个自定义方法：挂载到Array.prototype上
- `use strict`是什么：答严格模式，不会有变量提升等操作
- 自执行函数是什么？具体怎么写？
- 如何监听页面变化：答：自己暂时还未遇到要监听页面变化的场景，一般都是通过事件来监听某些变化的，比如`onClick`事件等。
   正确：`MutationObserver`
   >MutationObserver给开发者们提供了一种能在某个范围内的DOM树发生变化时作出适当反应的能力.该API设计用来替换掉在DOM3事件规范中引入的Mutation事件.
   
   ```javascript
   var MutationObserver = window.MutationObserver || window.WebKitMutationObserver || window.MozMutationObserver;
   
  // 获取ell元素
  var ell = document.getElementById('ell')
  // 构造观察ell元素的实例
    var observer = new MutationObserver(function (mutationsList) {
        // mutationsList参数是个MutationRecord对象数组，描述了每个发生的变化
        mutationsList.forEach(function (mutation) {
            var target = mutation.target;
            if (!target || !target.render) {
                return;
            }
            // 变化的类型
            switch(mutation.type) {
              case 'characterData':
                // 文本内容变化
                target.render();
                break;
              case 'attributes':
                // rows属性值发生了变化
                target.render();
                break;
            }
        });
    });
    
    // 开始观察ell元素并制定观察内容
    observer.observe(ell, {
        attributes: true,
        subtree: true,
        characterData: true,
        attributeFilter: ['rows']    
    });
   ```
- ES6有什么新特性
- 数组的一些常用方法：答了shift\unshift\pop\push(忘记说了XDD)、splice
  实际常用的有：
    - concat
    - join
    - push\pop
    - shift\unshift
    - slice
    - splice
    - map\forEach
    - every\filter\find、entries、values、keys、includes(ES6)
- 深拷贝：JSON、递归深拷贝
- 如果对比2个对象，相等还是相同
- 基本数据类型和复杂数据类型有哪些，都是存储在哪里的：基本数据类型储存在栈、复杂数据类型储存在堆；为什么：读取数据更加方便，尤其针对复杂数据类型
- 异步流程：对比回调地狱答了promise
- fetch和XMLHttpRequest的区别：答了对应的api和什么时候获取数据，**忘记说fetch会返回一个promise了**
- a==1 && a==2，什么时候会成立（这个没想出来，答了一个可能和object有关）
- 重绘和重排是什么，什么时候会发生
- DOM树的结构和渲染流程：树状结构、栅格化和绘入过程
- cookie\localStorage\sessionStorage的区别：储存数据大小，网络请求是否携带，是否会过期等等
- css中 `content`属性是干什么的：`:after、:before`伪元素必需的一条属性，如果不加的话是不会显示的...
- px\em\rem的区别
- BFC是什么，什么时候会触发

- XSS攻击
   **记反了，说成了CSRF攻击...不知道面试官有没有察觉...感觉他还听得津津有味的T^T**
   XSS攻击是html代码中嵌入了script脚本内容，`<div><script>alert(1)</script></div>`, 或者在url参数上拼接了script代码
   **如何防范：对敏感字符进行转义**
   ```javascript
    function escape(str) {
      str = str.replace(/&/g, '&amp;')
      str = str.replace(/</g, '&lt;')
      str = str.replace(/>/g, '&gt;')
      str = str.replace(/"/g, '&quto;')
      str = str.replace(/'/g, '&#39;')
      str = str.replace(/`/g, '&#96;')
      str = str.replace(/\//g, '&#x2F;')
      return str
    }
    ```
    
- 有什么想问的嘛？
  在ZOOM这家公司里，前端主要负责哪些工作：还技术债、目前前后端还未分离，做工程化、组件化抽离开发（带有公司风格的）


## 二面：用人经理面试

简单聊了聊 因为关于前端的都已经面好了，所以给出了几道算法题，其中有一题记得之前看到过，但是一直没再温习（主要是也用不到），并没有答上来

- 有100层楼，手中有2个鸡蛋，问：如何才能找到鸡蛋仅会被摔碎的那一层
  这个题其实是考察算法里的__动态规划的问题__，有兴趣的可以在网上找找答案