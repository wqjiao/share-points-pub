# 公共方法

## 1、手机号及身份证号码 隐藏设置
    ```js
    function setNumberStar (str, frontLen, endLen) { 
        var len = str.length - frontLen - endLen;
        var star = '';
        for (var i = 0; i < len; i ++) {
            star += '*';
        }
        return str.substring(0, frontLen) + star + str.substring(str.length - endLen);
    }
    ```

## 2、日期格式转换
    ```js
    function formatDate(time, format = 'YY-MM-DD hh:mm:ss') {
        var date = new Date(time);

        var year = date.getFullYear(), // 年
            month = date.getMonth()+1, // 月份 从0开始的
            day = date.getDate(), // 日
            hour = date.getHours(), // 小时
            min = date.getMinutes(), // 分钟
            sec = date.getSeconds(); // 秒

        // 前置 0, 开个长度为10的数组 格式为 00 01 02 03
        var preArr = Array.apply(null,Array(10)).map(function(elem, index) {
            return '0' + index;
        });

        var newTime = format.replace(/YY/g,year)
            .replace(/MM/g,preArr[month] || month)
            .replace(/DD/g,preArr[day] || day)
            .replace(/hh/g,preArr[hour] || hour)
            .replace(/mm/g,preArr[min] || min)
            .replace(/ss/g,preArr[sec] || sec);

        return newTime;
    }
    // formatDate(new Date().getTime());//2017-05-12 10:05:44
    // formatDate(new Date().getTime(),'YY年MM月DD日');//2017年05月12日
    // formatDate(new Date().getTime(),'今天是YY/MM/DD hh:mm:ss');//今天是2017/05/12 10:07:45
    ```

## 3、React -- Copy 按钮
    ```js
    import React from 'react';
    import {message} from 'antd';

    class CopyBtn extends React.Component {
        constructor(props) {
            super(props);
        }

        copyClick(domId) {
            if (!domId) { message.error('please press Ctrl/Cmd+C to copy') }

            var inp = document.getElementById(domId);

            if (inp && inp.select) {
                let defaultValue = inp.defaultValue
                inp.value = inp.defaultValue.replace(/\s/g, '').toString()
                inp.select()
                try {
                    document.execCommand('copy')
                    inp.value = defaultValue
                    inp.blur()
                    message.success('已复制')
                } catch (err) {
                    message.error('please press Ctrl/Cmd+C to copy')
                }
            }
        }

        render() {
            let me = this
            return (
                <div
                    className="copybtn"
                    style={this.props.style} 
                    onClick={this.copyClick.bind(me,this.props.domId)}
                >
                    复制
                </div>
            )
        }
    }
    export default CopyBtn;
    ```

## 4、替换字符串中指定位置元素
    ```js
    let stertIndex = me.formatDate(now).indexOf('-', 5);
    queryOptions['startValue'] = me.formatDate(now).substring(0, stertIndex + 1) + '01';
    ```

