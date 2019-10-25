# 创建/发布/撤销 npm 包

## 提供node环境

* node官网下载安装node
   网址： https://nodejs.org/zh-cn/ 

## 注册登录npm官网
   该npm账号后面会用到

* npm网站地址

   https://www.npmjs.com/

* npm网站注册地址

   https://www.npmjs.com/signup

## 注册完毕后，登录

* Windows直接cmd到命令行

   npm login

* 输入用户名、密码、邮箱

## 创建文件夹，下载配置文件

* 创建文件夹

   mkdir testXXX

* 下载配置文件

   npm init

* 配置完成创建index.js文件，输入代码

   cd.>index.js

   module.exports = 123456789;

## 保存完毕，发布npm包
   npm publish testXXX

## 本地下载npm包
   npm install testXXX

## 删除发布的npm测试包
   npm --force unpublish testXXX