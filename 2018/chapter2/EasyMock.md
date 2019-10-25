# EasyMock Mock数据

## 用法：
    - 创建接口
    - 添加获取方式 get/post/put/delete/patch
    - 添加 url
    - 添加 json 数据
    - 预览接口/添加 Query Parameters 参数

## 数据模板基本使用

* 添加 min - max 数量的随机数据

'name|min-max': string/number/object/boolean/array/regexp

```javascript
{
    "string|1-10": "*"
    "number|1-100: 100
    "object|2-4": {
        "110000": "北京市",
        "120000": "天津市",
        "130000": "河北省",
        "140000": "山西省"
    }
    "boolean|1-2": true
    "array|1-10": [
        "Mock.js"
    ]
    "array|1-10": [
        {
            "name|+1": [
                "Hello",
                "Mock.js",
                "!"
            ]
        }
    ]
    'regexp|1-5': /\d{5,10}\-/
}
```

* 添加指定数量的数据

'name|count': string/object/array/regexp

```javascript
{
    'string|2': '***'
    'object|2': {
        "310000": "上海市",
        "320000": "江苏省",
        "330000": "浙江省",
        "340000": "安徽省"
    }
    "array|3": [
        "Mock.js"
    ]
    "array|1": [
        "AMD",
        "CMD",
        "UMD"
    ]
    'regexp|3': /\d{5,10}\-/
}
```

* 添加指定数据库中的随机一个数据

'name|+1': number/array

```javascript
    "number|+1": 2
    "array|+1": [
        "AMD",
        "CMD",
        "UMD"
    ]
```

* 添加上下文有关联性的数据 Function

'name': function

```javascript
{
    'foo': 'Syntax Demo',
    'name': function() {
        return this.foo
    }
}
```

* 正则表达式 RegExp

'name': regexp

```javascript
{
    'regexp': /[a-z][A-Z][0-9]/
    'regexp': /\w\W\s\S\d\D/
    'regexp': /\d{5,10}/
    'regexp|3': /\d{5,10}\-/
    'regexp|1-5': /\d{5,10}\-/
}
```

* 上下文关联性路径 Path

'name': '@/a @/b/c/d'

```javascript
// 绝对路径
{
    "foo": "Hello",
    "nested": {
        "a": {
            "b": {
                "c": "Mock.js"
            }
        }
    },
    "absolutePath": "@/foo @/nested/a/b/c"
}

// 相对路径
{
    "foo": "Hello",
    "nested": {
        "a": {
            "b": {
                "c": "Mock.js"
            }
        }
    },
    "relativePath": {
        "a": {
            "b": {
                "c": "@../../../foo @../../../nested/a/b/c"
            }
        }
    }
}
```

## easy-mock 部署到本地

easy-mock 官网的接口不稳定，时常挂，将 easymock 部署到本地服务器就不会出现不稳定的情况，随时可用

[Github](https://github.com/easy-mock/easy-mock/blob/dev/README.zh-CN.md)
[简书](https://www.jianshu.com/p/1650c2b9eec9)

## 网址

[EasyMock](https://www.easy-mock.com)
[Mockjs Examples](http://mockjs.com/examples.html)