## 5、环形进度条组件-Vue
    ```
    <template>
        <!-- 评分环形进度条 -->
        <div class="progress-circle" @click="handleClick">
            <svg width="100%" height="100%" viewBox="0 0 100 100" version="1.1" xmlns="http://www.w3.org/2000/svg">
                <circle class="progress-background" cx="50" cy="50" r="50" fill="transparent"/>
                <circle
                    class="progress-bar"
                    cx="50"
                    cy="50"
                    r="50"
                    fill="transparent"
                    transform="matrix(0, -1, 1, 0, 0, 200)"
                    :stroke-dasharray="strokeDasharray"
                    id="cicle1"
                />
            </svg>
            <div class="point-box">
                <p class="point-num">{{ score }}</p>
                <p class="point-text">风险评分</p>
            </div>
            <div class="icon-question-mark"></div>
        </div>
    </template>

    <script>
        export default {
            name: 'circleProcess',
            props: {
                score: {
                    type: String,
                    default: 0
                },
                handleClick: {
                    type: Function,
                    default: () => {}
                }
            },
            data () {
                return {
                    strokeDasharray: "0 1069"
                }
            },
            mounted () {
                setTimeout(() => {
                    var score = this.score / 100,
                        perimeter = Math.PI * 2 * 50;
                    var circle = document.querySelectorAll("circle")[1];

                    this.isShow = true;
                    circle.setAttribute('stroke-dasharray', perimeter * score + " " + perimeter * (1- score));
                }, 200)
            }
        }
    </script>

    <style scoped lang="less">
        .progress-circle {
            position: relative;
            width: 2.266667rem;
            height: 2.266667rem;
        }
        circle {
            transform-origin: center;
            stroke-linecap: round;
            &.progress-background {
                stroke-width: 8px;
                stroke: rgba(255, 255, 255, 0.2);
                transform: scale(0.9);
            }
            &.progress-bar {
                stroke-width: 8px;
                transform: scale(0.9) rotate(-90deg);
                -webkit-transition: stroke-dasharray .25s ease;
                transition: stroke-dasharray .25s ease;
                stroke: #fff;
            }
        }
        .point-box {
            width: 100%;
            position: absolute;
            top: 46%;
            left: 0;
            text-align: center;
            transform:  translateY(-50%);
            color: #fff;
            line-height: 1;
            .point-num {
                font-size: .8rem; // 60px
                margin-bottom: .16rem; // 12px
            }
            .point-text {
                font-size: .32rem; // 24px
            }
        }
        .icon-question-mark {
            width: .32rem;
            height: .32rem; // 24px
            background: url(../../assets/online_loan/grade_wenhao@3x.png) no-repeat;
            background-size: 100% 100%;
            position: absolute;
            top: 0;
            right: -0.32rem;
        }
    </style>
    ```
    组件中调用 <circleProcess :score="score" :handleClick="handleClick"></circleProcess>

## 6、获取地址栏 url 中的某参数值
    ```js
    function _getUrlParam(name) {
        var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)");
        var r = window.location.search.substr(1).match(reg);
        if (r != null) return unescape(r[2]);
        return "";
    }
    ```

## 7、向 window 中添加自定义方法或参数
    ```js
    window.demoFunction = (function() {
        let arr = [1, 2, 3],
            name = 'wqjiao';
        let data = {
            arr,
            name
        };

        return data;
    }());

    console.log('window: ', window);
    ```
    注意： 可在全局添加，一遍该项目的所有页面都可以调用，否则仅限使用到该组件或页面的地方调用。

## 8、清空 react-router(history) 产生的多余的 session
    ```js
    // 删除 sessionStorage 中的所有数据
    sessionStorage.clear();
    // 或 建立公共方法
    function sessionStorage() {
        try {
            let sessionLength = window.sessionStorage.length;
            let sessionKeys = [];
            // 遍历key中包含@@History的项
            for (let i = 0; i < sessionLength; i ++) {
                if (window.sessionStorage.key(i).match('@@History')) {
                    sessionKeys.push(window.sessionStorage.key(i));
                }
            }
            // 删除该项session
            for (let j = 0; j < sessionKeys.length; j ++) {
                window.sessionStorage.removeItem(sessionKeys[j]);
            }
        } catch(err) {
            window.Raven && window.Raven.captureException(err);
        }
    }
    ```

## 9、正则表达式匹配

* 身份证号码
    ```js
    /**
     * @param {*} e
     * @returns 校验身份证号
     */
    export function getIdentityValue(e) {
        let value = e.target.value;
        const reg = /^\d{6}(18|19|([23]\d))\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$/;

        if (!reg.test(value)) {
            value = value.replace(/[\u4e00-\u9fa5|,]+/, ''); // 过滤中文
            value = value.replace(/[^\d{18}$)|(^\d{17}(\d|X|x]/, '');
        }
        return value;
    }
    ```

* 手机号码
    ```js
    /**
     * @param {*} e
     * @returns 校验手机号码
     */
    export function getPhoneValue(e) {
        let value = e.target.value;

        if (!/^1[3-9][0-9]{9}$/.test(value)) {
            value = value.replace(/[^0-9]/g, '');
        }
        return value;
    }
    ```

* 固定电话
    ```js
    /**
     * @param {*} e
     * @returns 校验固定电话--大陆
     */
    export function getFixedTelephone(e) {
        let value = e.target.value;

        if (!/^0\d{2,3}-\d{7,8}$/.test(value)) {
            value = value.replace(/[^0-9-]/g, '');
        }
        return value;
    }
    ```

