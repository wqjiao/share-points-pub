# Antd 组件使用

## Antd Table

* 点击 Table 行高亮

```js
this.state = {
    rowId: '' // 点击的行 id值
}

/**
* @method 点击 Table Row
* @param [Object] record 行数据
* @param [Number] index 行索引
* @param event 行 事件
*/
onRowClick = (record, index, event) => {
    console.log('record, index, event:', record, index, event);
    this.setState({
        rowId: record.id
    });
}

// 点击该行指定对应id行高亮
setRowClassName = (record) => {
    return record.id === this.state.rowId ? 'click-row-highlight' : '';
}

<Table
    rowKey={record => record.id}
    pagination={pagOption} bordered={true} 
    columns={columns} dataSource={tableData}
    onRowClick={me.onRowClick} // 行 点击事件
    rowClassName={me.setRowClassName} // 行 类名
/>
```

```css
/*被选中的表格行的样式*/
.click-row-highlight {
    background: #eef6ff;
}
.ant-table-tbody > .click-row-highlight:hover > td {
    background: #eef6ff;
}
```

* 2、指定悬浮显示气泡框
```js
import { Popover } from 'antd'
const content = (
    <div>
        <p>2018-11-12 09:56:55 客服1 已电话沟通</p>
        <p>2018-11-13 09:56:55 客服222 已微信指导拍摄，左前纵梁、右后纵梁等</p>
    </div>
);
return (
    <Popover content={content}>{assign_at}</Popover>
);
```

## Antd DatePicker/MonthPicker/RangePicker

DatePicker、MonthPicker、RangePicker 部分 locale 是从 value 中读取，所以请先正确设置 moment 的 locale。
```js
import {DatePicker} from 'antd';
import moment from 'moment';
// 推荐在入口文件全局设置 locale
import 'moment/locale/zh-cn';
moment.locale('zh-cn');

<DatePicker defaultValue={moment('2015-01-01', 'YYYY-MM-DD')} />
```

或者 在控件中使用 `locale` 属性

```js
import {DatePicker} from 'antd';
import locale from 'antd/lib/date-picker/locale/zh_CN';

<DatePicker locale={'zh-cn'} defaultValue={moment('2015-01-01', 'YYYY-MM-DD')} />
```

注意: 若 日期选择器页面初始进入时有 value 值，则须添加以下代码，否则，初始进入加载的是英文状态的选择器

## Form 表单

### 纯文本，最后需要一并提交时，必须放在 getFieldDecorator

    ```js
    {form.getFieldDecorator('name', {
        initialValue: formData.name || '',
    })(<span>{formData.name}</span>)}
    ```

### 空间修改过程中需要手动添加 Error

* rules[].validator

```js
{form.getFieldDecorator('name', {
    initialValue: formData.name || '',
    rules: [{
        validator: (rule, value, callback) => {
            if (value.length > 0 && (value.length < 2 || !(value[0] + ''))) {
                callback('请输入相应数值');
            }
            if (value[0] > value[1]) {
                callback('下限数值不能大于上限数值');
            }
            callback();
        },
    }],
})(<Input placeholder="请输入" />)}
```

* form.setFields

```js
form.setFields({
    name: {
        value: '', // 该 name 对应的 value
        errors: [new Error('请重新输入证件号码')], // 需要添加的错误信息
    },
});
```

### 修改关联性控件 options.getValueFromEvent

`getValueFromEvent` 修改/返回 `onChange` 中的 value

```js
{form.getFieldDecorator('name', {
    initialValue: formData.name || '',
    getValueFromEvent: e => {
        let value = getIdentityValue(e);

        if (value.length === 18) {
            const types = getIdentityTypes(value);

            this.setState({
                formData: {
                    ...formData,
                    gender: types.sex,
                    birth: types.birth,
                },
            });
        }
        return value;
    },
})(<span>{formData.name}</span>)} 
```

### 重置表单数据

* form.resetFields([names: string[]])

重置一组输入控件的值，不传值，表示清空||重置所有组件

* form.setFieldsValue({[fieldName]: value })

设置一组输入控件的值 (注意：不要在 componentWillReceiveProps 内使用，否则会导致死循环)

### 表单中的时间控件

时间类组件的 value 类型为 moment 对象，所以在提交服务器前需要预处理。

```js
this.props.form.validateFields((err, fieldsValue) => {
    if (err) return;
    const values = {
        ...fieldsValue,
        // 时间类组件的 value 类型为 moment 对象，所以在提交服务器前需要预处理
        'date-picker': fieldsValue['date-picker'].format('YYYY-MM-DD'),
    }
    console.log('Received values of form: ', values);
});
```

### 报错

`index.js:1 Warning: `getFieldDecorator` will override `value`, so please don't set `value` directly and use `setFieldsValue` to set it.`

去掉 自定义FormItem组件中的defaultProps中默认的value值。