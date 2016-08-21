## Profile Object

> 一个典型的用户简历对象

```json
{
  "id": 1,
  "description": "Hello World",
  "user": {
    // ...
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

Parameter                 | Type      | Description
------------------------- | --------- | -----------
`id`                      | `Integer` | 简历对象在数据库中的唯一ID
`descriptiion`            | `String`  | 简历对象中的`自我介绍`
`user`                    | `User`    | 该简历对象对应的`用户`[对象](#user-object)
`languages`               | `Array`   | 简历中包含的`语言`对象数组
`education_experiences`   | `Array`   | 简历中包含的`教育经历`对象数组
`working_experiences`     | `Array`   | 简历中包含的`工作经历`对象数组
`certifications`          | `Array`   | 简历中包含的`证书`对象数组
`contact_information`     | `ContactInformation` | 简历的`联系方式`对象


### Get User Profile

```shell
curl https://askiapi.herokuapp.com/api/users/4/profile \
  -H 'Content-Type: application/json; charset=utf-8' \
  -X GET \
  -i
```

> 返回结果，与`Profile`对象内容一致

获取用户的简介内容，无需权限验证

#### HTTP Request

`GET https://askiapi.herokuapp.com/api/users/:user_id/profile`

### Update User Profile

更新用户的简介内容，需要对应用户的权限认证才能进行更新。更新操作对于`Profile`对象的几个组合类而言，需要使用特定的CRUD接口进行操作。每一个组合类都有一套完整的REST API接口，下面以`Language`为例进行介绍。

<aside class="warning">
对于<code>CREATE</code>，<code>UPDATE</code>与<code>DESTROY</code>操作，都需要在HTTP请求头中加入验证Token。
</aside>