* 替换掉非数字字符
    ```js
    /**
     * @param {*} value
     * @returns 可以输入小数的数字输入框
     */
    export function checkNumberEvent(value) {
        value = value + ''; // String(value)

        if (!/^\d+(\.\d+)?$/.test(value)) {
            value = value.replace(/[^0-9.]/g, '');
        }

        return value ? parseFloat(value) : '';
    }
    ```

* 替换掉汉字字符
    ```js
    /**
     * @param {*} e
     * @returns 替换掉汉字字符
     */
    export function getNotCharacterValue(e) {
        let value = e.target.value;

        if (/[\u4e00-\u9fa5]+/.test(value)) {
            value = value.replace(/[\u4e00-\u9fa5|,]+/, '');
        }
        return value;
    }
    ```

* 指定输入框转换数字(最多两位小数)
    ```js
    /**
    * @return {*} value
    * @description 指定输入框转换数字(最多两位小数)
    */
    export function limitDecimals(value) {
        const reg = /^(\d+)\.(\d\d).*$/;
        
        let bef = '',
        _value = String(value);

        // 需要限制用户输入的整数最大位数
        // 截取小数点前面正整数为7位
        if (_value.indexOf('.') != -1) {
        // if (_value.includes('.')) {
            const index = _value.indexOf('.');
            bef = _value.substring(0, index);
        } else {
            bef = _value;
        }
    
        // 限制输入7位
        if (bef && !/^(\d{1,7})$/.test(bef)) {
            value = bef.substring(0, 7);
        }
        
        if(typeof value === 'string') {
            return !isNaN(Number(value)) ? value.replace(reg, '$1.$2') : '';
        } else if (typeof value === 'number') {
            return !isNaN(value) ? String(value).replace(reg, '$1.$2') : '';
        } else {
            return '';
        }
    }
    ```

* 地址 URL 链接
    ```js
    export function getUrl(url) {
        let reg = /(http|ftp|https):\/\/[\w\-_]+(\.[\w\-_]+)+([\w\-\.,@?^=%&:/~\+#]*[\w\-\@?^=%&/~\+#])?/;
        
        return url && reg.test(url);
    }
    ```

* 日期 YYYY-MM-DD
    ```js
    export function getDate(v) {
        let reg = /^[1-9]\d{3}-(0[1-9]|1[0-2])-(0[1-9]|[1-2][0-9]|3[0-1])$/;
        
        return reg.test(v);
    }

## 10、获取当前日期 YYYY-MM-DD
    ```js
    function getDate() {
        var date = new Date();
        return date.getFullYear() + '-'
            + (date.getMonth() < 9 ? ('0' + (date.getMonth() + 1)) : (date.getMonth() + 1)) + '-'
            + (date.getDate() < 10 ? ('0' + date.getDate()) : date.getDate())
    }
    ```

## 11、日志输出函数 logger
    ```js
    // 环境变量
    const ENV = (function() {
        if (window.env == 'local') {
            return 'dev';
        } else {
            return window.env;
        }
    })();
    function Logger() {
        let logger = {};

        if (ENV === 'production') {
            logger.show = false;
        } else {
            logger.show = true;
        }

        ["log", "info", "warn", "error"].forEach(function(item) {
            var name = item;

            logger[name] = function () {
                try {
                    if (!window.console) return; // prevents errors on IE
                    if (logger.show) {// 如果要显示日志信息
                        console[name].apply(console, arguments);
                    }
                } catch(e) {}
            };
        });
        return logger;
    }
    ```

## 12、添加键盘事件

    ```js
    addEvents () {
        let me = this;
        // 监听键盘事件
        document.addEventListener('keydown',function(e){
            switch(e.keyCode){
                // enter
                case 13:
                    me._handleOrderPass();
                    break;
            }
        });
    }
    ```

## 13、排序算法 -- 快速排序

```js
let quickSort = function(arr) {

　　if (arr.length <= 1) { return arr; }

　　var pivotIndex = Math.floor(arr.length / 2);

　　var pivot = arr.splice(pivotIndex, 1)[0];

　　var left = [];

　　var right = [];

　　for (var i = 0; i < arr.length; i++){

　　　　if (arr[i] < pivot) {

　　　　　　left.push(arr[i]);

　　　　} else {

　　　　　　right.push(arr[i]);

　　　　}

　　}

　　return quickSort(left).concat([pivot], quickSort(right));

};

