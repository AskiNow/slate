---
title: API Reference

language_tabs:
  - shell
  - javascript

includes:
  - errors

search: true
---

# Introduction

这是AskiNow的后台API文档，一共分为如下几个模块：

1. 权限验证与用户管理（已完成）
2. 用户简历与成为教师流程
3. 订单管理
4. 课程管理
5. 支付系统

# Authorization

## Introduction

权限验证机制简介

基于Authorization Token的机制进行权限验证，使用HTTP头来进行Token和其他验证信息的传递。用于权限验证的headers主要有：

1. `access-token`: Token字符串
2. `client`： 对客户端使用的唯一标记，字符串
3. `uid`： 当前用户的唯一标识，对于使用`email`注册的用户而言，`uid`就是邮箱；使用其他OAuth2创建的用户，`uid`由这些第三方服务指定；该值的作用是令后台可以判断当前请求对应的用户对象

用户管理模块中，`登陆`，`注册`以及`忘记密码`的功能都是基于权限验证机制实现的，其他模块中涉及到用户数据的修改与验证时，也是基于上述权限验证机制进行验证。

本模块中涉及到的全部API在下方列出, 所有的数据传输都是通过JSON格式。

## User Object

用户对象，是使用JSON格式描述的用户信息，在各种API的返回值中都会出现，以`User`作为类型名称。

> 一个简单的用户对象

```json
{
  "id": 4,
  "provider": "email",
  "uid": "alistair.zhao@askinow.com",
  "name": null,
  "nickname": null,
  "image": null,
  "email": "alistair.zhao@askinow.com",
  "created_at": "2016-07-30T09:49:04.191Z",
  "updated_at": "2016-07-30T09:49:04.191Z"
}
```

Parameter        | Type      | Description
---------------- | --------- | -----------
id               | `Integer` | 用户的唯一ID
provider         | `String`  | 用户的"提供方"，一般邮箱注册的用户为`email`
uid              | `String`  | 对于不同的`provider`下用户的`id`，一般为用户的邮箱地址
name             | `String`  | 用户的名称
nickname         | `String`  | 用户的昵称，`当前版本不使用`
image            | `String`  | 用户的头像URL
email            | `String`  | 用户的注册邮箱
created_at       | `Date`    | 用户创建入库的日期
updated_at       | `Date`    | 用户上次更新的日期

## Sign Up

```shell
curl "https://askiapi.herokuapp.com/api/auth.json" \
  -H "Content-Type: application/json; charset=utf-8" \
  -d '{ "email": "alistair.zhao@askinow.com", 
        "password": "foobar123", 
        "password_confirmation": "foobar123" }' \
  -X POST \
  -i 
```

```javascript

```

> 返回的成功结果，HTTP code为200

```json
{
  "status": "success", 
  "data": 
  {
    "id": 4,
    "provider": "email",
    "uid": "alistair.zhao@askinow.com",
    "name": null,
    "nickname": null,
    "image": null,
    "email": "alistair.zhao@askinow.com",
    "created_at": "2016-07-30T09:49:04.191Z",
    "updated_at": "2016-07-30T09:49:04.191Z"
  }
}
```

> 返回的失败结果，HTTP code为422，本例中因为`alistair.zhao@askinow.com`已经用于创建用户，因此产生了在`email`域上的错误

```json
{
  "status": "error",
  "data": 
  {
    "id": null,
    "provider": "email",
    "uid": "",
    "name": null,
    "nickname": null,
    "image": null,
    "email": "alistair.zhao@askinow.com",
    "created_at": null,
    "updated_at": null
  },
  "errors": 
  {
    "email": ["already in use"],
    "full_messages": ["Email already in use"]
  }
}
```

注册一个新用户

### HTTP Request

`POST https://askiapi.herokuapp.com/api/auth`

### POST Parameters

Parameter             | Required | Description
--------------------- | -------  | -----------
email                 | true     | 用户的注册邮箱，该值是唯一的
password              | true     | 用户的密码
password_confirmation | true     | 密码确认
name                  | false    | 用户的名字，即full name

### Success response

Parameter             | Type      | Description
--------------------- | --------- | -----------
status                | `String`  | 如果注册成功，则该值为`success`；否则为`error`
data                  | `User`    | 创建的用户对象

### Error response

