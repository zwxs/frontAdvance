## React知识点

### 1. React 中的 Fragment  组件作用以及用法

- 作用： 因为react 中 render渲染函数需要一个返回值 返回要渲染的组件或者元素 当组件过多的时候 通常会在外层包裹一个div
但是包一个div结构就多了一层 所以React官方提供了一个 Fragment(片段组件) 用来包裹我们的组件 并且不会多出一层 其实原理类似于Vue中的 <template></template> 组件
 
- 实际项目中用法如下
```js
    import React, { Component, Fragment } from 'react';
    <Fragment>
    {developer.welcome && <h1 className={styles.welcome}>{developer.welcome}</h1>}
    <div style={{ textAlign: 'center' }}>
        <HelperBlock linkClass={styles.linkClass} itemWidth={235} txtClass={styles.txtClass} />
    </div>
    </Fragment>
```

### 2. React 中的 classnames 组件 作用及用法

- 作用: 在react 中 clasName属性添加类名 只能添加单个类名 如果要添加多个类名需要手动 使用空格 或者 拼接字符串中间加空格的方式 添加 比较麻烦 classnames 组件的作用就是可以通过一个函数把你要的类名都作为函数参数传递然后帮你拼接好

- 实际项目中用法如下

```js
    import classnames from 'classnames';
    <div key={i} className={classes.item} style={{ width: itemWidth }}>
        <Link className={classnames(classes.link, linkClass)}>
        </Link>
    </div>
```

### 3. React中 使用全局和局部类名样式 依赖 css-loader 插件

- 作用: 由于 默认情况下 CSS 会将所有的类名暴露到全局的选择器作用域中 像类名重复了就容易引起样式冲突 通常为了避免此问题
要使用局部作用域的样式 vue 中可以通过 style标签的 scoped 属性来标识局部和全局样式 
在 React中没有此内置的功能 就只能通过 第三方的方式 例如css-loader 插件实现样式私有化

- 实际项目中用法如下

```less
//  第一种方式是 在 类名前面加上 :local 类似于 后代选择器
:local .welcome {
  margin: 39px 0 36px 0;
  min-height: 54px;
  font-weight: normal;
  font-size: 36px;
  color: #000000;
  text-align: center;
}
// 如果有很多类名都需要 私有化可以 使用less scss的嵌套方式 把:local 写在最外面
:local {
  .linkClass {
    width: 163px;
    height: 172px;
    padding-top: 22px !important;
  }
  .txtClass{
    margin-top: 18px;
    font-size: 16px;
  }
}




```
- 然后还需要在webpack配置中配置css-loader
```js
  {
    test: /\.less$/,
    use: [
      "style-loader",
      {
        loader: "css-loader",
        options: {
          importLoaders: 1,
          localIdentName: "[name]__[local]--[hash:base64:5]"
        }
      },     
    ]
  },
```

- 最后项目运行阶段会把 less scss 等代码编译成css 然后把类名变成和项目文件夹相关的一个特殊的类名 从而达成一个样式私有化效果

```css
.navblock__welcome--3wrQ6 {
  margin: 39px 0 36px 0;
  min-height: 54px;
  font-weight: normal;
  font-size: 36px;
  color: #000000;
  text-align: center;
}
.navblock__linkClass--2YmXM {
  width: 163px;
  height: 172px;
  padding-top: 22px !important;
}
.navblock__txtClass--2OWqi {
  margin-top: 18px;
  font-size: 16px;
}

```



### 4. 当view 层某个组件过于复杂的 结构层次很多的时候尽可能抽离成多个小组件 提高组件复用性和组件代码的可读性

```js
// 创建 视图里面需要的一个小区块组件
const Section = props => {
   return (
    <div className={styles.section}>
      <div className={styles.item}>
        <Link to={link} className={styles.tit}>
          {props.title}
        </Link>
        <div style={{ padding: '10px 4px 0 4px' }}>
          ...
        </div>
        {button}
      </div>
    </div>
  );
};

```