quickSort([21, 2, 5,89,56,33]); // [2, 5, 21, 33, 56, 89]
```

## 14.获取今天之前/后的 day 天的日期区间
    ```js
    /**
     * @return 今天前后 day 天的日期区间
     * @param [Number] day 日期间隔
     */
    function _getWeekRangeDate(day) {
        const time = new Date(new Date().toLocaleDateString()).getTime();
        const todayTime = new Date(time); // Fri Jan 18 2019 00:00:00 GMT+0800 (中国标准时间)
        // const todayTime = new Date(time + 24 * 60 * 60 * 1000 - 1); // Fri Jan 18 2019 23:59:59 GMT+0800 (中国标准时间)

        let todayDate = todayTime.getFullYear() + '-' + (todayTime.getMonth() + 1) + '-' + todayTime.getDate(); // time1表示当前时间
        let beforeTime = new Date(todayDate);
        beforeTime.setDate(todayTime.getDate() + day);
        let beforeDate = beforeTime.getFullYear() + '-' + (beforeTime.getMonth()+1) + '-' + beforeTime.getDate();

        return { beforeDate, todayDate};
    }

    let data = _getWeekRangeDate(-6);
    console.log(data.beforeDate, data.todayDate);
    ```

## 15.new Date() 对象转换
    ```js
    let date = new Date();

    date.constructor() // Wed Jan 23 2019 15:44:27 GMT+0800 (中国标准时间)
    date.getDate() // 23 月天
    date.getDay() // 3 周天
    date.getFullYear() // 2019 年
    date.getHours() // 15 时
    date.getMilliseconds()
    date.getMinutes()
    date.getMonth() // 0 月(需+1)
    date.getSeconds() // 42 秒
    date.getTime() // 1548229542855
    date.getTimezoneOffset()
    date.getUTCDate()
    date.getUTCDay()
    date.getUTCFullYear()
    date.getUTCHours()
    date.getUTCMilliseconds()
    date.getUTCMinutes()
    date.getUTCMonth()
    date.getUTCSeconds()
    date.getYear()
    date.setDate()
    date.setFullYear()
    date.setHours()
    date.setMilliseconds()
    date.setMinutes()
    date.setMonth()
    date.setSeconds()
    date.setTime()
    date.setUTCDate()
    date.setUTCFullYear()
    date.setUTCHours()
    date.setUTCMilliseconds()
    date.setUTCMinutes()
    date.setUTCMonth()
    date.setUTCSeconds()
    date.setYear()
    date.toDateString() // Wed Jan 23 2019
    date.toGMTString() // Wed, 23 Jan 2019 07:45:42 GMT
    date.toISOString() // 2019-01-23T07:45:42.855Z
    date.toJSON() // date.toJSON()
    date.toLocaleDateString() // 2019/1/23
    date.toLocaleString() // 2019/1/23 下午3:45:42
    date.toLocaleTimeString() // 下午3:45:42
    date.toString() // Wed Jan 23 2019 15:45:42 GMT+0800 (中国标准时间)
    date.toTimeString() /// 15:45:42 GMT+0800 (中国标准时间)
    date.toUTCString() // Wed, 23 Jan 2019 07:45:42 GMT
    date.valueOf() // 1548229542855
    date.Symbol(Symbol.toPrimitive)
    ```

## 16.根据身份证号码 value 获取相应类型

    ```js
    // 根据 身份证号: value 获取相应类型
    export function getIdentityTypes(value) {
        const province = value.substring(0, 2); // 省
        const city = value.substring(0, 4); // 市
        const area = value.substring(0, 6); // 区
        const year = value.substring(6, 10); // 年
        const month = value.substring(10, 12); // 月
        const day = value.substring(12, 14); // 日
        const sex = value.substring(16, 17); // 性别
        return {
            province,
            city,
            area,
            year,
            month,
            day,
            birth: `${year}-${month}-${day}`,
            sex: sex % 2 === 0 ? '女' : '男',
        };
    }

    // 调用该方法
    getValueFromEvent = e => {
        let value = getIdentityValue(e);
        const reg = /^\d{6}(18|19|([23]\d))\d{2}((0[1-9])|(10|11|12))(([0-2][1-9])|10|20|30|31)\d{3}[0-9Xx]$/;

        // 提取身份证数据前提： 18位、匹配正确
        if (value.length === 18 && reg.test(value)) {
            const types = getIdentityTypes(value);

            return {
                gender: types.sex,
                birth: types.birth,
            }
        }
        return value;
    }
    ```

## 17.组合式比较

* 不考虑其他，只要内容形态一致， 则认为数组或对象相等

    ```js
    let obj1 = {a: [1, '2'], b: 2},
        obj2 = {b: 2, a: [1, '2']},
        arr1 = [1 ,2, {b: 2, a: 1}, 9],
        arr2 = [1 ,2, {a: 1, b: 2}, 9];

    equal(obj1, obj2); // true
    equal(arr1, arr2); // true
    equal(obj1, arr1); // false
    ```
    注意： [1, 2] 不等于 [2, 1];

* `解决方案` -- 普通变量、数组/对象(递归调用，直到为普通变量)
    ```js
    // 提取变量类型
    function getType(obj) {
        // return Object.prototype.toString.call(array).slice(8, -1);
        return Object.prototype.toString.call(obj).replace(/\[object (\w+)\]/, "$1");
    }

    // 比较数组
    function equalArray(arr1, arr2, cb) {
        if (arr1.length != arr2.length) return false;

        return arr1.every(function(el, i) {
            if (cb) return cb(i);
            return el === arr2[i];
        });
    }

    // 比较对象
    function equalObject(obj1, obj2, cb) {
        let aProps = Object.getOwnPropertyNames(obj1),
            bProps = Object.getOwnPropertyNames(obj2);

        if (aProps.length !== bProps.length) return false;

        return aProps.every(function(props) {
            if (cb) cb(props);
            return obj1[props] === obj2[props];
        });
    }

    //比较任意类型
    function equal(a, b) {
        let typeA = getType(a),
            typeB = getType(b);

        // 类型
        if (typeA !== typeB) return false;

        // 数组 || 对象 || 普通变量
        if (typeA === 'Array') {
            return equalArray(a, b, function(index) {
                return equal(a[index], b[index]);
            });
        } else if (typeA === 'Object') {
            return equalObject(a, b, function(props) {
                return equal(a[props], b[props]);
            });
        } else {
            return a === b;
        }
    }
    ```

## 18.绑定/移除事件

点击区域非组件内部时，隐藏指定元素

    ```js
    componentWillUnmount() {
        let me = this;

        // 卸载 body 上绑定的指定事件
        document.body.removeEventListener('click', e => me.clickFunction(me, e), false);
    }

    // 点击该组件其他区域时，隐藏下拉框
    _onBlurHandler(me) {
        document.body.addEventListener('click', e => me.clickFunction(me, e), false);
    }

    // 监听 document.body 点击事件
    clickFunction = (me, e) => {
        // 针对不同浏览器的解决方案
        function matchesSelector(element, selector) {
            if(element.matches){
                return element.matches(selector);
            } else if(element.matchesSelector){
                return element.matchesSelector(selector);
            } else if(element.webkitMatchesSelector){
                return element.webkitMatchesSelector(selector);
            } else if(element.msMatchesSelector){
                return element.msMatchesSelector(selector);
            } else if(element.mozMatchesSelector){
                return element.mozMatchesSelector(selector);
            } else if(element.oMatchesSelector){
                return element.oMatchesSelector(selector);
            } else if(element.querySelectorAll){
                var matches = (element.document || element.ownerDocument).querySelectorAll(selector),
                    i = 0;
        
                while(matches[i] && matches[i] !== element) i++;
                return matches[i] ? true: false;
            }
            throw new Error('Your browser version is too old,please upgrade your browser');
        }

        // 匹配当前组件内的所有元素
        if (
            matchesSelector(
                e.target,
                '.incoming-widgets-location-cascader-index-locationCascader *'
            )
        ) {
            return;
        }

        // 判断 下拉框是否显示
        if (me.state.menuVisible) {
            me.setState({
                menuVisible: false,
            });
        }
    };
    ```

## 19.数组去重方法
    采用多种方式，实现数组去重，说明利弊

* 简单方式去重 -- 采用空数组空间
    ```js
    /*
     * 新建一新数组，遍历传入数组，值不在新数组就 push 进该新数组中
     * IE8 以下不支持数组的 indexOf 方法
    */
    function removeDuplicates(array) {
        var temp = []; //一个新的临时数组
        for(var i = 0; i < array.length; i++) {
            if(temp.indexOf(array[i]) === -1) {
                temp.push(array[i]);
            }
        }
        return temp;
    }

    var arr = [1, 2, 2, 4, 9, 6, 7, 5, 2, 3, 5, 6, 5];
    console.log(removeDuplicates(arr));
    ```

* 对象键值法去重
    ```js
    /*
     * 速度最快， 占空间最多（空间换时间）
     * 该方法执行的速度比其他任何方法都快， 就是占用的内存大一些。
     * 现思路：新建一js对象以及新数组，遍历传入数组时，判断值是否为js对象的键，
     * 不是的话给对象新增该键并放入新数组。
     * 注意点：判断是否为js对象键时，会自动对传入的键执行“toString()”，
     * 不同的键可能会被误认为一样，例如n[val]-- n[1]、n["1"]；
     * 解决上述问题还是得调用“indexOf”。
    */
    function removeDuplicates(array) {
        var temp = {},
            arr = [],
            len = array.length, val, type;

        for (var i = 0; i < len; i++) {
            val = array[i];
            type = typeof val;
            if (!temp[val]) {
                temp[val] = [type];
                arr.push(val);
            } else if (temp[val].indexOf(type) < 0) {
                temp[val].push(type);
                arr.push(val);
            }
        }
        return arr;
    }

    var arr = [1,2,"2",4,9,"a","a",2,3,5,6,5];
    console.log(removeDuplicates(arr));
    ```

* 排序后相邻去重法 -- Array.prototype.sort()
    ```js
    /*
     * 给传入数组排序，排序后相同值相邻，
     * 然后遍历时,新数组只加入不与前一值重复的值。
     * 会打乱原来数组的顺序
    */
    function removeDuplicates(array) {
        array.sort();
        var temp=[array[0]];
        for(var i = 1; i < array.length; i++) {
            if( array[i] !== temp[temp.length-1]) {
                temp.push(array[i]);
            }
        }
        return temp;
    }

    var arr = [1,2,"2",4,9,"a","a",2,3,5,6,5];
    console.log(removeDuplicates(arr));
    ```

* 存在唯一性 -- Object.keys()
    ```js
    function removeDuplicates(arr) {
        let temp = [];
        let hash = {};
        for(let i = 0; i < arr.length; i++) {
            if(!hash[JSON.stringify(arr[i])]) {
                hash[JSON.stringify(arr[i])] = true;
                temp.push(arr[i]);
            }
        }
        return temp;
    }

    let arr1 = [{a:1},{b:2},{c:3},{a:1},{d:2}];
    let arr2 = [1,2,3,4,5,6,1,2,3];
    console.log(removeDuplicates(arr1));
    console.log(removeDuplicates(arr2));
    ```

* 数组下标法 -- indexOf
    ```js
    /*
     * 还是得调用“indexOf”性能跟方法1差不多，
     * 实现思路：如果当前数组的第i项在当前数组中第一次出现的位置不是i，
     * 那么表示第i项是重复的，忽略掉。否则存入结果数组。
    */
    function removeDuplicates(array) {
        var temp = [];
        for(var i = 0; i < array.length; i++) {
            //如果当前数组的第i项在当前数组中第一次出现的位置是i，才存入数组；否则代表是重复的
            if(array.indexOf(array[i]) === i) {
                temp.push(array[i]);
            }
        }
        return temp;
    }

    var arr = [1,2,"2",4,9,"a","a",2,3,5,6,5];
    console.log(removeDuplicates(arr));
    ```

* 优化遍历数组法
    思路：获取没重复的最右一值放入新数组

    ```js
    /*
     * 推荐的方法
     * 方法的实现代码相当酷炫，
     * 实现思路：获取没重复的最右一值放入新数组。
     * （检测到有重复值时终止当前循环同时进入顶层循环的下一轮判断）
    */
    function removeDuplicates(array) {
        var temp = [];
        var index = [];
        var length = array.length;
        for(var i = 0; i < length; i++) {
            for(var j = i + 1; j < length; j++) {
                if (array[i] === array[j]) {
                    i++;
                    j = i;
                }
            }
            temp.push(array[i]);
            index.push(i);
        }
        console.log(index);
        return temp;
    }

    var arr = [1,2,2,3,5,3,6,5];
    console.log(removeDuplicates(arr));
    ```

* ES6 -- Set
    ```js
    function removeDuplicates(arr) {
        let temp = new Set(arr);
        return Array.from(temp);
    }
    let arr = [1,2,3,4,5,6,1,2,3];
    console.log(removeDuplicates(arr));
    ```

* 返回去重后的数组长度
    不使用额外的数组空间

    ```js
    var removeDuplicates = function(nums) {
        if (nums && nums.length < 1) return 0;

        var number = 0;
        for(var i = 0; i < nums.length; i++) {
            // 相邻两个值比较，不同才做统计操作
            if (nums[i] != nums[number]) {
            number ++;
            nums[number] = nums[i];
            }
        }
        // 不同数字为总量= number+1
        return ++number;
    };
    console.log(removeDuplicates([1, 2, 2]))
    ```

## 20.两数之和

* 双重遍历法
    ```js
    var twoSum = function(nums, target) {
        let arr = [];
        for(let i = 0; i < nums.length; i++) {
            for(let j = i + 1; j < nums.length; j++) {
                if (nums[i] + nums[j] === target) {
                    arr = [i, j];
                    break;
                }
            }
        }
        return arr;
    }
    ```

* `indexOf` 代替法
    ```js
    var twoSum = function(nums, target) {
        let a, b;
        for(let i = 0; i < nums.length; i++) {
            b = nums.indexOf(target - nums[i]);
            if(b > -1 && b !== i) {
                a = i;
                break;
            }
        }
        return [a, b];
    };
    ```

* 对象索引
    ```js
    var twoSum = function(nums, target) {
        let mapObj = {};
        let res = [];
        nums.forEach((e, i) => mapObj[e] = i);

        for(let i = 0; i < nums.length; i++) {
            let j = mapObj[target - nums[i]];
            if(j && j !== i) {
                res = [i, j];
                break;
            }
        }

        return res;
    }
    ```

* 使用 `Map()`
    ```js
    var twoSum = function(nums, target) {
        let map = new Map();
        let res = [];
        nums.forEach((e, i) => map.set(e, i));

        for (let i = 0; i < nums.length; i++) {
            let j = map.get(target - nums[i]);
            if (j && j !== i) {
                res = [i, j];
                break;
            }
        }

        return res;
    }
    ```

## 21.两数相加

* js 链表
    ```js
    function Node(element) {
        this.element = element;
        this.next = null;
    }

    // 提供对链表的操作方法
    function linkedList() {
        this.head = new Node( 'head' ); // 头节点
        this.find = find; // 查找节点
        this.insert = insert; // 插入节点
        this.remove = remove; // 删除节点
        this.findPrev = findPrev; // 查找前一个节点
        this.display = display; // 显示链表
    }

    // 找到该数据，保存该数据节点
    function find(item) {
        var currNode = this.head;
        while (currNode.element != item) {
            currNode = currNode.next;
        }
        return currNode;
    }

    // 插入一个元素
    function insert(newElement, item) {
        // 当前元素及后面元素
        var newNode = new Node(newElement); // 每次保存在 next 下的数据
        // 保存上一次的 newNode 的数据
        var current = this.find(item);
        newNode.next = current.next;
        current.next = newNode;
    }
    
    // 查找带删除节点的前一个节点
    function findPrev(item) {
        var currNode = this.head;
        while (!(currNode.next == null) && (currNode.next.element != item)) {
            currNode = currNode.next;
        }
        return currNode;
    }
    
    // 删除一个元素
    function remove(item) {
        var prevNode = this.findPrev(item);
        if(!(prevNode.next == null)) {
            prevNode.next = prevNode.next.next;
        }
    }
    
    // 打印链表元素
    function display() {
        var currNode = this.head;
        while (!(currNode.next == null)) {
            console.log(currNode.next.element);
            currNode = currNode.next;
        }
    }


    //测试程序
    var cities = new linkedList();
    cities.insert("Conway", "head");
    cities.insert("Russellville", "Conway");
    cities.insert("Alma", "Russellville");
    cities.insert('Grape', 'Alma');
    cities.display();
    
    cities.remove('Alma');
    cities.display();
    ```

* 两数相加 -- js链表
    ```js
    var addTwoNumbers = function(l1, l2) {
        let dummyHead,carry = 0,curr;
        
        while (l1 !== null || l2 !== null) {
            let num1 = l1 ? l1.val : 0;
            let num2 = l2 ? l2.val : 0;
            let sum = num1 + num2 + carry;
            if(!dummyHead){
                dummyHead = new ListNode(sum % 10);
                curr = dummyHead;
            }else{
                curr.next = new ListNode(sum % 10);
                curr = curr.next;
            }
            carry = sum > 9 ? 1 : 0;
            l1 = l1 ? l1.next : l1;
            l2 = l2 ? l2.next : l2;
        }

        if (carry > 0) {
            curr.next = new ListNode(1);
        }

        return dummyHead;
    }
    ```

## 22.无重复字符的最长子串
    ```js
    var lengthOfLongestSubstring = function(s) {
        var res = 0; // 用于存放当前最长无重复子串的长度
        var str = ""; // 用于存放无重复子串
        var len = s.length;
        for(var i = 0; i < len; i++) {
            var char = s.charAt(i);
            var index = str.indexOf(char);
            if(index === -1) {
                str += char;
                res = res < str.length ? str.length : res;
            } else {
                str = str.substr(index + 1) + char;
            }
        }
        return res;
    };
    lengthOfLongestSubstring("pwwkew"); // 3 kew
    ```

## 23.找出两个数组中相同或者不同的元素，返回一个数组

* `concat` & `filter`

```js
let arr1 = [44, 6, 9, 88, 2, 3, 5],
    arr2 = [9, 3, 44];
