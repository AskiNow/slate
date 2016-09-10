## Profile Object

> 一个典型的用户简历对象

```json
{
  "id": 1,
  "description": "Hello World",
  "name": "Vincent Zhao",
  "nickname": "kumasento",
  "avatar": {
    "url": "..."
  },
  "title": "mr",
  "birthday": "1994-03-03",
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
`name`                    | `String`  | 简历对象中的`用户姓名`
`nickname`                | `String`  | 简历对象中的`用户昵称`
`avatar`                  | `Object`  | 简历对象中的`用户头像`
`title`                   | `Enum`    | 简历对象中的`用户头衔`
`birthday`                | `Enum`    | 简历对象中的`用户生日`
`languages`               | `Array`   | 简历中包含的`语言`对象数组
`education_experiences`   | `Array`   | 简历中包含的`教育经历`对象数组
`working_experiences`     | `Array`   | 简历中包含的`工作经历`对象数组
`certifications`          | `Array`   | 简历中包含的`证书`对象数组
`contact_information`     | `ContactInformation` | 简历的`联系方式`对象

有几个注意要点：

1. `avatar` 即用户头像，请参考`User`对象中相关的描述来使用
2. `name`，`nickname`和`avatar`都与`User`对象中对应的域相同，在之后的版本中，`User`的对应域会放弃，只使用`Profile`中的域。
3. `title`的枚举值有：`mr`，`mrs`，`ms`，`miss`，`dr`，前端展示时记得首字母大写，末尾增加`.`
4. `languages`往后的几个域，在更新的时候作为参数名称时都要增加`_attributes`后缀
5. 与日期相关的参数，在前端使用时如果遇到问题，可以尝试将自身作为参数创建 JavaScript 中的 `Date` 对象


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

```shell
curl https://askiapi.herokuapp.com/api/users/4/profile \
  -H 'Content-Type: application/json; charset=utf-8' \
  -X PUT \
  -d '{
    "name": "Vincent Zhao",
    "languages_attributes": [
    { "id": 1, "name": "Chinese", "proficiency": "native_or_bilingual" },
    { "id": 2, "_destroy": 1 },
    { "name": "English", "proficiency": "professional" }  
    ]
  }' \
  -i
```

更新用户的简介内容，需要对应用户的权限认证才能进行更新。本接口不只可以更新简介类自身的属性，还可以对其组合类进行 CRUD 操作，具体操作方式为：

1. 对简介类自身属性，直接传参数即可；
2. 创建组合类的对象，传递不带`id`的参数
3. 修改组合类的对象，需要在参数中增加`id`域，指向要更新的目标
4. 删除组合类的对象，需要在参数中增加`_destroy`域，并将其值设置为1

返回结果是更新后的用户简介对象。

> 如果需要同时更新avatar，则调用方式与`User` 类中对头像更新的讲解基本一致，只需要修改除了头像参数以外的值即可  