```html
  <div className={styles.noticewrap}>
    <style>{styleString}</style>
    <div className={styles.title}>
      ...
    </div>
    <div className={styles.list}>
      {list.map(item => (
        <!-- 使用当前创建的小区块组件 -->
        <Section key={item.id} {...item} bidNoticeType={this.props.webConfig.setting.bidNoticeType} />
      ))}
    </div>
  </div>
```

### 5. 若视图中有遇到逻辑判断等操作 提出来到JS中处理 视图只尽可能做渲染 不做逻辑判断

```js
  // 根据不同类型 判断跳转到不同的链接
  let link = `/${getTenantCode()}/bid-notice/detail?post_id=${props.id}&bid_id=${props.bid_id}`;
  if (props.bidNoticeType) {
    link = `/${getTenantCode()}/bid-advance/detail?advance_id=${props.advance_id}&bid_id=${props.bid_id}`;
  }
  // 根据不同的状态 判断展示不同的按钮
  let button = null;
  if (props.has_replay) {
    button = <span className={`${styles.end}`}>已报名</span>;
  } else if (props.is_end) {
    button = <span className={`${styles.end}`}>已截止</span>;
  } else {
    button = (
      <Link to={link} className={`${styles.btn} color-primary`}>
        报名
      </Link>
    );
  }
```

```html
<div className={styles.section}>
  <div className={styles.item}>
    <Link to={link} className={styles.tit}>
      {props.title}
    </Link>
    
    {button}
  </div>
</div>
```

### 6. React mobx-react 状态管理

- 作用: 存储React 中 用于 全局保存  或者 组件之间通讯需要的数据 (状态管理) 类似于 readux 和 vuex

- 用法流程：
  1. 在项目根组件 定义一个状态管理容器 并将所有状态集合对象 注入到 状态管理容器中
  2. 定义状态管理的配置文件 并引入到状态集合对象里面
  3. 在各自状态管理配置文件里面 定义 状态 并 定义或者 通过请求 配置好状态的数据
  4. 在需要用到全局状态的组件引入 和 通过对应的函数调用状态
- 流程代码:

1. 导入mobox容器 并在根组件中使用

```js
import { Provider } from 'mobx-react';

ReactDOM.render(
  <Provider {...stores}>
    ...
  </Provider>,
  document.getElementById('root')
);
```

2. 在根组件 导入状态集合对象 并注入到 Provider 容器中

```js
import * as stores from './stores/index';
<Provider {...stores}>
  ...
</Provider>
```

3. 定义各类 状态管理的文件 然后统一融合到 状态集合对象里面 
- 单个状态管理文件 WebConfig.js
```js  
// 定义状态状态类
class WebConfig {
  // @observable 装饰器 类似于 let const 定义状态类里面的同步的全局变量 值可以是任意的JS类型的数据 区别就是 observable 的属性值在其变化的时候 mobx 会自动追踪并作出响应 就是当状态变化的时候 在组件中能够及时获取到变化后的状态
  @observable primaryTheme = {};
  @observable navbarColor = '#243043';


  // @action  动作 对于 store 对象中可观察的属性值，在他们改变的时候则会触发观察监听的函数 通常就是定义一些需要异步请求的数据 然后通过函数的方式访问
  @action fetchToken() {
    apiFetch(api.getToken).then(data => {
      setToken(data.token);
    });
  }
}
// 导出当前状态类的实例对象 
export default new WebConfig();
```
-  状态集合对象文件 index.js
```js 

import area from './area';
import webConfig from './webConfig';
export {
  area,
  webConfig,
  ...
};

```

4. 在子组件中引入需要用到的状态 并使用

```js
// 导入  inject 和  observer 组件
import { inject, observer } from 'mobx-react';
// @inject 注入(加载)状态集合对象 中的单个状态的实例
@inject('webConfig')
// observer:将你的组件变成响应式组件。就是数据改变时候可以出发重新的渲染
@observer 
// this.props.webConfig.primaryColor:这个就是如何使用数据源中的值.就和父子组件传参一样  使用 this.props.状态实例对象.里面的状态属性 取值
let primary = this.props.webConfig.primaryColor;
```

