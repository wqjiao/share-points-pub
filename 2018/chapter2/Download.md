# 下载压缩包

## 请求多个接口

    ```
    const promist1 = new Promise((resolve, reject) => {
        $.ax({
            url: url + 'getPdf',
            type: 'get',
            data: {},
            dataType: 'json',
            success: function(res) {
                window.open(API_BASE_URL + 'loan/orders?excel=1');
                resolve(res.data)
            }
        })
    })
    const promist2 = new Promise((resolve, reject) => {
        $.ax({
            url: url + 'getVideo',
            type: 'get',
            data: {},
            dataType: 'json',
            success: function(res) {
                window.open(API_BASE_URL + 'loan/orders?excel=2');
                resolve(res.data)
            }
        })
    });
    const promist3 = new Promise((resolve, reject) => {
        $.ax({
            url: url + 'getImage',
            type: 'get',
            data: {},
            dataType: 'json',
            success: function(res) {
                window.open(API_BASE_URL + 'loan/orders?excel=3');
                resolve(res.data)
            }
        })
    });
    Promise.all([promist1, promist2, promist3]).then((resultList) => {
        console.log('results:', resultList)
    })
    ```

## 请求接口，直接将多个文件打成一个压缩包
    ```
    let url = 'https://www.easy-mock.com/mock/5bdace5449b3f05c44e33cf8/mockurl/';
        
    $.ax({
        url: url + 'getPdf',
        type: 'get',
        data: {},
        dataType: 'json',
        success: function(res) {
            if (res.success) {
                // window.open(res.data); // 1、出现浏览器拦截情况

                // 2、js 创建 a 标签，实现点击操作
                var a = document.createElement('a');
                var url = API_BASE_URL + 'loan/orders?excel=1';
                var filename = 'data.xlsx';
                a.href = url;
                a.target = '_blank';
                a.download = filename;
                a.click();

                // 3、接口直接返回二进制文件流，浏览器自行下载
            } else {
                notification.error({
                    message: '请求失败',
                    description: '下载失败，请重新下载'
                });
            }
        },
        error: function() {
            notification.error({
                message: '请求失败',
                description: '数据请求失败，请重试！'
            });
        }
    });
    ```