Parameter             | Type      | Description
--------------------- | --------- | -----------
status                | `String`  | 如果注册成功，则该值为`success`；否则为`error`
data                  | `User`    | 用户创建用户的参数
errors                | `Error`   | 包含每个域的错误列表以及所有错误信息的完整列表

## Sign in

```shell
curl "https://askiapi.herokuapp.com/api/auth/sign_in" \
  -H "Content-Type: application/json; charset=utf-8" \
  -d '{ "email": "vincent.zhao@askinow.com", "password": "foobar123" }' \
  -X POST \
  -i
```

> 返回的成功结果，尤其注意HTTP headers的内容

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Access-Token: 2qQA9BIYAklNUZ_fJ9YIQg
Token-Type: Bearer
Client: ZtHRYmqSttQZp68POuoxKw
Expiry: 1471086719
Uid: vincent.zhao@askinow.com
```

```json
{
  "data":
  {
    "id": 2,
    "email": "vincent.zhao@askinow.com",
    "provider": "email",
    "uid": "vincent.zhao@askinow.com",
    "name": null,
    "nickname": null,
    "image": null
  }
}
```

> 登陆失败结果

```json
{ "errors":["Invalid login credentials. Please try again."] }
```

用户登录，使用`email`与`password`进行登录，返回结果包含用户对象和header中的权限验证信息。

### HTTP Request

`POST https://askiapi.herokuapp.com/api/auth/sign_in`

### POST Parameters

Parameter             | Required | Description
--------------------- | -------  | -----------
email                 | true     | 用户的注册邮箱，该值是唯一的
password              | true     | 用户的密码

### Success response

`Body`

Parameter             | Type      | Description
--------------------- | --------- | -----------
data                  | `User`    | 用户对象

`Header`

<aside class="info">
如下三个headers在后续的请求中（需要权限验证）都需要加上
</aside>

Header       | Description
------------ | ----------
Access-Token | 权限验证Token
Client       | 发起请求的客户端标识字符串
Uid          | 用户的唯一ID  

### Error response

Parameter             | Type      | Description
--------------------- | --------- | -----------
errors                | `Array`   | 登陆错误信息字符串列表

# Profile

用户简介信息：用户简介主要包含如下几个部分

1. `语言`：用户的主要使用语言，包含语言的`名称`和使用的`熟练程度`；
2. `教育经历`：包含`学校名称`，`开始和结束日期`，`学位`，`简短描述`，以及`专业`；
3. `工作经历`：包含`公司名称`，`开始和结束日期`，`简短描述`，以及`职位`；
4. `证书`：`名称`，`颁发机构`，`日期`和`简短描述`；
5. `联系信息`：`电话号码`和`邮箱地址`；

此外，用户简介本身还包含用户的`个人描述`。具体的字段信息如下所述：

## Profile Object

> 一个典型的用户简历对象

```json
{
  "id": 1,
  "description": "Hello World",
  "user": {
    "id": 1,
    "provider": "email",
    "uid": "vincent.zhao@askinow.com",
    "name": null,
    "nickname": null,
    "image": null,
    "email": "vincent.zhao@askinow.com",
    "created_at": "2016-07-31T03:50:37.789Z",
    "updated_at": "2016-08-12T14:21:04.437Z"
  },
  "languages": [
    // ...
  ],
  "education_experiences": [
    // ...
  ],
  "working_experiences": [
    // ...
  ],
  "certifications": [
    // ...
  ],
  "contact_information": {
    // ...
  }
}
```

