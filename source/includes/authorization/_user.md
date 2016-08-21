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
  "avatar": { 
    "url": null
  },
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
avatar           | `Avatar`  | 用户的`头像对象`
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
    "avatar": { 
      "url": null
    },
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
    "avatar": { 
      "url": null
    },
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