console.time();
// 并集
let union = [...new Set([...arr1, ...arr2])];
// 交集
let obj = arr1.concat(arr2).filter(function(v, i, arr) {
    return arr.indexOf(v) !== arr.lastIndexOf(v);
});
let intersect = [...new Set(obj)];
// 差集
let difference = arr1.concat(arr2).filter(function(v, i, arr) {
    return arr.indexOf(v) === arr.lastIndexOf(v);
});
console.log(union, intersect, difference);
console.timeEnd();
```

* `set` & `filter`

```js
let a = new Set([44, 6, 9, 88, 2, 3, 5]);
let b = new Set([9, 3, 44]);
console.time();
// 并集
let union = [...new Set([...a,...b])];
// 交集
let intersect = [...new Set([...a].filter(x=> b.has(x)))];
// 差集
let difference = [...new Set([...a].filter(x=> !b.has(x)))];
console.log(union, intersect, difference);
console.timeEnd();
```

注意：数组 a 中的元素必须有值。

* `reduce` 同时实现  `map` 和 `filter`

```js
const numbers = [10, 20, 30, 40];
const doubledOver50 = numbers.reduce((finalList, num) => {
	console.log('finalList:', finalList, num)
    num = num * 2;
    if (num > 50) {
    	finalList.push(num);
    }
    return finalList;
}, []);
```

并、交、差集
```js
let a = new Set([44, 6, 9, 88, 2, 3, 5]);
let b = new Set([9, 3, 44]);

