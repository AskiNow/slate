## Language Object

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
`name`           | `String`  | 语言的`名称`，在后台保存是全部小写，前端显示可以首字母大写
`proficiency`    | `Enum`    | 语言的`熟练程度`，是只有三个值的枚举对象： `elementary`（基础水平），`professional`（专业），`native_or_bilingual`（母语或者第二语言）
`created_at`     | `Date`    | 语言创建入库的日期
`updated_at`     | `Date`    | 语言上次更新的日期

### Get User Profile Language

```shell
curl https://askiapi.herokuapp.com/api/users/7/profile/languages/1 \
  -H 'Content-Type: application/json; charset=utf-8' \
  -X GET \
  -i
```

> 返回结果

```json
{
  "id": 1,
  "name": "chinese",
  "profile_id": 4,
  "proficiency": "native_or_bilingual",
  "created_at": "2016-08-14T12:59:06.790Z",
  "updated_at": "2016-08-14T12:59:06.790Z"
}
```

获取用户简介中的`语言`对象

### List User Profile Languages

```shell
curl https://askiapi.herokuapp.com/api/users/7/profile/languages \
  -H 'Content-Type: application/json; charset=utf-8' \
  -X GET \
  -i
```
> 返回结果

```json
[
  {
    "id": 1,
    "name": "chinese",
    "profile_id": 4,
    "proficiency": "native_or_bilingual",
    "created_at": "2016-08-14T12:59:06.790Z",
    "updated_at": "2016-08-14T12:59:06.790Z"
  }
]
```

获取用户简介中的全部`语言`对象


### Create User Profile Language

```shell
curl "https://askiapi.herokuapp.com/api/users/7/profile/languages" \
  -H "Content-Type: application/json; charset=utf-8" \
  -H "Access-Token: Lof2G_3B9A6HXPlpTGw6ig" \
  -H "Client: srAnFc2OsPfnAEF5H-rmmA" \
  -H "Uid: vincent.zhao@askinow.com" \
  -d '{ "name": "English", "proficiency": "professional" }' \
  -X POST \
  -i
```

> 返回结果为刚创建的`语言`对象

```json
{
  "id": 2,
  "name": "english",
  "profile_id": 4,
  "proficiency": "professional",
  "created_at": "2016-08-14T13:04:28.784Z",
  "updated_at": "2016-08-14T13:04:28.784Z"
}
```

当用户想要添加一门新的语言在自己的简介中时，使用该接口。**注意要添加验证信息**，只有用户本人才能创建。

#### HTTP Request

`POST https://askiapi.herokuapp.com/api/users/:user_id/profile/languages`

#### POST Parameters

Parameter             | Required | Description
--------------------- | -------  | -----------
`name`                | true     | 语言的名称，比如`Chinese`，`Japanese`等等，在后台统一转换为小写
`proficiency`         | true     | 语言的熟练度，取值范围如上所述

<aside class="warning">
熟练度的取值一定要在上述的三个枚举值之间，否则会触发500错误（该异常暂时未处理）。
</aside>

> 错误情况1：因为`name`发生了重复，每个用户的`语言`名称是唯一的。

```plaintext
HTTP/1.1 422 Unprocessable Entity
```

```json
{ "errors": { "name": ["has already been taken"] } }
```

> 错误情况2：验证信息失效

```plaintext
HTTP/1.1 401 Unauthorized
```

```json
{ "errors": ["Authorized users only."] }
```

### Update User Profile Language

```shell
curl "https://askiapi.herokuapp.com/api/users/7/profile/languages/2" \
  -H "Content-Type: application/json; charset=utf-8" \
  -H "Access-Token: Lof2G_3B9A6HXPlpTGw6ig" \
  -H "Client: srAnFc2OsPfnAEF5H-rmmA" \
  -H "Uid: vincent.zhao@askinow.com" \
  -d '{ "name": "English", "proficiency": "professional" }' \
  -X PUT \
  -i
```

> 返回信息，修改过的语言对象

```plaintext
HTTP/1.1 200 OK
```

```json
{ 
  "profile_id": 4,
  "id": 2,
  "name": "english",
  "proficiency": "professional",
  "created_at": "2016-08-14T13:04:28.784Z",
  "updated_at": "2016-08-14T13:04:28.784Z"
}
```

更新操作与创建操作使用的参数基本一致，除了URL中要指定`language_id`以外。注意验证信息也要加上。

#### HTTP Request

`PUT/PATCH https://askiapi.herokuapp.com/api/users/:user_id/profile/languages/:id`

#### POST Parameters

Parameter             | Required | Description
--------------------- | -------  | -----------
`name`                | true     | 语言的名称，比如`Chinese`，`Japanese`等等，在后台统一转换为小写
`proficiency`         | true     | 语言的熟练度，取值范围如上所述

<aside class="warning">
熟练度的取值一定要在上述的三个枚举值之间，否则会触发500错误（该异常暂时未处理）。
</aside>

### Delete User Profile Language

```shell
curl "https://askiapi.herokuapp.com/api/users/7/profile/languages/2" \
  -H "Content-Type: application/json; charset=utf-8" \
  -H "Access-Token: Lof2G_3B9A6HXPlpTGw6ig" \
  -H "Client: srAnFc2OsPfnAEF5H-rmmA" \
  -H "Uid: vincent.zhao@askinow.com" \
  -X DELETE \
  -i
```

> 当成功执行之后，返回信息为空，只有`204`的状态码

```plaintext
HTTP/1.1 204 No Content
```

`DELETE https://askiapi.herokuapp.com/api/users/:user_id/profile/languages/:id`