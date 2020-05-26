### 什么是可配置化表单

1. 传统表单通常是由前端按照设计图 从上往下一个一个表单元素样式去写 表单的提示文字
     表单的验证 表单的值的处理等等都是在当前页面进行维护
2. 可配置化表单 是把项目可能遇到的所有表单类型 抽出成每一个表单组件 打包到一个表单对象里面 通过对象属性和值映射的方式映射出对应类型的表单 把每种类型的表单的提示 验证  值的处理统一先写好 通过后台的数据配置来动态控制表单的位置 提示 验证 值处理等等操作

### 可配置化表单的好处

1. 解决开发过程中表单的提示 验证 值的处理 等等经常容易变化的东西 通过后台修改数据来变化 而不是每次都前端去修改页面 修改代码 从而有时候突然要这个表单 有时候又不要这个表单 有时候需要长度限制 有时候又不需要长度限制等等一些需求变更导致的同一个功能点反复开发的痛点

2. 提高维护效率 后期如果有新增一些其他的表单 或者 删除一些表单时候只需要调整后台返回数据即可 把一个功能点的维护成本从1day 到 1h  1h 到 1min

3. 可以把表单界面 和 数据格式 分开统一处理 把代码的模块分离便于开发和维护

### 可配置化表单的要求和核心点

1. 可配置化表单 需要后端的(强)配合 首先前端开发好可能出现的所有表单的类型的组件 把每种类型的表单可能需要用到的数据格式 数据内容告诉后端 去设计对应的表结构 字段 值 
2. 前端根据后端返回的表单数据结构进行统一渲染 并且把对应的值一对一的传参 判断处理 

### 可配置化表单的适用场景

1. 虽然可配置表单的好处很多但是并不是所有地方都需要他 比如登录 或者  修改密码 等一些表单元素类型 验证 提示 比较固定规范而且很少去修改的情况没必要适用可配置化表单
2. 通常在一些表单类型 验证 提示 容易变化的情况 例如用户注册 用户资料收集 等等表单比较多 而且表单容易发生变化的时候比较适合使用可配置化表单


### 可配置表单实现思路

1. 按照正常的页面表单组件开发 提取每个需要用到的表单组件 例如 input password email vcode number select checkbox 等等 按照项目所需要的需求开发好表单组件
2. 定义需要配置的内容 例如 label value rule 等需要变化的属性 作为组件的参数
3. 把所有表单组件统一打包在一个表单容器对象中 把表单的名称(类型) 和 表单的值 作为容器对象的属性和值 统一导出整个表单容器
4. 使用表单的时候引入表单容器组件 通过表单容器对象的 键->值 的映射出真正的表单组件 调用此组件传递需要的参数

### 核心点

1. 定义的组件要统一规则 和 共性 要尽可能保持同类型的参数一致 如果特殊表单组件所需参数不同可特殊处理
2. 对后端返回的数据的格式化处理 如果要实现表单的位置 顺序 都动态化处理 以及 表单的键 和 对应的值 一一对应需要对数据进行格式化处理


### 核心代码和开发流程

1. 定义好常用的表单组件 和 表单映射对象 把表单组件通过表单名称:组件 的格式 包装到表单映射对象里面

```jsx
import React from 'react';
const FormInput: React.FC<InputProps> = (props) => {
...具体的组件代码省略
};

export default FormInput;
import React from 'react';
const FormPassword: React.FC<InputProps> = (props) => {
...
};

export default FormPassword;
import React from 'react';
const FormInputSms: React.FC<InputProps> = (props) => {
...
};

export default FormInputSms;

import FormInput from '../../../forms/FormInput';
import FormPassword from '../../../forms/FormPassword';
import FormInputSms from '../../../forms/FormInputSms';

const formMap = {
  input: FormInput,
  password: FormPassword,
  sms_code: FormInputSms,
};

```

