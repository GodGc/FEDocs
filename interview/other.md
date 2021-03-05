> [面试注意事项](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651235882&idx=2&sn=346a3e9d351b8815d310e5f118719cca&chksm=bd497fae8a3ef6b87920385507804d5c20246051bacf8d9a149e600d8bf5e5c0987ce37e66cd&mpshare=1&scene=1&srcid=&sharer_sharetime=1583535813224&sharer_shareid=0b94c73df48513ed6d090a244e519f53&key=324e443932aeb1ae9c4deb728d1653d6c9e9eb5f60b8cffa095e4548873b0bbe1b6d58184298b76f27714cd1133ff9a205fec0ccf35ed2b8017d070e2089cfbe74667a750d4cd2ac1f30e5253df4168c&ascene=1&uin=NjM4OTgxNjAx&devicetype=Windows+10&version=62080079&lang=zh_CN&exportkey=Ay4Rs8U%2FepT5WxS5SnmPhy4%3D&pass_ticket=D60bwsqfiqlKytevV5nIaAKOFSNlAfUUt42rTxJt2%2B9Wdlua8KxPFpBdC29Jjlgs)

# 上海头条广告

1、mock api
为什么去做？

- 代替口头约定接口方案
- 提升效率工作效率
- 技术保障
    对接 yapi，实现部署
- 完成项目部署全流程
- 修复测试接口导出报告 bug
- 登录 LDAP  plugin  开发

2、Hybird  VS H5

- 宿主环境区别
- 唤起原生 APP 能力、
- 如何唤起分享？

3、sdk.js   - JSBridge

- JSBridge
      - 通信是双向的。
      - webview 提供 API
- 如何使用

4、前端团队
 - 四套脚手架
    - M  站   JS TS
    - PC JS TS

- 业务代码组件库

- 脚手架价值
  - 帮助经验不足的同学快速启动项目
  - 技术沉淀
  - TS 规范代码
  - ESlint  约束代码
  - 项目解构
- 不太好的点：
  - 不利于非开发脚手架同学的技术沉淀
- 解决方案
  - 周三团队技术分享。
- 脚手架升级
  - 自定义文件名

5、webpack

- plugins
- happy webpack
- less  loader 有几个？

6、Fetch  和  Ajax(xhr)  的区别是什么？
baidu.com get json
实现一个 Ajax

7、JS  函数节流和防抖

# binance

## 一面

- 如果不用 includes，那么如何查找字符串中的某个字符？
  - indexOf
  - 正则匹配，（如何编写一个匹配手机号的正则）
- forEach 和 map 的区别
- every 和 any 的区别、它们可以手动打断吗？
- 如何合并 2 个数组
  - concat
  - 其他呢？ES6 结构赋值
- 如何查找数组中某个元素出现了多少次
- webpack 分模块打包、多入口打包/多页面打包？
- webpack 提取多页面（项目）都引用的库 vendor，
  - optimization-代码分割

```javascript
        optimization: {
            splitChunks: {
              cacheGroups: {
                // 打包业务中公共代码  priority低，后提取
                // 只提取common和styles目录下的文件，打包成common.js和common.css
                // 否则会把任意目录下的文件也打包进common里，造成污染
                common: {
                      name: "common",
                      chunks: "initial",
                      test: /[\\/]src[\\/](common|styles)/,
                      minSize: 1,
                      priority: 0,
                      minChunks: 3,
                },
                // 打包node_modules中的文件 priority更高，先提取
                vendor: {
                      name: "vendor",
                      test: /[\\/]node_modules[\\/]/,
                      chunks: "initial",
                      priority: 10,
                      minChunks: 2,
                }
              }
            },
            runtimeChunk: { name: 'manifest' } // 运行时代码
          },
```

- react 15 的 diff 算法，key 是干什么的
- react 16
  - 常用 hook：useCallback、useEffect、useMemo...
  - 如何在函数组件中发起网络请求，在哪个生命周期？
  - SSR 渲染，react 的自带 api、renderToString？
  - setState 的时候发生了什么？
- redux 是什么，怎么用，常用 api ,reduce?
- react pureComponent 和 class component 的区别
- 输入 url 到页面出现都发生了什么
- 如果没有联网，那么还会发起请求吗？
  - 不会，浏览器的 network 线程会判断是否联网
- CSRF 攻击是什么？
  - 如何防御？
  - 如何设置 cookie 只能在线读取？http only
- git 的常见命令
  - 如果在从 a 分支创建的 b 分支有 1、2、3 功能 commit，如何让 a 只有 2 功能？精准移动 commit
  - git reset hard 和 soft 的区别
    - hard 物理删除
    - 会保存工作区和暂存区

> Hooks 官方文档：
> [https://zh-hans.reactjs.org/docs/hooks-intro.html](https://zh-hans.reactjs.org/docs/hooks-intro.html)
> 10 分钟教你手写 8 个常用的自定义 hooks
> [https://juejin.im/post/5e57d0dfe51d4526ce6147f2?utm_source=gold_browser_extension#heading-5](https://juejin.im/post/5e57d0dfe51d4526ce6147f2?utm_source=gold_browser_extension#heading-5)

## 二面

- 写一个 modal box 组件
  - 参考：[https://www.jianshu.com/p/810e8104f72f](https://www.jianshu.com/p/810e8104f72f)
  - 如果调用者有 overflow：hidden，如何确保 modal 可以正常出现？
    - 插入到 body 下面，如何操作? appendChild 和 react createPortal

```javascript
import React, { Component } from "react";
import ReactDOM from "react-dom";
import "./Portal.css";

export default function Portal(WrappedComponent) {
    return class extends Component {
        constructor(props) {
            super(props);
            //直接插入dom节点到body下
            if (!this.node) {
                this.node = document.createElement("div");

                document.body.appendChild(this.node);
            }
        }
        //组件即将卸载时候删除dom节点
        componentWillUnmount() {
            this.node && this.node.remove();
        }
        //渲染内容
        renderContent() {
            return (
                <div>
                    <div className="portal-bgc" onClick={this.props.hide} />
                    {/*给WrappedComponent传递props*/}
                    <WrappedComponent {...this.props} />
                </div>
            );
        }

        render() {
            const { visible } = this.props;
            //visible控制显示/隐藏
            if (visible) return this.node && ReactDOM.createPortal(this.renderContent(), this.node);
            else return null;
        }
    };
}
```

- 如何全屏居中
- 相对比其他面试者，自身有什么优势？
  - 兴趣驱动
  - 英语
  - 思维
  - 沟通

## 三面

1. 讲一下技术背景，不包含业务（主要讲一下技术栈）
2. react+redux
    - 点击页面一个按钮改变一个 redux 中的值，页面中 3 个组件进行了改变，请问这个数据是如何流动并且触发到这 3 个组件上的，对应的 api 需要讲出 以及 数据流
3. react 中长列表定时推送一条新数据插入到最顶上，如果做到性能优化，保证浏览器不卡死
    - 这个点参照长列表渲染（[长列表优化](http://www.godrry.com/FEDocs/#/javascript/ES5#react%E9%95%BF%E5%88%97%E8%A1%A8%E4%BC%98%E5%8C%96)）
4. webpack 中 有 ABCD 四个依赖（js），如何在打包的时候把 ABC 打包到一个文件里，D 打包在另外一个文件里
5. webpack 如果缓存某个库，以达到下次打包时加快打包速度
