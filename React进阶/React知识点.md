## React知识点

### 1. React 中的 Fragment  组件作用以及用法

- 作用： 因为react 中 render渲染函数需要一个返回值 返回要渲染的组件或者元素 当组件过多的时候 通常会在外层包裹一个div
但是包一个div结构就多了一层 所以React官方提供了一个 Fragment(片段组件) 用来包裹我们的组件 并且不会多出一层 其实原理类似于Vue中的 <template></template> 组件

- 实际项目中用法如下
```jsx
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

```jsx
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

```jsx
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

```jsx
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

```jsx
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

```jsx
import { Provider } from 'mobx-react';

ReactDOM.render(
  <Provider {...stores}>
    ...
  </Provider>,
  document.getElementById('root')
);
```

2. 在根组件 导入状态集合对象 并注入到 Provider 容器中

```jsx
import * as stores from './stores/index';
<Provider {...stores}>
  ...
</Provider>
```

3. 定义各类 状态管理的文件 然后统一融合到 状态集合对象里面 
- 单个状态管理文件 WebConfig.js
```jsx
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


### 7. React 中 定义组件的2种方式 函数组件 和 普通组件

#### 7.1 普通组件 也可以通过TS的泛型定义组件的 props的类型 

```jsx
import React, { Component } from 'react';
interface IProps {
  group?: any;
}
class ArchiveGroup extends Component<IProps> {
  state = {
   
  };
  componentDidMount() {
    // 使用自定义的props的group
    console.log(this.props.group)
  }
  render() {
    return (
      <div>
      组件元素内容
      </div>
    )
  }
}
```

- 普通组件的特点: 保留React组件的基本功能 state 生命 周期 render 渲染函数等等

#### 7.2 函数组件 也可以通过TS的泛型定义组件的 props的类型 

```jsx
import React from 'react';
interface IProps {
  template?: any;
}
const ArchiveNav: React.FC<IProps> = ({ template}) => {
  console.log(template)
  return (
    <div>
      组件元素内容
    </div>
  )
}
```

- 函数组件的方式特点: 
  类似于函数一样 写法比较简洁 然后 也可以定义组件的props类型 通过函数传参的方式 使用 对应的 props 属性
也不需要写render函数 直接 return 返回组件的内容
- 函数组件的方式缺点: 
  没有组件的生命周期 所以需要用到生命周期的情况要使用普通组件



### 8. Redux配合 reduxjs/toolkit 状态管理

#### 8.1  创建需要管理的状态的模块 Reducer

- Reducer的含义和作用 ： reducer是一种高阶函数 他的作用就是用来管理 状态的 state 和 action 进行一些操作返回新的state 也可以理解为state 和 action的容器)

- 创建步骤
1. 首先引入 reduxjs的工具包 reduxjs/toolkit
```js
  import { createAsyncThunk, createSlice } from '@reduxjs/toolkit';
```
2. 定义当前状态模块 需要管理的状态属性
```js
  const initialState = {
      属性:默认值
  };
```
3. 定义一些异步请求接口的函数
```jsx
export const getAreaData = createAsyncThunk('area/getAreaData', async () => {
  return (await apiFetch(api.getAreaList)) as any;
});
```
4. 创建 当前状态模块
使用reduxjs/toolkit 工具包提供的createSlice 函数可以创建状态模块的对象
函数需要传入参数
  1. name 模块名称
  2. 当前状态模块 初始化属性对象
  3. reducer 函数
  4. extraReducers 额外的reducer函数

```jsx
    const archive = createSlice({
    name: 'archive',
    initialState,
    reducers: {    
      setState(state, action) {
        // 这里面可以做一些数据处理然后更新状态 action.payload 可以获取到当前调用这个函数传递过来的参数值
        state = Object.assign(state, action.payload);
      },
    },
    extraReducers: (builder) => {
      // 在扩展 reducer函数 里面的参数 builder 是一个 reducers映射对象的容器
      // getArchiveData 是当前定义的异步数据请求函数 通过 addCase 当用到此数据的时候会去调用对应接口
      // 会将异步返回的数据 返回到action.payload中 state是当前的状态
      builder.addCase(getArchiveData.fulfilled, (state, action) => {     
        const data = action.payload;
        // 可以对数据做一些处理然后赋值到状态状态对象的属性上
        state.template = data.template;
      });
    
    },
  });

```

5. 导出当前 状态模块的reducer 和 模块里面的action动作

```jsx
export default area.reducer;
export const areaActions = area.actions;
```

#### 8.2 然后将每个状态小模块在 整个状态对象中统一打包引入

1.  引入  reduxjs/toolkit 工具  和各个模块
```jsx
import { configureStore, combineReducers } from '@reduxjs/toolkit';
import user from './userReducer';
import archive from './archiveReducer';
...
```
2. 创建根Reducer 把所有小模块的Reducer统一合并到一个 根Reducer中
```jsx
const rootReducer = combineReducers({
  user,
  archive
  ...
});

```
3. 然后将 根Reducer 作为根状态RootState导出

```jsx
export type RootState = ReturnType<typeof rootReducer>;
```

4. 然后创建状态对象实例对象 和 分发对象  并导出

```jsx
const store = configureStore({
  reducer: rootReducer,
});
export type AppDispatch = typeof store.dispatch;

export default store;
```

#### 8.3 在组件中使用状态

1. 引入 redux 状态到props的映射器 分发对象  根状态 和需要使用到的模块

```jsx
import { connect } from 'react-redux';
import { AppDispatch, RootState } from '../../reducers';
import { getArchiveData } from '../../reducers/archiveReducer';
```
2. 将AppDispatch 分发对象 映射到当前组件 props中

1. 定义当前组件 类型接口
``` jsx
interface ArchiveProps {
  dispatch: AppDispatch;
  companyLogo: string; // 公司logo
  companyName: string; // 公司名称
  archive: any;
}
```

2. 创建当前页面组件并且使用 当前类型接口
```jsx
 class Archive extends Component<ArchiveProps>
```

3. 当组件渲染完成 后调用异步请求函数 执行当前 状态模块里面的异步请求函数

```jsx
  componentDidMount() {
    this.fetchData();
  }

  async fetchData() {
    try {
      await Promise.all([this.props.dispatch(getArchiveData())]);
    } catch (e) {}
  }
```

4. 创建状态到props的映射器 将 当前根状态里面的对应状态属性 映射到当前组件实例的props中

```jsx
const mapStateToProps = (state: RootState) => ({
  archive: state.archive,
});

export default connect(mapStateToProps)(Archive);

```

5. 然后在组件中的render函数里面 使用this.props. 对应的熟悉就能获取到state中对应的熟悉 
```jsx
console.log( this.props.archive)
const { template } = this.props.archive;// 可以直接使用 也可以通过结构赋值赋值取出到变量中
```