2. 首先调用后端的表单数据接口拿到表单数据
3. 把返回的表单数据的表单id 表单 值 表单数据 表单的错误提示等等属性 通过特定规则格式化出来 存储到redux之类的数据流管理容器里

```jsx
let template = data.template[0];
let tmpFields = template.fields[0].fields;
let fieldIds: any = [];
let values: any = {};
let fields: any = {};
let errors: any = {};
let keyObj: any = {};
tmpFields.forEach((field: any) => {
field.extra = field.extra ? field.extra : {};
let rule = field.extra.rule ? field.extra.rule : {};
let id = field.table_id;
rule = Object.assign(
    {
    type: field.widget,
    name: field.name_chn,
    required: field.required === '1',
    },
    rule
);
field.extra.rule = rule;
fieldIds.push(id);
fields[id] = field;
errors[id] = undefined;
values[id] =
    field.extra.defaultValue !== undefined
    ? field.extra.defaultValue
    : undefined;

if (field.extra.has_key) {
    keyObj[field.extra.has_key] = id;
}
});
state.keyObj = keyObj;
state.template = data.template as any;
state.errors = errors;
state.fields = fields;
state.values = values;
state.fieldIds = fieldIds;
```

4. 然后在需要用到表单的页面 把所有表单的id 映射到 当前组件的props 通过遍历所有表单的id 引入表单容器组件 传入当前每个表单id调用生成所需要的表单数量

```jsx
const mapStateToProps = (state: RootState) => ({
  fieldIds: state.register.fieldIds,
});

{fieldIds.map((item) => (
<RegisterLayout key={item} id={item} />
))}
```
5. 在表单容器组件里面根据当前表单id 去表单的值和数据里面映射出 当前表单id对应的表单的值和数据

```jsx
const mapStateToProps = (state: RootState, props: any) => {
  // @ts-ignore
  let field = state.register.fields[props.id];
  // @ts-ignore
  let value = state.register.values[props.id];
  // @ts-ignore
  let error = state.register.errors[props.id];
  error = error ? error : {};
  return {
    // @ts-ignore
    field,
    // @ts-ignore
    value,
    error,
  };
};
```

6. 然后在容器组件里面引入表单映射对象 通过 表单的名称(类型) 来映射对应的表单作为一个 Widget 动态表单组件
调用动态表单组件传递当前 表单的值 和其他的表单数据

```jsx
const { field, value, error } = this.props;
// @ts-ignore
const Widget = formMap[field.widget]
    ? // @ts-ignore
    // @ts-ignore
    formMap[field.widget]
    : formMap.input;

return (
    <Widget
    value={value}
    error={error}
    label={field.name_chn}
    changeValue={this.handleChange}
    field={field}
    />
);
```

7. 然后在对应的表单组件里面拿到当前表单的值和数据 进行针对性的表单处理
包括表单数据中的表单验证 表单提示 表单label 表单值 表单的处理函数等等进行表单功能处理

```jsx
  // let extraProps = defaultProp({}, props?.field?.extra, 'props');
  let extraProps = Object.assign({}, props?.field?.extra.rule);;
 
  if (props?.field?.extra.trim === true) {
    extraProps.onBlur = (e:any) => {
      props.changeValue(e.target.value.trim());
    };
  }
  const otherProps = omit(props, [
    'label',
    'error',
    'changeValue',
    'value',
    'field',
  ]);
  const { value = '', changeValue = () => {}, error = {} } = props;

  if (props.field && props.field.extra && props.field.extra.for_key) {
    extraProps.disabled = true;
  }
  return (
    <div
      className={classnames(formStyles.formWrap, {
        [formStyles.error]: error.status === 'error',
      })}
    >
      <span className={formStyles.label}>{props.label}</span>
      <input
        {...otherProps}
        {...extraProps}
        value={value}
        onChange={(e) => {
          changeValue(e.target.value);
        }}
        className={classnames(formStyles.input, otherProps.className, {
          [formStyles.disabled]: extraProps.disabled,
        })}
      />
    </div>
  );
```

