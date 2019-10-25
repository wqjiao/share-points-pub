## 创建页面

在pages目录下创建文件夹

config/route.config.js 增加路由配置

## CSS/LESS

使用的CSS modules写法

## jwt集成

fetch配置（utils/request.js (isomorphic-fetch)）

1）在headers中配置Authorization字段，用于统一校验登录状态。

2）在headers中配置post请求传参格式application/x-www-form-urlencoded

```javascript
import {stringify} from 'qs';

function postData(url, data) {
  // Default options are marked with *
  return fetch(url, {
    body: stringify(data), // must match 'Content-Type' header
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    headers: {
        'Authorization': 'xxxxxxxxxx',
        'Content-Type': 'application/x-www-form-urlencoded; charset=utf-8',
    },
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
  })
  .then(response => response.json()) // parses response to JSON
}
```

## Menu权限控制

src\models\menu.js 

menuData为服务器获取，控制只显示哪些路由。

menuData会更新route中的auth字段，控制该路由页面中的具体页面元素是否可用。

## 全局样式


## 接口形式
```json
{
    "code":200,
    "msg":"success",
    "data" : []
}
```
## 按需加载


## refs不能使用

## 注释规范

保证类和函数级别的注释

## 单个文件不能超过500行

## history.push()不要携带数据，刷新的时候会丢失

## 布局

采用Ant.design提供的Grid布局方式，需要兼容sm和md两种屏幕。

## 公共组件应该是不涉及业务功能的组件。 

## 环境变量
测试和线上打包，如果指定域名需要使用config.js中的define功能处理。目前增加了apiUrl来控制接口请求的地址。

## DVA（Redux、redux-saga)
[DvaJS--指南](https://dvajs.com/guide/)
[DvaJS--API](https://dvajs.com/api/)
[DvaJS--知识地图](https://dvajs.com/knowledgemap)

```
  |---- put (action)
  |<--reducer--| 
Models -- call --> Services(fetch)
  ^                   |
  |                   |
  |<------yield-------|
```

### Javascript 语言
* 变量声明
* 箭头函数
* 模块 Import 和 Export
* ES6 对象和数组
* Promises -- fetch/new Promise
* Generators
  dva 的 effects 是通过 generator 组织的，Generator 返回的是迭代器，通过 yield 关键字实现暂停功能。
  这是一个典型的 dva effect，通过 yield 把异步逻辑通过同步的方式组织起来。
  ```js
  app.model({
    namespace: 'todos',
    effects: {
      *addRemote({ payload: todo }, { put, call }) {
        yield call(addTodo, todo);
        yield put({ type: 'add', payload: todo });
      },
    },
  });
  ```

css modules
  ```css
  .title {
    color: red;
  }
  :global(.title) {
    color: green;
  }
  ```
  使用：
  ```js
  <App className={styles.title} /> // red
  <App className="title" />        // green
  ```
### Reducer

```js
app.model({
  namespace: 'todos',
  state: [],
  reducers: {
    add(state, { payload: todo }) {
      return state.concat(todo);
    },
    remove(state, { payload: id }) {
      return state.filter(todo => todo.id !== id);
    },
    update(state, { payload: updatedTodo }) {
      return state.map(todo => {
        if (todo.id === updatedTodo.id) {
          return { ...todo, ...updatedTodo };
        } else {
          return todo;
        }
      });
    },
  },
};
```

注意: `state` 中的数据参数，尽量避免嵌套太多层

### Effect

```js
app.model({
  namespace: 'todos',
  effects: {
    *addRemote({ payload: todo }, { put, call }) {
      yield call(addTodo, todo);
      yield put({ type: 'add', payload: todo });
    },
  },
});
```

* `yield call(fetch, '/todos')` 用于调用异步逻辑，支持 promise 
* `yield put({ type: 'todos/add', payload: 'Learn Dva' })` 用于触发 action
* `yield select(state => state.todos)` 用于从 `state` 中获取数据

## umi
[Umi--指南](https://umijs.org/zh/guide/)
[Umi--配置](https://umijs.org/zh/config/)
[Umi--API](https://umijs.org/zh/api/)
Router，环境区分，按需加载，Mock，部署

* router.push(path) 推一个新的页面到 history 里
* router.replace(path) 替换当前页面，参数和 router.push() 相同
* router.go(n) 往前或往后跳指定页数 router.go(2) || router.go(-1)
* router.goBack() 后退一页

## react router
[react-router](https://reacttraining.com/react-router/web/api/NavLink)

## 提交代码描述

add/feat 新增某需求
remove/delete 删除某需求代码 || 文件
chore 修改一些零星事务
bugfix/fix 修改某个bug

### model 存放数据
* 1.详情页中获取的数据存放在 `model` 中时，下次进入页面更新当前参数数据，需要先做一次数据清除 --> 防止下次进入页面时，当前参数无值，保留上次进入页面带入的值(即使数据已更新);
`componentDidMount` 中 fetch 数据，在 `componentWillUnmount` 中 clear 数据
    ```js
    // 清空 model--state 数据
    clear() {
        return {
            userBy: [],
            data: [],
            infoData: {
                optType: '',
                customer: {},
                customerNatural: {},
            },
            userDicts: {},
            isMarital: true,
        };
    },
    ```

### Bizcharts 中图表坐标两端空白设置

* 提示信息 替换

  - 单挑折线提示
  ```js
  const tooltip = [
    `${x}*${y}`,
    (x, y) => {
      return {
        title: x,
        name: name,
        value: y,
      };
    },
  ];
    ```

  - 多条折线提示

  ```js
  const tooltip1=[`${x}*key*value`, function(x, key, value) {
    return {
      title: x,
      name: forMap[key],
      value: `${value}%`
    };
  }]
  const tooltip2=[`${x}*key*value`, function(x, key, value) {
    return {
      title: x,
      name: forMap[key],
      value: `${value}%`
    };
  }]
  ```

* 自定义 toolTip
htmlContent
```js
<Tooltip useHtml htmlContent={(title, items) => {return '<div><ul><li>.....</li></ul></div>'}}>
```

* 图例参数自定义替换

```js
// 图例参数替换
const forMap = {
    passRate: '通过率',
    rejectRate: '拒绝率',
}
<Legend itemFormatter={item => forMap[item]} />
```

## 圆饼图

```js
const salesPieData = [
  {
    x: '家用电器',
    y: 4544,
  }, {
    x: '食用酒水',
    y: 3321,
  }, {
    x: '个护健康',
    y: 3113,
  }, {
    x: '服饰箱包',
    y: 2341,
  }, {
    x: '母婴产品',
    y: 1231,
  }, {
    x: '其他',
    y: 1231,
  },
];
<Pie
  hasLegend // 右侧图例说明
  title="标题" // 图表标题
  subTitle="子标题" // 图表中央子标题
  total={() => <Yuan>{salesPieData.reduce((pre, now) => now.y + pre, 0)}</Yuan>} // 图表中央的总数
  data={salesPieData} // 图标数据
  valueFormat={value => <Yuan>{value}</Yuan>} // 显示值的格式化函数
  height={270} // 图表高度
  lineWidth={4} // canvas 的线宽
/>
```