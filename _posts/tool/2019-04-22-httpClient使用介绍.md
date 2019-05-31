---
layout: post
title:  "Editor REST Client 使用教程"
date:   2019-04-22 14:15:48 +0800
categories: Tool
tags: tool
excerpt: 介绍了一些 Editor REST Client 的使用教程
---
<!-- TOC -->

- [制作一个http请求](#制作一个http请求)
    - [请求行](#请求行)
    - [请求头](#请求头)
    - [请求体](#请求体)
- [变量](#变量)
    - [自定义变量](#自定义变量)
        - [环境变量](#环境变量)
        - [文件变量](#文件变量)
        - [请求变量](#请求变量)
    - [系统变量](#系统变量)
- [系统设置](#系统设置)
- [rest editor 和 postman对比](#rest-editor-和-postman对比)
        - [安装和启动](#安装和启动)
        - [操做使用](#操做使用)
        - [学习成本](#学习成本)
        - [团队协作](#团队协作)
- [其他](#其他)
    - [制作curl](#制作curl)
    - [快捷键的使用](#快捷键的使用)
    - [保存响应](#保存响应)
    - [生成原始的请求代码段](#生成原始的请求代码段)
- [相关资料](#相关资料)

<!-- /TOC -->

### 制作一个http请求

#### 请求行

```
GET https://example.com/comments/1 HTTP/1.1

POST https://example.com/comments/1

https://example.com/comments/1
```

如果省略`request`方法，请求将被视为`GET`

```
GET https://example.com/comments?page=2&pageSize=10
```
有时在单个请求中可能有多个查询参数，将所有查询参数放在请求行中很难阅读和修改。所以我们允许你将查询参数传播到多行（一行一个查询参数），我们将在请求行开始后立即解析行，?并且&像
```
GET https://example.com/comments
    ?page=2
    &pageSize=10
```
#### 请求头

紧接在第一个空行的请求行之后的行被解析为请求标题。请提供标准`field-name: field-value`格式的标题，
  每行代表一个标题。默认情况下，如果未明确指定，则会在请求中`REST Client Extension`
    添加`User-Agent`带有值的标头`vscode-restclient`。
    您还可以在设置中更改默认值`rest-client.defaultHeaders`。以下是请求标头的示例：
```
    User-Agent: rest-client
    Accept-Language: en-GB,en-US;q=0.8,en;q=0.6,zh-CN;q=0.4
    Content-Type: application/json
```


#### 请求体
如果你想提供请求主体，请添加请求行和请求头后，一个空行，之后它会被视为所有内容的请求体。以下是请求正文的示例：
```
POST https://example.com/comments HTTP/1.1
Content-Type: application/xml
Authorization: token xxx

<request>
    <name>sample</name>
    <time>Wed, 21 Oct 2015 18:27:50 GMT</time>
</request>
```

如果请求体内容很大，可以将请求体写在文件中，然后引用这个文件
示例
```
POST http://dev.avatarinternalapi.jd.com/alarm/alarm-info
Content-Type: {{contentType}}

< ./alarm.json
```


### 变量

#### 自定义变量
     自定义变量包含 环境变量  文件变量  请求变量

##### 环境变量
不同环境下可以切换使用不同的变量

在哪定义：设置->工作区设置
```json
{"rest-client.environmentVariables": {
    "$shared": {  //共享变量
        "version": "v1"
    },
    "local": {   //本地变量
        "version": "v2",
        "host": "dev.avatarinternalapi.jd.com",
        "token": "test token"
    },
    "production": {
        "host": "example.com",
        "token": "product token"
    }
}}
```
当`production` 中没有`version`时，调用`production`环境时，回去找`$shared` 中的`version`

##### 文件变量
- 对于文件变量，定义遵循`@variableName = variableValue`占用完整行的语法。
- 变量名称不得包含任何空格。至于变量值，它可以由任何字符组成，甚至允许空格（前导和尾随空格将被剥离）。
- 如果你想保留一些像换行符这样的特殊字符，你可以使用反斜杠 `\n`
- 无论您在何处定义文件变量`http`文件，它们可以在整个文件的任何请求中引用

```
@host = dev.avatarinternalapi.jd.com
@contentType = application/json

GET  http://{{host}}/alarm/email-info?pin=zhangkaixuan100,zhangkaixuan1002

Content-Type: {{contentType}}
```

##### 请求变量
求变量的定义语法就像单行注释一样，在所需的请求`URL`之前 `# @name newname`

使用场景:当一个请求需要其他请求的值，作为请求的参数时

请求变量引用语法如下`{{requestName.(response|request).(body|headers).(JSONPath|XPath|Header Name)}}`。
您有两个参考部分选择响应或请求：正文和标题。对于正文部分，它只适用于`JSON`和`XML`响应，您可以使用`JSONPath`和`XPath`
来提取特定的属性或属性。例如，如果`JSON`响应返回`body {"id": "mock"}`，
则可以将`JSONPath`部分设置`$.id`为引用`id`。

示例：
```
###

# @name createComment
POST {{baseUrl}}/comments HTTP/1.1
Content-Type: application/json

###

# @name getCreatedComment

GET {{baseUrl}}/comments/{{createComment.response.body.$.id}} HTTP/1.1

Authorization: {{login.response.headers.X-AuthToken}}
```

#### 系统变量
系统自带的一些变量，使用系统变量需要有 `$`符号
- `{{$guid}}` 唯一识别号
- `{{$randomInt min max}}` 返回一个`min` 和 `max` 之间的随机数
- `{{$timestamp [offset option]}}`：添加`UTC`时间戳。
- `{{$timestamp number option}}`，例如3小时前`{{$timestamp -3 h}}`;代表后天`{{$timestamp 2 d}}`。

更多系统变量用法请参考 [官方文档](https://marketplace.visualstudio.com/items?itemName=humao.rest-client#overview)

### 系统设置
可以用户设置中，覆盖系统设置

示例: `"editor.fontSize": 13`, 将系统默认的字体大小改为13

更多用户设置用法请参考 [官方文档](https://marketplace.visualstudio.com/items?itemName=humao.rest-client#overview)
###  rest editor 和 postman对比
##### 安装和启动
- `postman` 属于一个`pc`端的应用，需要下载和安装，每次使用需要启动应用
- `rest editor` 属于一个插件，高版本的`phpstorm`自带，可以在`phpstom`中直接使用，无需单独启动新应用
- 结论：在安装和启动上，`rest editor` 更加快捷

##### 操做使用

- `postman` 有功能强大的操做界面，使用起来方便，支持全局变量的定义。对于一个`http`请求中所需的所有参数，几乎都可以在界面中定义。
- `rest editor` 没有操做界面，需要定义一个`.http`后缀的文件，在文件中编写请求。一个`http`请求的所需参数，都需要手动编写，一般编辑器会有参数联想功能。
- 结论: 在编写一个完整的`http`请求的过程中，`postman`显得更加方便一点；二者都可以保存编写的`http`请求

##### 学习成本
- 结论: 相比而言`rest editor` 学习成本更高，应为是纯手动编写http请求，需要学习`rest editor`的`http`请求的编写规则，并且需要熟练掌握`http`协议。

##### 团队协作
- `postman:` 与他人共享编写过的的http请求需要付费
- `rest editor` 可以将编写的文件同步到代码仓库，实现团队共享
- 结论: `rest editor` 更甚一筹

### 其他
#### 制作curl
鼠标放置到请求上，邮件单击选择`Copy Request As cURL`

```http
curl --request POST --url http://dev.avatarinternalapi.jd.com/alarm/alarm-info --header 'content-type: application/json' --data '< ./alarm.json'
```

#### 快捷键的使用
- `Ctrl+Alt+L（Cmd+Alt+L对于macOS）` 重新重新运行上一个请求
- `Ctrl+Alt+K（Cmd+Alt+K对于macOS）` 停止请求
- `Ctrl+Alt+E（Cmd+Alt+E对于macOS）` 选择一个环境

更多快捷键用法请参考 [官方文档](https://marketplace.visualstudio.com/items?itemName=humao.rest-client#overview)

#### 保存响应

保存完整的响应

左上角保存的图表

保存响应正文

左上角保存正文图表

#### 生成原始的请求代码段

选中一次请求的所有代码后 ->点击右键 -> 选择Generate Code Sinppet ->选择你想要的语言

### 相关资料
[官方文档](https://marketplace.visualstudio.com/items?itemName=humao.rest-client#overview)

[`Visual Studio Code`下载](https://code.visualstudio.com/download)