// 并集
let union = [...new Set([...a,...b])];
// 交集
let intersect = [...new Set([...a])].reduce((finalList, num) => {
    if (b.has(num)) finalList.push(num);
    return finalList;
}, []);
// 差集
let difference = [...new Set([...a])].reduce((finalList, num) => {
    if (!b.has(num)) finalList.push(num);
    return finalList;
}, []);
```

注意：与 `set & filter` 方法类似，数组 a 必须有值

`reduce` 统计数组中相同项的个数
```js
let cars = ['BMW', 'Benz', 'Benz', 'Tesla', 'BMW', 'Toyota'];
let carsObj = cars.reduce(function (obj, name) {
    obj[name] = obj[name] ? ++obj[name] : 1;
    return obj;
}, {});
carsObj; // => { BMW: 2, Benz: 2, Tesla: 1, Toyota: 1 }
```

## 24. 将字符串中指定元素位置后的第一个元素的大写字母替换成小写字母

```js
let str = '11143fdg_ASdd';
let arr = str.split('');
let sym = '_';
arr.forEach((item, i) => {
	if (item === sym) {
		let pos = i + 1;
		let _substr = str.substring(i, pos+1);
		if (new RegExp(`(${sym}([A-Z]))`).test(_substr)){
            let rep = str.substring(pos, pos+1).toLowerCase();
            // str = str.substr(0, pos) + rep + str.substring(pos+1, str.length); // 保留该元素
            str = str.substr(0, i) + rep + str.substring(pos+1, str.length); // 删除该元素
		}
	}
});

console.log(str, arr);
```