## 前端项目开发流程

### 1. 代码规范和格式化

- 首先项目代码格式化需使用 prettier 格式化  
    1. 使用插件 npm run format  格式化
    2. 使用vscode的 Prettier - Code formatter 插件 然后把保存码格式化 选项改为当前插件的格式化方式
    
### 2. 项目分支开发

- 克隆项目 拉取master分支最新代码

- 新需求开发 需要创建新的开发分支 
    - 分支名称命名规范: 日期-当月迭代更新次数-租户名称[-功能]  
        1. 如果是基础标准代码的就使用 standard
            例如 20200417-sp4-standard-test 
        2. 如果是单个租户的 代码的就使用 租户简写  
            例如20200417-sp4-sjy-test
- 如果是项目原有bug修改 也创建新的开发分支
    - 分支名称命名规范: 日期-bug-租户名称[-功能]  如
        1. 如果是基础标准代码的就使用 standard
            例如 20200417-bug-standard-test 
        2. 如果是单个租户的代码的就使用 租户简写  
            例如20200417-bug-sjy-test
- 然后根据新功能需求写 代码 或者 根据bug文档去修改bug


### 3. 项目代码发布版本

- 代码写完后如果是测试需要把当前开发分支的代码提交 
    1. 如果是测试环境 需要把当前开发分支的代码合并到 origin/dev 分支上 
    2. 如果是镜像环境 需要把当前开发分支的代码合并到 origin/alpha 分支上
    3. 如果是正式环境 需要把当前开发分支的代码合并到 origin/master 分支上  
    - 注意: 
        切记都是从当前开发分支 去 直接合并到 dev 或者 alpha  或者 master 不能 把dev合并到alpha 或者 alpha合并到master
    - 原因： 
        可能并不是所有的测试 或者 alpha 的 代码都需要上线  可能会导致需要和不需要上线的功能都到master 导致代码的一些冲突回滚问题很难解决 所以都一定要从当前开发分支直接合并到对应要上的版本的分支

- 代码合并完成后在当前需要发布的分支上去执行发布命令
    1. 使用cmd 输入npm命令 npm run publish
    2. 使用vscode插件底部的 npm 脚本 选中当前项目找到对应的publish去点击发布

    3. 发布过程中可能会遇到一些问题
        - 未指定发布项目的 本地目录地址
            1. 在项目根目录 创建一个 .env.local 的配置文件
            2. 在文件中 写入当前发布项目的本地目录地址      
                CZHOME_PUBLIC_PATH=C:\Users\zhengw16\Desktop\workspace\czhome\public\czbms
            3. 如果项目在C盘 或者 没有完全取得读写权限可能会报错 需要设置项目读写权限或者放入其他盘
        - git版本问题 不同的git版本 提交成功后返回的log信息不一样
            在 新版本的git 返回成功日志 
            nothing to commit, working tree clean
            在 旧版本的git 返回成功日志
            nothing to commit, working directory clean
            此时解决方案
            1. 最好是吧git升级到最新版本 2.14 以上都可以 (推荐)
            2. 修改项目根目录的 czhome.js 里面的判断 把 nothing to commit, working tree clean 改成  nothing to commit, working directory clean
   
- 项目发布完成后 打开 jenkins 自动化处理项目工作台 https://tool-jenkins.mysre.cn/
    1. 找到当前发布的项目 czsaas-test-czhome-ycg 并进入
    2. 点击 左上角 Build with Parameters  配置项目的发布的参数
    3. 选择当前要发布项目的分支 origin/dev 或者  origin/alpha 或者 origin/master
    4. 后面打上发布的备注tag
    5. 点击开始构建 等待完成即可 打开项目地址预览发布效果