Parameter        | Type      | Description
---------------- | --------- | -----------
`id`             | `Integer` | 简历对象在数据库中的唯一ID
`descriptiion`   | `String`  | 简历对象中的`自我介绍`
`user`           | `User`    | 该简历对象对应的`用户`[对象](#user-object)
`created_at`     | `Date`    | 语言创建入库的日期
`updated_at`     | `Date`    | 语言上次更新的日期


### Language Object

> 一个语言对象

```json
{
  "id": 1,
  "name": "chinese",
  "profile_id": 1,
  "proficiency": "native_or_bilingual",
  "created_at": "2016-08-13T08:08:53.750Z",
  "updated_at": "2016-08-13T08:45:50.469Z"
}
```

Parameter        | Type      | Description
---------------- | --------- | -----------
`id`             | `Integer` | 语言对象在数据库中的唯一ID
`profile_id`     | `Integer` | 关联的profile对象的ID（不建议使用，后续可能会不显示）
`name`           | `String`  | 语言的`名称`，全部小写
`proficiency`    | `Enum`    | 语言的`熟练程度`，是只有三个值的枚举对象： `elementary`（基础水平），`professional`（专业），`native_or_bilingual`（母语或者第二语言）
`created_at`     | `Date`    | 语言创建入库的日期
`updated_at`     | `Date`    | 语言上次更新的日期

### Education Experience Object

> 一个用户教育经历对象

```json
{
  "id": 2,
  "school_name": "Peking University",
  "degree": "Bachelor of Science",
  "field_of_study": "Computer Science and Technology",
  "started_at": "2012-09-01",
  "ended_at": "2016-07-01",
  "description": "During my undergraduate study at Peking University, I ...",
  "profile_id": 1,
  "created_at": "2016-08-13T09:20:16.161Z",
  "updated_at": "2016-08-13T09:20:27.095Z"
}
```

Parameter         | Type      | Description
----------------- | --------- | -----------
`id`              | `Integer` | 教育经历对象在数据表中的唯一ID
`profile_id`      | `Integer` | 关联的profile对象的ID（不建议使用，后续可能会不显示）
`school_name`     | `String`  | 学校名称
`degree`          | `String`  | 学位名称
`field_of_study`  | `String`  | 专业名称
`started_at`      | `Date`    | 教育经历的开始日期
`ended_at`        | `Date`    | 教育经历的结束日期
`description`     | `String`  | 用户对教育经历的描述
`created_at`      | `Date`    | 教育经历创建入库的时间戳
`updated_at`      | `Date`    | 教育经历上次更新的时间戳

### Working Experience Object

> 一个用户工作经历对象

```json
{
  "id": 1,
  "company_name": "Sophisters Ltd",
  "position": "CTO",
  "started_at": "2015-08-01",
  "ended_at": null,
  "description": "Amazing",
  "profile_id": 1,
  "created_at": "2016-08-13T14:12:32.492Z",
  "updated_at": "2016-08-13T14:12:32.492Z"
}
```

与`教育经历`相同的字段解释意义相同，其他字段的含义：

Parameter         | Type      | Description
----------------- | --------- | -----------
`company_name`    | `String`  | 公司名称
`position`        | `String`  | 职位

### Certification Object

> 用户获得的证书对象

```json
{
  "id": 2,
  "name": "IELTS",
  "authority": "British Council",
  "issued_at": "2015-10-24",
  "description": "Total score 7.5, Yeah!",
  "profile_id": 1,
  "created_at": "2016-08-13T13:41:54.076Z",
  "updated_at": "2016-08-13T13:41:54.076Z"
}
```

Parameter         | Type      | Description
----------------- | --------- | -----------
`name`            | `String`  | 证书的名称
`authority`       | `String`  | 证书的颁发机构
`issued_at`       | `Date`    | 颁发日期

### Contact Information

> 用户的联系信息对象

```json
{
  "id": 3,
  "phone_number": "+86 18638670575",
  "email": "vincent.zhao@askinow.com",
  "profile_id": 1,
  "created_at": "2016-08-13T14:29:00.035Z",
  "updated_at": "2016-08-13T14:29:00.035Z"
}
```

Parameter         | Type      | Description
----------------- | --------- | -----------
`phone_number`    | `String`  | 电话号码
`email`           | `String`  | 联系邮箱

```shell
curl https://askiapi.herokuapp.com/api/users/4/profile \
  -H 'Content-Type: application/json; charset=utf-8' \
  -X GET \
  -i
```

## Get User Profile

获取用户的简介内容，无需权限验证

### HTTP Request

`GET https://askiapi.herokuapp.com/api/users/:user_id/profile`


```shell
curl https://askiapi.herokuapp.com/api/users/2/profile \
  -H 'Content-Type: application/json; charset=utf-8' \
  -H 'Access-Token: tuBXy8nxftkF-l1kswXQzQ' \
  -H 'Client: zH4mamOX525ubYSkM0B5sw' \
  -H 'Uid: vincent.zhao@askinow.com' \
  -d '{ "description": "This is a new description", 
        "languages": [ 
          { "name": "Chinese", "proficiency": "native_or_bilingual" }
         ] }' \
  -X PUT \
  -i
```

## Update User Profile

更新用户的简介内容，需要对应用户的权限认证才能进行更新

### HTTP Request

`PUT/PATCH https://askiapi.herokuapp.com/api/users/:user_id/profile`

